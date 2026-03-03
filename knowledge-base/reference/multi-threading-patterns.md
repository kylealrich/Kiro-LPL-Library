# Multi-Threading Patterns in LPL

## Overview

LPL supports multi-threading for background actions to improve performance when processing large datasets. The multi-threading pattern uses a coordinator-worker model where a loop action spawns multiple worker threads that process partitioned data in parallel.

## When to Use Multi-Threading

Multi-threading is beneficial for:
- Full data replications or exports
- Batch processing of large record sets
- Data transformations on millions of records
- Archive backfill operations
- Any operation where data can be partitioned

Multi-threading is NOT suitable for:
- Incremental updates (small datasets)
- Operations requiring strict sequential processing
- Operations with complex inter-record dependencies
- Single-record operations

## Multi-Threading Architecture

### Components

1. **Configuration Fields** - Control multi-threading behavior
2. **Coordinator Action** - Spawns and manages worker threads
3. **Worker Action** - Processes partitioned data
4. **Locking Mechanism** - Prevents duplicate execution
5. **Checkpoint System** - Tracks progress for restart capability

### Data Flow

```
User/System
    ↓
Coordinator Action (ReplicateLoop)
    ↓
Spawns N Worker Threads (Replicate)
    ↓
Each Thread Processes Data Partition
    ↓
Completion Handler (SetFinished)
```

## Configuration Fields

### Required Fields

```
MultiThreadFullReplications is Boolean
    // Enables/disables multi-threading
    
MultiThreadNumThreads is Numeric size 2
    // Number of parallel threads (must be > 1)
    default label is "NumberOfThreads"
    
MultiThreadField is a BusinessField
    // Field used to partition data across threads
    // Must be Numeric, Integer, Date, TimeStamp, or UniqueID type
    context of BusinessClass
    
MultiThreadSchemaBuilt is Boolean
    // Tracks whether schema has been built for this run
    default label is untranslatable
```

### Field Rules for Configuration

```
MultiThreadField
    if (MultiThreadFullReplications)
        required
            "MultiThreadFieldMustBeEntered"
        constraint(MultiThreadField.RepresentationType.Numeric
                    or MultiThreadField.RepresentationType.Integer
                    or MultiThreadField.RepresentationType.Date
                    or MultiThreadField.RepresentationType.TimeStamp
                    or MultiThreadField.RepresentationType.UniqueID)
            "MultiThreadFieldMustBeANumericOrIntegerOrDateOrTimestampType"
            
MultiThreadNumThreads
    if (MultiThreadFullReplications)
        required
            "MustChooseANumberOfThreads"
        constraint(MultiThreadNumThreads > 1)
            "NumberOfThreadsMustBeMoreThan1"
```

## Locking Mechanism

### Lock Fields

```
ReplicateLoopLocked is a NativeField
    type is Boolean
    // Prevents duplicate coordinator execution
    
ReplicateThreadLocked is a NativeField
    type is Boolean
    restricted
    // Prevents duplicate worker thread execution
    // Uses LocalThreadNum to identify specific thread
```

### Lock Actions

```
LockReplicateLoop is an Instance Action 
    restricted                          
    
    Entrance Rules
        constraint (not ReplicateLoopLocked)
            "ReplicationSet<ReplicationSet>,Sequence<RepSetBC>,BusinessClass<BusinessClass>,ReplicateClassName<ReplicateClassName>ReplicateLoopIsAlreadyLocked.ThisPotentiallyIndicatesADuplicateThread."

UnlockReplicateLoop is an Instance Action
    restricted

LockReplicateThread is an Instance Action 
    restricted
    
    Parameters
        PrmThreadNum is Numeric size 2
            default label is "ThreadNumber"
    
    Entrance Rules
        if (PrmThreadNum < 0)
            LocalThreadNum = 0
        else
            LocalThreadNum = PrmThreadNum
        
        constraint (not ReplicateThreadLocked)
            "ReplicationSet<ReplicationSet>,Sequence<RepSetBC>,BusinessClass<BusinessClass>,ReplicateClassName<ReplicateClassName>ReplicateThread<LocalThreadNum>IsAlreadyLocked.ThisPotentiallyIndicatesADuplicateThread."
    
    Exit Rules
        LocalThreadNum = -1

UnlockReplicateThread is an Instance Action
    restricted	
    
    Parameters
        PrmThreadNum is Numeric size 2
            default label is "ThreadNumber"
    
    Entrance Rules
        if (PrmThreadNum < 0)
            LocalThreadNum = 0
        else
            LocalThreadNum = PrmThreadNum
    
    Exit Rules
        LocalThreadNum = -1
```

### Bulk Unlock

```
UnlockAllReplicateThreads is an Instance Action
    restricted	
    
    Parameters
        WasIncremental is Boolean
            default label is "Incremental"
    
    Local Fields
        ReplLocalThreadNum	is Numeric size 2
        
    Action Rules
        ReplLocalThreadNum = 0
        
        if (MultiThreadFullReplications and (DisableIncrementalReplication or not IncrementalReplication or not WasIncremental))
            while (ReplLocalThreadNum < MultiThreadNumThreads)
                invoke UnlockReplicateThread
                    invoked.PrmThreadNum = ReplLocalThreadNum
                ReplLocalThreadNum = ReplLocalThreadNum + 1
        else
            invoke UnlockReplicateThread
                invoked.PrmThreadNum = 0
```

## Coordinator Action Pattern

The coordinator action spawns worker threads and manages their lifecycle.

### Example: ReplicateLoop

```
ReplicateLoop is an Instance Action
    run in background
    restricted
    valid when (ReplicationSet.InProcess)
    
    Queue Mapping Fields
        ReplicationSet
        BusinessClass
    
    Local Fields
        ReplLocalThreadNum	is Numeric size 2
        LocalRequestId  is UniqueID
        LocalBackgroundGroup is AlphaUpper size 34
    
    Entrance Rules
        invoke LockReplicateLoop
        
    Action Rules
        ReplLocalThreadNum = 0
        LocalBackgroundGroup = ReplicationSet + RepSetBC
    
        if (MultiThreadFullReplications
        and (not IncrementalReplication 
        or   DisableIncrementalReplication 
        or   not ReplicationSet.LastRefreshStamp entered))
            // Multi-threaded execution
            invoke Create RepSetBCHistory	
                invoked.ReplicationSet 				 = ReplicationSet
                invoked.RepSetBC 					 = RepSetBC
                invoked.RepSetBCHistory.Backfill 	 = 0 
                invoked.RepSetBCHistory.HistoryStamp = ReplicationSet.AdjustedCurrentRefreshStamp 

            initialize MultiThreadSchemaBuilt

            while (ReplLocalThreadNum < MultiThreadNumThreads)
                invoke Replicate in background group (LocalBackgroundGroup)
                    invoked.PrmThreadNum = ReplLocalThreadNum
                ReplLocalThreadNum = ReplLocalThreadNum + 1
                
            invoke SetFinished in background group (ReplicationSet)
                run after background group (LocalBackgroundGroup)
        else
            // Single-threaded execution
            invoke Replicate in background group (ReplicationSet)
                invoked.PrmThreadNum = -1
```

### Key Patterns

1. **Lock on entry**: Prevent duplicate coordinator execution
2. **Background group**: Coordinate all worker threads
3. **While loop**: Spawn N worker threads
4. **Thread numbering**: Pass unique thread number (0, 1, 2, ...)
5. **Completion handler**: Schedule cleanup after all threads complete
6. **Single-threaded fallback**: Use PrmThreadNum = -1 for single thread

## Worker Action Pattern

The worker action processes a partition of data assigned to its thread.

### Example: Replicate

```
Replicate is an Instance Action 
    run in background
    restricted
    valid when (ReplicationSet.InProcess)
    
    Parameters
        PrmThreadNum is Numeric size 2
            default label is "ThreadNumber"
    
    Queue Mapping Fields
        ReplicationSet
        BusinessClass
    
    Entrance Rules
        constraint (not SynchronizeRecordCountLocked)
            "A_'SynchronizeRecordCount'_isInProcess.ReplicationCannotRunUntilComplete."
        invoke LockReplicateThread 
            invoked.PrmThreadNum = PrmThreadNum
            
        if (DisableIncrementalReplication and ExpectedNumberOfRecordsIsValid) 
            initialize ExpectedNumberOfRecords
        
        LocalStarted = system current timestamp
        
    Exit Rules
        NonHistoricalChangeOptimizationEligible = LocalNonHistChangeOptEligible
        
        if (ExpectedNumberOfRecordsIsValid)
            ExpectedNumberOfRecords = ExpectedNumberOfRecords + LocalNumberCreated + LocalBackFillUpdateAsDelete - LocalNumberDeleted
                
        if (ReplicationSet.CurrentRefreshStamp entered 
        and (ReplicationSet.MaintainHistoryCount > 0
        or   LocalReplicationWarnings entered))
            if (PrmThreadNum = -1)
                // Single-threaded: Create or update history directly
                if (not RepSetBCHistoryInstance exists) 
                    invoke Create RepSetBCHistory
                        // ... set all fields
                else
                    invoke Update RepSetBCHistoryInstance
                        // ... update all fields
            else
                // Multi-threaded: Update shared history record
                if (RepSetBCHistoryInstance exists) 
                    invoke UpdateForLoop RepSetBCHistoryInstance
                        invoked.PrmRecordCount				= LocalRecordCount 
                        invoked.PrmSegments					= LocalSegments	   
                        invoked.PrmNumberCreated			= LocalNumberCreated 
                        invoked.PrmNumberUpdated			= LocalNumberUpdated 
                        invoked.PrmNumberDeleted			= LocalNumberDeleted 
                        invoked.PrmBackfillUpdateCreates	= LocalBackFillUpdateAsDelete 
                        invoked.PrmStarted					= LocalStarted	   
                        invoked.PrmReplicationWarnings 		= LocalReplicationWarnings 
                        
                        if (ExpectedNumberOfRecordsIsValid)
                            invoked.PrmExpectedNumberOfRecords = LocalNumberCreated + LocalBackFillUpdateAsDelete - LocalNumberDeleted
        
        invoke UnlockReplicateThread
            invoked.PrmThreadNum = PrmThreadNum
```

### Key Patterns

1. **Thread parameter**: Receive thread number from coordinator
2. **Lock with thread number**: Each thread locks independently
3. **Thread-specific logic**: Different behavior for single vs multi-threaded
4. **Accumulator updates**: Aggregate results from thread execution
5. **Unlock on exit**: Always release thread lock
6. **Timestamp tracking**: Record start time for monitoring

## Data Partitioning

### Partition Strategy

Data is partitioned based on the `MultiThreadField` value:
- Thread 0: Processes records where MultiThreadField MOD NumThreads = 0
- Thread 1: Processes records where MultiThreadField MOD NumThreads = 1
- Thread N: Processes records where MultiThreadField MOD NumThreads = N

### Choosing a Partition Field

Good partition fields:
- **Evenly distributed** - Values spread across range
- **Immutable** - Value doesn't change during processing
- **Indexed** - For efficient filtering
- **Numeric types** - Integer, Numeric, UniqueID
- **Date/Time types** - Date, TimeStamp

Poor partition fields:
- **Skewed distribution** - Most records have same value
- **Mutable** - Value changes during processing
- **Text fields** - Not supported
- **Boolean fields** - Only 2 partitions possible

### Example Partition Fields

```
// Good choices
RECORD_ID           // UniqueID - evenly distributed
CREATE_STAMP        // TimeStamp - evenly distributed
SEQUENCE_NUMBER     // Numeric - evenly distributed

// Poor choices
STATUS              // Limited values, skewed distribution
ACTIVE_FLAG         // Boolean - only 2 partitions
DESCRIPTION         // Text - not supported
```

## Checkpoint System

### Checkpoint Records

For restart capability, use checkpoint records to track progress:

```
RepSetBCCheckPoint is a BusinessClass
    // Tracks progress for each thread
    
    Persistent Fields
        ReplicationSet
        RepSetBC
        ThreadNumber        is Numeric size 2
        LastProcessedValue  is like MultiThreadField
        RecordsProcessed    is Numeric size 12
        LastUpdateStamp     is TimeStamp
```

### Using Checkpoints

```
Action Rules
    // Read checkpoint for this thread
    if (RepSetBCCheckPointRel exists)
        LocalStartValue = first RepSetBCCheckPointRel.LastProcessedValue
    else
        LocalStartValue = 0
    
    // Process records from checkpoint
    for each BusinessClassRel
        where (MultiThreadField > LocalStartValue
        and    MultiThreadField MOD MultiThreadNumThreads = PrmThreadNum)
        
        // Process record
        // ...
        
        // Update checkpoint periodically
        if (RecordsProcessed MOD 1000 = 0)
            invoke Update RepSetBCCheckPointRel
                invoked.LastProcessedValue = each.MultiThreadField
                invoked.RecordsProcessed = RecordsProcessed
                invoked.LastUpdateStamp = system current timestamp
            commit transaction
```

### Clearing Checkpoints

```
Action Rules
    if (MultiThreadNumThreads > 0)
        invoke Delete RepSetBCCheckPoint set
```

## Completion Handler

### SetFinished Pattern

```
SetFinished is an Instance Action
    run in background
    restricted
    
    Queue Mapping Fields
        ReplicationSet
        BusinessClass
    
    Action Rules
        if (RepSetBCHistoryInstance exists)
            if (ReplicationSet.CurrentRefreshStamp entered  
            and (ReplicationSet.MaintainHistoryCount > 0
            or   RepSetBCHistoryInstance.ReplicationWarnings entered))
                invoke Update RepSetBCHistoryInstance
                    invoked.Finished = update stamp 
            else
                invoke Delete RepSetBCHistoryInstance
```

### Scheduling Completion

```
invoke SetFinished in background group (ReplicationSet)
    run after background group (LocalBackgroundGroup)
```

## Safety Mechanisms

### Force Unlock Actions

Provide manual override for stuck locks:

```
ForceUnlockReplicateLoop is an Instance Action
    valid when (ReplicateLoopLocked)
    confirmation required
        "ReplicateLoopShouldOnlyBeUnlockedIfYouAreCertainItIsNotActivelyProcessing.AreYouSure?"
    
    Action Rules
        invoke UnlockReplicateLoop
```

### Automatic Lock Release

Connection-level locks automatically release when:
- Process terminates normally
- Process crashes or is killed
- Connection is closed
- End of work is reached

### Cleanup on Reset

```
ClearRestartInformation is an Instance Action
    restricted
    
    Parameters
        Unlock is Boolean
        
    Action Rules
        initialize RestartInformation	
        initialize RestartData
        
        if (Unlock)
            if (ReplicateLoopLocked)
                invoke UnlockReplicateLoop
                
            invoke UnlockAllReplicateThreads
                invoked.WasIncremental = WasIncremental
        
        if (MultiThreadNumThreads > 0)
            invoke Delete RepSetBCCheckPoint set
```

## Best Practices

### Configuration

1. **Choose appropriate thread count**
   - 2-8 threads for most scenarios
   - More threads = more overhead
   - Consider server CPU cores

2. **Select good partition field**
   - Evenly distributed values
   - Immutable during processing
   - Indexed for performance

3. **Test with small datasets first**
   - Verify partitioning works correctly
   - Check for race conditions
   - Monitor resource usage

### Implementation

1. **Always use locking**
   - Prevent duplicate execution
   - Use connection-level locks
   - Provide force unlock option

2. **Implement checkpoints**
   - Enable restart capability
   - Commit periodically
   - Clear on completion

3. **Handle single-threaded fallback**
   - Use PrmThreadNum = -1
   - Same code path, different logic
   - Simplifies testing

4. **Aggregate results carefully**
   - Use thread-safe updates
   - Consider race conditions
   - Validate final counts

### Monitoring

1. **Track thread progress**
   - Log start/end times
   - Record counts processed
   - Capture warnings/errors

2. **Monitor resource usage**
   - CPU utilization
   - Memory consumption
   - Database connections

3. **Provide visibility**
   - Show active threads
   - Display progress
   - Report completion status

## Common Pitfalls

### Race Conditions

**Problem**: Multiple threads updating same record
**Solution**: Partition data so threads don't overlap

### Uneven Distribution

**Problem**: One thread processes 90% of data
**Solution**: Choose better partition field with even distribution

### Deadlocks

**Problem**: Threads waiting on each other's locks
**Solution**: Use consistent lock ordering, avoid circular dependencies

### Memory Issues

**Problem**: Too many threads consuming too much memory
**Solution**: Limit thread count, process in smaller batches

### Stuck Locks

**Problem**: Lock not released after crash
**Solution**: Provide force unlock action, use connection-level locks

## Related Topics

- [Actions](actions.md) - Action definitions and types
- [Background Processing](background-processing.md) - Background execution
- [Locking](locking.md) - Locking mechanisms
- [Performance Optimization](performance-optimization.md) - Performance tuning
