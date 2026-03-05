# Actions

## Overview

Actions define operations that can be performed on business objects. They control how records are created, updated, deleted, and processed. Actions can be simple instance operations or complex set-based processes, and can include parameters, validation rules, and workflow integration.

## Syntax

```
<ActionName> is a[n] [<ActionType>] [Request] Action
    [<DefaultLabel>]
    [privileged]
    [action tag is <ActionTag>]
    …
    [completion message is <Message>]
    [no records message is <Message>]
    [workflow event title is <Message>]
    [valid when <Condition>]
    [<Message>]
    [restricted]
    [(restrict | enable) action on lists]
    [(refresh and | lazily) lock this instance]
    [(enable | disable) ImmutableContextCache]
    [bod type is <Literal>]
    [bypass field rules]
    [bypass relational integrity rules]
    [use array insert]
    [manual update]
    [disable checkpoint]
    [subject is <Subject>]
    [effective date required]
    [reason code required]
    [action comment required]
    [request action process is <PFlowServiceName>]
    [request action linkback webapp is <WebAppName>]
    [disable RetroactiveEffectiveDating]
    [disable resume on error]
    [disable multiple instance selection]
    [confirmation required]
        [<Message>]
    [run in (foreground | background)]
    [[initial] schedule concurrency is (FullConcurrency | NoConcurrency | NoActionGroupConcurrency)]
    [[initial] misfire strategy is (DoNothing | RunOnce | RunAllProcessesScheduled)]
    [allow anonymous access]
    [allow user fields [from <BusinessClass>] as parameters]
    …
    [synchronized [on <Message>]]
    [allow unlimited concurrency]
    Queue Mapping Fields
        <RelatedField>
        …
    Set Is
        <RelatedValue>
        …
    Parameters
        <FieldName> [<DataDefinition>]
        …
    Parameter Rules
        <parameter FullFieldName>
            <Rule>
            …
    Local Fields
        // same definition as on Business Class
    Results
        (<FieldName> | <BusinessClass> view)
        …
    Field Rules
        [(merge | replace) specifications]
        <FieldRules>
        …
    SubType <ConditionName> Field Rules
        [(merge | replace) specifications]
        <FieldRules>
        …
    Accumulators
        <FieldName> [<DataDefinition>]
    Instance Selection [<AsOfOperator>]
        [include [only] deleted records]
        where <Condition>
    Sort Order [is (<SetName> | primary)]
        <RelatedField>
        …
    [is transaction boundary]
    Action Rules
        <Rule>
        …
    Empty Set Rules
        <Rule>
        …
    Set Rules
        Entrance Rules
            <Rule>
            …
        Exit Rules
            <Rule>
            …
    <sort FullFieldName> Set Rules
        Entrance Rules
            <Rule>
            …
        Exit Rules
            <Rule>
            …
    Instance Rules
        <Rule>
        …
    Entrance Rules
        <Rule>
        …
    Exit Rules
        <Rule>
        …
    InitiateRequest Rules
        <Rule>
        …
    UpdateRequest Rules
        <Rule>
        …
    CancelRequest Rules
        <Rule>
        …
    Rule Blocks
        <RuleBlockName>
        …
            <Rule>
            …
```

## Action Types

```
ActionType ::= ( Create    // Creates new records, fires all field rules
               | Update    // Updates record, fires all field rules
               | Delete    // Virtual delete, marks as deleted
               | Purge     // Physical delete, removes from database
               | Preview   // Like update but doesn't fire field rules or save
               | Instance  // Updates record without field rules
               | Set       // Runs rules on set of records
               | Import)   // Creates in context of new instance, doesn't auto-save
```

### Create Action
- Creates new records
- Fires all field rules
- Initializes default values
- Validates required fields

### Update Action
- Updates existing records
- Fires all field rules
- Validates changes
- Tracks modifications

### Delete Action
- Virtual delete (soft delete)
- Marks record as deleted
- Does not physically remove from database
- Preserves data for audit/recovery

### Purge Action
- Physical delete (hard delete)
- Removes record from database permanently
- Cannot be recovered
- Use with caution

### Preview Action
- Similar to update
- Does not fire field rules
- Does not actually update record
- Useful for "what-if" scenarios

### Instance Action
- Does not run field rules
- Does update record
- Direct manipulation
- Use for system operations

### Set Action
- Runs rules on set of records
- Batch processing
- Can process multiple instances
- Supports instance selection

### Import Action
- Runs in context of new instance
- Does not implicitly save
- Used for data import
- Supports BOD type specification

## Automatic Action Generation

### Default Behavior
If no actions are defined:
- Create, Update, and Delete are automatically generated
- Standard behavior for each type
- No customization

### Required Actions
If any actions are defined:
- At least one must be a Create-type action
- Update and Delete are optional
- Cannot have business object that can never be created

### Explicit Declaration
To use standard actions when defining custom actions:
```
Create is an Action
Update is an Action
Delete is an Action
```

## Action Attributes

### Basic Attributes

| Attribute | Description |
|-----------|-------------|
| `<DefaultLabel>` | Human-readable label for UI |
| `privileged` | Requires elevated privileges |
| `action tag is <ActionTag>` | Tags for audit trail |
| `completion message is <Message>` | Message on successful completion |
| `no records message is <Message>` | Message when Set Action finds no records |
| `workflow event title is <Message>` | Title for workflow events (Request Actions) |
| `valid when <Condition>` | Action available only when condition true |
| `restricted` | Can be invoked only, not executed via UI |

### List Behavior

| Attribute | Description |
|-----------|-------------|
| `restrict action on lists` | Overrides business class default, restricts on lists |
| `enable action on lists` | Overrides business class default, enables on lists |

### Locking

| Attribute | Description |
|-----------|-------------|
| `refresh and lock this instance` | Refreshes and locks (Instance, Update, Delete, Purge) |
| `lazily lock this instance` | Locks lazily (Instance, Update, Delete, Purge) |

### Performance

| Attribute | Description |
|-----------|-------------|
| `enable ImmutableContextCache` | Enables immutable context caching |
| `disable ImmutableContextCache` | Disables immutable context caching (default) |

### Import Actions

| Attribute | Description |
|-----------|-------------|
| `bod type is <Literal>` | BOD type for import: `'<verb>.<noun>'` |

### Rule Bypass

| Attribute | Description |
|-----------|-------------|
| `bypass field rules` | Skips field rules (Create, Update) |
| `bypass relational integrity rules` | Skips integrity checks (Delete, Purge) |

### Create Actions

| Attribute | Description |
|-----------|-------------|
| `use array insert` | Uses array insert for performance |

### Set Actions

| Attribute | Description |
|-----------|-------------|
| `manual update` | Manual update mode |
| `disable checkpoint` | Disables restart logic, implies unlimited concurrency |
| `allow unlimited concurrency` | Allows unlimited concurrent execution |

### Subject

| Attribute | Description |
|-----------|-------------|
| `subject is <Subject>` | Associates action with business subject |

### Required Fields

| Attribute | Description |
|-----------|-------------|
| `effective date required` | Requires effective date |
| `reason code required` | Requires reason code |
| `action comment required` | Requires comment |

### Request Actions

| Attribute | Description |
|-----------|-------------|
| `request action process is <PFlowServiceName>` | ProcessFlow service for request |
| `request action linkback webapp is <WebAppName>` | Web app for linkback |

### Effective Dating

| Attribute | Description |
|-----------|-------------|
| `disable RetroactiveEffectiveDating` | Disables retroactive effective dating |

### Error Handling

| Attribute | Description |
|-----------|-------------|
| `disable resume on error` | Disables error resumption |

### UI Behavior

| Attribute | Description |
|-----------|-------------|
| `disable multiple instance selection` | Prevents multi-select on lists |
| `confirmation required [<Message>]` | Requires user confirmation |

### Execution Mode

| Attribute | Description |
|-----------|-------------|
| `run in foreground` | Runs synchronously |
| `run in background` | Runs asynchronously |

### Background Execution

| Attribute | Description |
|-----------|-------------|
| `schedule concurrency is FullConcurrency` | Full concurrent execution |
| `schedule concurrency is NoConcurrency` | No concurrent execution |
| `schedule concurrency is NoActionGroupConcurrency` | No concurrency within action group |
| `initial schedule concurrency is ...` | Initial concurrency setting |

### Misfire Strategy

| Attribute | Description |
|-----------|-------------|
| `misfire strategy is DoNothing` | Skip missed executions (default) |
| `misfire strategy is RunOnce` | Run once if missed |
| `misfire strategy is RunAllProcessesScheduled` | Run all missed executions |
| `initial misfire strategy is ...` | Initial misfire setting |

### Security

| Attribute | Description |
|-----------|-------------|
| `allow anonymous access` | Allows anonymous user execution |

### User Fields

| Attribute | Description |
|-----------|-------------|
| `allow user fields as parameters` | Allows user fields as parameters |
| `allow user fields from <BusinessClass> as parameters` | Allows user fields from specific class |

### Synchronization

| Attribute | Description |
|-----------|-------------|
| `synchronized [on <Message>]` | Enforces synchronization (background only) |

## Action Components

### Queue Mapping Fields
```
Queue Mapping Fields
    <RelatedField>
    …
```
Up to two fields for queue mapping.

### Set Definition
```
Set Is
    <RelatedValue>
    …
```
Defines the set for Set Actions.

### Parameters
```
Parameters
    <FieldName> [<DataDefinition>]
    …
```
Input parameters for the action.

### Parameter Rules
```
Parameter Rules
    <parameter FullFieldName>
        <Rule>
        …
```
Validation and defaulting for parameters.

### Local Fields
```
Local Fields
    // same definition as on Business Class
```
Temporary fields for action execution.

### Results
```
Results
    (<FieldName> | <BusinessClass> view)
    …
```
Output results from Set Actions.

### Accumulators
```
Accumulators
    <FieldName> [<DataDefinition>]
```
Accumulator fields for Set Actions.

### Instance Selection
```
Instance Selection [<AsOfOperator>]
    [include [only] deleted records]
    where <Condition>
```
Filters instances for Set Actions.

### Sort Order
```
Sort Order [is (<SetName> | primary)]
    <RelatedField>
    …
```
Defines processing order for Set Actions.

### Transaction Boundary
```
is transaction boundary
```
Marks action as transaction boundary.

## Action Rules

### Action Rules
```
Action Rules
    <Rule>
    …
```
Rules for entire set (Set Action) or instance (other actions).
Can include constraint rules to prevent action execution.

### Empty Set Rules
```
Empty Set Rules
    <Rule>
    …
```
Rules when Set Action finds no records (Set Actions only).

### Set Rules
```
Set Rules
    Entrance Rules
        <Rule>
        …
    Exit Rules
        <Rule>
        …
```
Rules for Set Action execution (Set Actions only).

### Sort-Specific Set Rules
```
<sort FullFieldName> Set Rules
    Entrance Rules
        <Rule>
        …
    Exit Rules
        <Rule>
        …
```
Rules specific to sort field changes (Set Actions only).

### Instance Rules
```
Instance Rules
    <Rule>
    …
```
Rules for each instance in set (Set Actions only).

### Entrance Rules
```
Entrance Rules
    <Rule>
    …
```
Rules executed before Action Rules.
Useful for Delete actions (record deleted before Action Rules).

### Exit Rules
```
Exit Rules
    <Rule>
    …
```
Rules executed after Action Rules.
Useful for Create actions (record created after Action Rules).

### Request Action Rules

```
InitiateRequest Rules
    <Rule>
    …
```
Rules when request action is initiated (Request Actions only).

```
UpdateRequest Rules
    <Rule>
    …
```
Rules when request action is updated (Request Actions only).

```
CancelRequest Rules
    <Rule>
    …
```
Rules when request action is canceled/rejected/withdrawn (Request Actions only).

## Event Handlers

```
EventHandler ::=
    on (<FullStateName>.<ActionName>
       | entrance to <FullStateName>
       | exit of <FullStateName>)
    Action Rules
        <Rule>
```

Handles state-related events:
- Action execution in specific state
- Entrance to state
- Exit from state

## Action Override

```
ActionOverride ::=
    [<FullStateName>.]<ActionName> is an ActionOverride
        [<DefaultLabel>]
        [effective date required]
        [reason code required]
        [action comment required]
```

Valid on configuration only. Overrides action attributes.

## Examples

### Example 1: Simple Create Action

```
CREATE is a Create Action
    default label is "Create Order"
    completion message is "Order created successfully"
    
    Parameters
        CUSTOMER
        ORDER_DATE
    
    Action Rules
        order.number = next.order.number
        order.status = "Draft"
        created.by = current.user
        created.date = today
```

### Example 2: Approval Action

```
APPROVE is an Instance Action
    default label is "Approve"
    valid when status = "Pending"
    confirmation required
        "Are you sure you want to approve this request?"
    
    Action Rules
        constraint current.user.has.permission("Approve")
            "You do not have permission to approve"
        
        make transition to APPROVED
        approval.date = today
        approved.by = current.user
```

### Example 3: Set Action

```
PROCESS_BATCH is a Set Action
    default label is "Process Batch"
    run in background
    allow unlimited concurrency
    
    Set Is
        order set
    
    Instance Selection
        where status = "Pending"
    
    Sort Order
        priority descending
        order.date
    
    Instance Rules
        process.order
        status = "Processed"
        processed.date = today
    
    Empty Set Rules
        log "No pending orders to process"
```

### Example 4: Request Action

```
SUBMIT_FOR_APPROVAL is a Request Action
    default label is "Submit for Approval"
    workflow event title is "Approval Request"
    request action process is APPROVAL_PROCESS
    
    InitiateRequest Rules
        status = "Pending Approval"
        submitted.date = today
        submitted.by = current.user
    
    UpdateRequest Rules
        last.update.date = today
    
    CancelRequest Rules
        status = "Cancelled"
        cancellation.date = today
```

### Example 5: Import Action

```
IMPORT_ORDERS is an Import Action
    default label is "Import Orders"
    bod type is "Sync.SalesOrder"
    bypass field rules
    
    Parameters
        IMPORT_FILE
    
    Action Rules
        dbimport IMPORT_FILE into ORDER
```

### Example 6: PurchaseOrder - Instance Action with Parameters and Validation

From the PurchaseOrder business class, demonstrating a complex instance action with parameters, parameter rules, and validation:

```
AcknowledgeManually is an Instance Action
    disable multiple instance selection
    valid when (AllowManualAcknowledgement)
    Parameters
        PrmAcknowledgementType is Alpha size 2
            default label is "AcknowledgementType"
            States
                Accepted                        value is "MT"
                AcknowledgedWithDetailChange    value is "MC"
        PrmAcknowledgementDate is Date
            default label is "AcknowledgementDate"
        PrmAcknowledgementPOShipDate is Date
            default label is "PO Ship Date"
        PrmAcknowledgementMessage is Alpha size 100
            default label is "AcknowledgementMessage"
    Parameter Rules
        PrmAcknowledgementType
            initial value is PrmAcknowledgementType.Accepted
            default to PrmAcknowledgementType.Accepted
        
        PrmAcknowledgementDate
            initial value is current corporate date
            default to current corporate date
            constraint (PrmAcknowledgementDate >= PurchaseOrderDate)
                "AcknowledgementDateCannotBeLessThanPurchaseOrderDate"
        
        PrmAcknowledgementPOShipDate
            constraint (PrmAcknowledgementPOShipDate >= PurchaseOrderDate)
                "ShipDateCannotBeLessThanPurchaseOrderDate"
        
        PrmAcknowledgementMessage
            required
                "AcknowledgementMessageIsRequired"
    Action Rules
        invoke Update first NonEDIPurchaseOrderRevisionsRel 
            invoked.AcknowledgementDate     = PrmAcknowledgementDate
            invoked.TransmissionMessage     = PrmAcknowledgementMessage
            
        for each PurchaseOrderLine set
            invoke GenerateLineAcknowledgements each
                invoked.PrmAcknowledgementType = PrmAcknowledgementType
                invoked.PrmAcknowledgementPOShipDate = PrmAcknowledgementPOShipDate
```

This example demonstrates:
- **disable multiple instance selection**: Prevents selecting multiple records for this action
- **valid when**: Conditional availability based on business logic
- **Parameters with States**: State field parameter with defined values
- **Parameter Rules**: Comprehensive parameter validation and defaulting
- **initial value**: Setting initial parameter values
- **default to**: Defaulting parameter values
- **constraint**: Parameter validation with error messages
- **required**: Mandatory parameter validation
- **invoke Update**: Updating related records
- **for each**: Iterating over related records
- **Passing parameters**: Passing action parameters to invoked actions

### Example 7: PurchaseOrder - Set Action with Complex Instance Selection

From the PurchaseOrder business class, demonstrating a set action with complex filtering and batch processing:

```
IssueFromBatch is a Set Action
    restricted
    Local Fields
        LocalBatchID is an IDMPID
        LocalSetMassIssue is Boolean
        LocalErrorMessage is Alpha 150
        LocalHasError is Boolean
        LocalErrorCounter is Numeric 9
        SavedPurchaseOrderResultResultView is a PurchaseOrderResult view
        LocalBegin is Boolean
        LocalBatchIDArray is an IDMUniqueIDOccurs
        LocalIDMBatchIDLimit is Numeric 4
        LocalUseIDM is Boolean
        LocalPurchaseOrderRange is like PurchaseOrderRange
            
    Parameters
        PrmCompany is a PurchasingCompany
        PrmIssueDraft is Boolean
        PrmShipToLocation is an InventoryLocation
        PrmVendorClass is a VendorClass
        PrmVendor is a Vendor
        PrmPurchaseFrom is a VendorLocation
        PrmBuyerGroup is a BuyerGroup
        PrmBuyer is a Buyer
        PrmPOCode is a POCode
        PrmPurchaseOrder1 is a PurchaseOrder
        PrmPurchaseOrder2 is a PurchaseOrder
        PrmPurchaseOrder3 is a PurchaseOrder
        PrmPurchaseOrder4 is a PurchaseOrder
        PrmPurchaseOrder5 is a PurchaseOrder
        PrmPurchaseOrder6 is a PurchaseOrder
        PrmPurchaseOrder7 is a PurchaseOrder
        PrmPurchaseOrder8 is a PurchaseOrder
        PrmPaperIssue is Boolean
        PrmFaxIssue is Boolean
        PrmEDIIssue is Boolean
        PrmEmailIssue is Boolean
        PrmXMLIssue is Boolean
        PrmCommentCode1 is a StandardCommentCode 
        PrmCommentCode2 is a StandardCommentCode 
        PrmCommentCode3 is a StandardCommentCode 
        PrmCommentCode4 is a StandardCommentCode 
        PrmCommentCode5 is a StandardCommentCode 
        PrmCommentCode6 is a StandardCommentCode 
        PrmCommentCode7 is a StandardCommentCode 
        PrmCommentCode8 is a StandardCommentCode 

    Parameter Rules
        PrmCompany
            required

    Instance Selection
        where   (Company = PrmCompany
        and     (PrmShipToLocation not entered
        or       ShipToLocation = PrmShipToLocation)
        and     (PrmVendorClass not entered
        or       Vendor.VendorClass = PrmVendorClass)
        and     (PrmVendor not entered
        or       Vendor = PrmVendor)
        and     (PrmPurchaseFrom not entered
        or       PurchaseFromLocation = PrmPurchaseFrom)
        and     (PrmBuyerGroup not entered
        or       Buyer.BuyerGroup = PrmBuyerGroup)
        and     (PrmBuyer not entered
        or       Buyer = PrmBuyer)
        and     (PrmPOCode not entered
        or       POCode = PrmPOCode)
        and    ((PrmPurchaseOrder1 not entered
        and      PrmPurchaseOrder2 not entered
        and      PrmPurchaseOrder3 not entered
        and      PrmPurchaseOrder4 not entered
        and      PrmPurchaseOrder5 not entered
        and      PrmPurchaseOrder6 not entered
        and      PrmPurchaseOrder7 not entered
        and      PrmPurchaseOrder8 not entered
        and      IsToBePrinted)
        or     ((PrmPurchaseOrder1 entered and PurchaseOrder = PrmPurchaseOrder1)
        or      (PrmPurchaseOrder2 entered and PurchaseOrder = PrmPurchaseOrder2)
        or      (PrmPurchaseOrder3 entered and PurchaseOrder = PrmPurchaseOrder3)
        or      (PrmPurchaseOrder4 entered and PurchaseOrder = PrmPurchaseOrder4)
        or      (PrmPurchaseOrder5 entered and PurchaseOrder = PrmPurchaseOrder5)
        or      (PrmPurchaseOrder6 entered and PurchaseOrder = PrmPurchaseOrder6)
        or      (PrmPurchaseOrder7 entered and PurchaseOrder = PrmPurchaseOrder7)
        or      (PrmPurchaseOrder8 entered and PurchaseOrder = PrmPurchaseOrder8)))
        and    ((PrmPaperIssue and IssueMethod.Paper)
        or      (PrmFaxIssue and IssueMethod.Fax)
        or      (PrmEDIIssue and IssueMethod.EDI)
        or      (PrmEmailIssue and IssueMethod.Email)
        or      (PrmXMLIssue and IssueMethod.XML))
        and     (PurchaseOrderLifeCycleState.Unreleased
        or      PurchaseOrderLifeCycleState.Released)
        and     (not ApprovalStatus.NeedsApproval))

    Accumulators
        POCounter
        BatchIDCounter

    Action Rules
        Set Rules
            Entrance Rules
                LocalBatchID = current timestamp

                if (PrmCompany.UseIDM)
                    LocalUseIDM = true
                    BatchIDCounter = 1
                    LocalBatchIDArray.IDMPID[BatchIDCounter] = LocalBatchID
                    LocalBegin = true

                    if (config.PURCHASEORDER_IDMBATCHID_LIMIT > 0)
                        LocalIDMBatchIDLimit = config.PURCHASEORDER_IDMBATCHID_LIMIT
                    else 
                        LocalIDMBatchIDLimit = 50

                if (PrmPurchaseOrder1 entered 
                or  PrmPurchaseOrder2 entered 
                or  PrmPurchaseOrder3 entered 
                or  PrmPurchaseOrder4 entered 
                or  PrmPurchaseOrder5 entered 
                or  PrmPurchaseOrder6 entered 
                or  PrmPurchaseOrder7 entered 
                or  PrmPurchaseOrder8 entered)
                    LocalSetMassIssue = true

                invoke Create PurchaseOrderResult
                    assign result to SavedPurchaseOrderResultResultView
                    invoked.FinanceEnterpriseGroup = PrmCompany.FinanceEnterpriseGroup
                    invoked.ReportType = PurchaseOrderResult.ReportType.MassPOIssueResult
                    invoked.PrintTimeStamp = current timestamp
                    invoked.Status = PurchaseOrderResult.Status.Processing
                    
            Exit Rules
                if (LocalUseIDM)
                    for each LocalBatchIDArray.IDMPID
                        LocalAsyncBackgroundGroup = "IDMJobGetStatusByBatch_" + each
                        if (each entered) 
                            invoke GetStatusByBatchID IDMJob in background group (LocalAsyncBackgroundGroup)
                                invoked.PrmActor = actor
                                invoked.PrmIDMBatchID = each
                                
                            if (not PrmIssueDraft)
                                invoke IssueFinalCompletionSet in background
                                    run after background group (LocalAsyncBackgroundGroup)
                                    invoked.PrmCompany = PrmCompany
                                    invoked.PrmPurchaseOrderRange = LocalPurchaseOrderRange
                                    invoked.PrmActor = actor
                                    invoked.PrmIDMBatchID = each
```

This example demonstrates:
- **restricted**: Action can only be invoked programmatically
- **Local Fields**: Multiple local variables for action processing
- **Multiple Parameters**: 20+ parameters for flexible filtering
- **Complex Instance Selection**: Multi-condition filtering with optional parameters
- **Accumulators**: Counting records processed
- **Set Rules with Entrance/Exit**: Pre and post-processing logic
- **Batch ID management**: Tracking batch operations
- **Configuration parameters**: Using config values
- **Background processing**: Invoking actions in background
- **Background groups**: Coordinating async operations
- **run after background group**: Chaining background operations
- **Array handling**: Managing batch ID arrays
- **Conditional logic**: Complex if/else structures
- **Result tracking**: Creating result records for reporting

### Example 8: PurchaseOrder - Simple Set Action with Rule Block

From the PurchaseOrder business class, demonstrating a set action that uses a rule block:

```
CreateIDMXML is a Set Action
    completion message is "Generated_XMLFileFor_Purchase_Order<PrmPurchaseOrder>"
    default label is "Generate_XMLFile"
    run in foreground
    Parameters
        PrmCompany is a PurchasingCompany
            default label is "Company"
        PrmPurchaseOrder is a PurchaseOrder
            default label is "PurchaseOrder"

    Parameter Rules
        PrmCompany
            required
        
        PrmPurchaseOrder
            required

    Local Fields
        LocalExecute is Boolean
        
    Instance Selection
        where (Company = PrmCompany
        and    PurchaseOrder = PrmPurchaseOrder)

    Action Rules
        Instance Rules
            include SetIDMXMLDefinitionValues

            LocalExecute = IDMXMLDefinition.GenerateXML

            if (IDMXMLDefinition.OutputAttachment entered)
                invoke FastUpdate
                    invoked.PurchaseOrderIDMXMLFile = IDMXMLDefinition.OutputAttachment
```

This example demonstrates:
- **completion message**: Custom completion message with parameter substitution
- **default label**: User-friendly action label
- **run in foreground**: Synchronous execution
- **Required parameters**: Both parameters must be provided
- **Instance Selection**: Filtering to specific record
- **Instance Rules**: Rules executed for each instance
- **include**: Using a rule block for complex logic
- **FastUpdate**: Efficient update without full field rules
- **Conditional update**: Only updating if condition is met

### Example 9: PurchaseOrder - Conditional Instance Action

From the PurchaseOrder business class, demonstrating a simple conditional action:

```
DeleteIDMXML is an Instance Action
    valid when (PurchaseOrderIDMXMLFile entered)
    completion message is "Deleted_XMLFileFor_Purchase_Order<PurchaseOrder>"
    confirmation required
    default label is "Delete_XMLFile"
    Action Rules
        invoke FastUpdate
            initialize invoked.PurchaseOrderIDMXMLFile
```

This example demonstrates:
- **valid when**: Action only available when condition is true
- **confirmation required**: User must confirm before execution
- **initialize**: Clearing a field value
- **FastUpdate**: Efficient update for single field change
- **Message substitution**: Using field values in messages

### Example 10: PurchaseOrder - Restricted Instance Action

From the PurchaseOrder business class, demonstrating a restricted action for internal use:

```
UpdateRevisionsAndIssueMethodFlag is an Instance Action
    restricted
    Action Rules
        if  (PurchaseOrderRevision = 1
        and  RevisionsInclude not entered
        and  IssueMethod not changed)
            if (PurchaseFromLocationRel.RevisionsInclude entered)
                RevisionsInclude = PurchaseFromLocationRel.RevisionsInclude
            else
            if (PurchasingVendorRel.RevisionsInclude entered)
                RevisionsInclude = PurchasingVendorRel.RevisionsInclude
```

### Example 11: RepSetBC - Multi-Threading Pattern with ReplicateLoop

From the RepSetBC business class, demonstrating a sophisticated multi-threading pattern for parallel data processing:

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
            invoke Replicate in background group (ReplicationSet)
                invoked.PrmThreadNum = -1
```

This example demonstrates:
- **Multi-threading coordinator pattern**: Spawns multiple worker threads
- **Background groups**: Coordinating parallel execution
- **while loop**: Iterating to create multiple threads
- **Thread numbering**: Passing unique thread numbers (0, 1, 2, ...)
- **run after background group**: Scheduling cleanup after all threads complete
- **Locking mechanism**: Preventing duplicate execution
- **Conditional multi-threading**: Only multi-threads for full replications
- **Single-threaded fallback**: Uses PrmThreadNum = -1 for single thread

### Example 12: RepSetBC - Worker Thread Action

From the RepSetBC business class, demonstrating the worker thread action that processes partitioned data:

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
        
        if (not Inactive
        and ColumnarFlattenHierarchy
        and (PrmThreadNum = -1
        or   PrmThreadNum = 0)
        and (AlwaysRebuild
        or   not IncrementalReplication 
        or   DisableIncrementalReplication 
        or   not ReplicationSet.LastRefreshStamp entered))
            invoke ReplicateFlattenedHierarchy in background group (ReplicationSet)
        
    Exit Rules
        NonHistoricalChangeOptimizationEligible = LocalNonHistChangeOptEligible
        
        if (ExpectedNumberOfRecordsIsValid)
            ExpectedNumberOfRecords = ExpectedNumberOfRecords + LocalNumberCreated + LocalBackFillUpdateAsDelete - LocalNumberDeleted
                
        if (ReplicationSet.CurrentRefreshStamp entered 
        and (ReplicationSet.MaintainHistoryCount > 0
        or   LocalReplicationWarnings entered))
            if (PrmThreadNum = -1)
                if (not RepSetBCHistoryInstance exists) 
                    invoke Create RepSetBCHistory				
                        invoked.ReplicationSet 				 = ReplicationSet
                        invoked.RepSetBC 					 = RepSetBC
                        invoked.RepSetBCHistory.Backfill 	 = 0 
                        invoked.RepSetBCHistory.HistoryStamp = ReplicationSet.AdjustedCurrentRefreshStamp 
                        invoked.RecordCount					 = LocalRecordCount 
                        invoked.Segments		 	 		 = LocalSegments    
                        invoked.Started						 = LocalStarted     
                        invoked.NumberCreated				 = LocalNumberCreated 
                        invoked.NumberUpdated				 = LocalNumberUpdated 
                        invoked.NumberDeleted				 = LocalNumberDeleted 
                        invoked.BackfillUpdateCreates		 = LocalBackFillUpdateAsDelete 
                        invoked.Finished					 = LocalFinished    
                        invoked.ReplicationWarnings			 = LocalReplicationWarnings 
                        
                        if (ExpectedNumberOfRecordsIsValid)
                            invoked.ExpectedNumberOfRecords	 = ExpectedNumberOfRecords
                else
                    invoke Update RepSetBCHistoryInstance
                        invoked.RecordCount				= LocalRecordCount 
                        invoked.Segments				= LocalSegments    
                        invoked.NumberCreated			= LocalNumberCreated 
                        invoked.NumberUpdated			= LocalNumberUpdated 
                        invoked.NumberDeleted			= LocalNumberDeleted 
                        invoked.BackfillUpdateCreates	= LocalBackFillUpdateAsDelete 
                        invoked.Started					= LocalStarted     
                        invoked.Finished				= LocalFinished    
                        invoked.ReplicationWarnings		= LocalReplicationWarnings 
                        
                        if (ExpectedNumberOfRecordsIsValid)
                            invoked.ExpectedNumberOfRecords	 = ExpectedNumberOfRecords
            else
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

This example demonstrates:
- **Thread parameter**: Receives thread number from coordinator
- **Thread-specific locking**: Each thread locks with its unique number
- **Thread-specific processing**: PrmThreadNum = -1 for single thread, >= 0 for multi-thread
- **Conditional history updates**: Different logic for single vs multi-threaded
- **Accumulator updates**: Aggregating results from thread execution
- **Automatic unlock**: Releasing thread lock in Exit Rules
- **Constraint validation**: Preventing conflicts with other operations
- **Timestamp tracking**: Recording start time for performance monitoring

### Example 13: RepSetBC - Thread Locking Actions

From the RepSetBC business class, demonstrating the locking mechanism for multi-threading:

```
LockReplicateLoop is an Instance Action 
    restricted                          
    
    Entrance Rules
        constraint (not ReplicateLoopLocked)
            "ReplicationSet<ReplicationSet>,Sequence<RepSetBC>,BusinessClass<BusinessClass>,ReplicateClassName<ReplicateClassName>ReplicateLoopIsAlreadyLocked.ThisPotentiallyIndicatesADuplicateThread."

UnlockReplicateLoop is an Instance Action
    restricted	

ForceUnlockReplicateLoop is an Instance Action
    valid when (ReplicateLoopLocked)
    confirmation required
        "ReplicateLoopShouldOnlyBeUnlockedIfYouAreCertainItIsNotActivelyProcessing.AreYouSure?"
    
    Action Rules
        invoke UnlockReplicateLoop

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

This example demonstrates:
- **Loop-level locking**: Prevents duplicate coordinator execution
- **Thread-level locking**: Prevents duplicate worker thread execution
- **Native field locks**: Uses NativeField for connection-level locking
- **Constraint-based locking**: Prevents action execution if already locked
- **Force unlock**: Manual override for stuck locks
- **Bulk unlock**: Unlocking all threads at once
- **Thread number normalization**: Converting -1 to 0 for single-threaded mode
- **Safety valve pattern**: Automatic lock release at end of work
- **LocalThreadNum pattern**: Using local field to track thread contextendorRel.RevisionsInclude
            else
            if (Company.RevisionsInclude entered)
                RevisionsInclude = Company.RevisionsInclude
            else
                RevisionsInclude = RevisionsInclude.NotApplicable

            if (RevisionsInclude = RevisionsInclude.NotApplicable)
                IssueMethod = IssueMethod.DoNotPrint
            else
            if (PurchaseFromLocationRel.RevisedIssueMethod entered)
                IssueMethod = PurchaseFromLocationRel.RevisedIssueMethod
            else
            if (PurchasingVendorRel.RevisedIssueMethod entered)
                IssueMethod = PurchasingVendorRel.RevisedIssueMethod
```

This example demonstrates:
- **restricted**: Action can only be invoked by other actions, not from UI
- **Cascading defaults**: Checking multiple sources for default values
- **Relation navigation**: Accessing related business class fields
- **State field assignment**: Setting state field values
- **Complex conditional logic**: Nested if/else structures
- **Defaulting hierarchy**: Location → Vendor → Company → Default

## Best Practices for Actions

### Parameter Design
- Use descriptive parameter names with "Prm" prefix
- Define parameter states for limited value sets
- Provide default labels for UI display
- Use parameter rules for validation and defaulting
- Make required parameters explicit

### Instance Selection
- Use optional parameters for flexible filtering
- Combine conditions with AND/OR appropriately
- Check for "not entered" to make parameters optional
- Filter by lifecycle state when appropriate
- Consider performance with large datasets

### Set Actions
- Use Local Fields for processing variables
- Define Accumulators for counting/summing
- Use Set Rules for pre/post processing
- Consider background processing for large sets
- Implement error handling and logging

### Action Rules
- Use constraint rules for validation
- Provide clear error messages
- Use invoke for related record updates
- Consider FastUpdate for performance
- Include rule blocks for reusable logic

### Background Processing
- Use background groups for coordination
- Implement "run after" for dependencies
- Track batch operations with IDs
- Handle errors gracefully
- Provide status feedback

### Restricted Actions
- Use for internal/system operations
- Document purpose and usage
- Invoke from other actions or rules
- Implement proper security
- Test thoroughly

### Confirmation
- Require confirmation for destructive operations
- Provide clear confirmation messages
- Explain consequences
- Allow cancellation
- Log confirmed actions

This completes the Actions documentation with real-world enterprise examples from the PurchaseOrder business class.

### Example: Requisition - Create Action with Field Rules and Exit Rules

From the Requisition business class, demonstrating a Create action within a StateCycle:

```
Create is a Create Action
    Action Rules
    Exit Rules
```

This is the base Create action in the Unreleased state of the RequisitionLifeCycle statecycle.


### Example: Requisition - Complex Create Action with Parameters

From the Requisition business class, demonstrating a comprehensive Create action with extensive parameters:

```
RSSLPLCreate is a Create Action
    default label is "Create"
    completion message is "RequisitionCreated"
    Local Fields
        LocalIsDropship is Boolean
    Parameters
        PrmCompany is an InventoryCompany
        PrmRequestingLocation is a RequestingLocation
        PrmFromCompanyLocation is a FromCompanyLocation
        PrmReferenceNumber is a ReferenceNumber
        PrmRequisitionDescription is Alpha size 30
        PrmRequestedDeliveryDate is Date
        PrmAllocationPriority is an AllocationPriority
        PrmVendor is a Vendor
        PrmPurchaseFromLocation is a VendorLocation
        PrmTransactionCurrencyCode is a Currency
        PrmPurchaseTaxable is Boolean
        PrmPurchaseTaxCode is a TaxCode
        PrmDefaultDistributionAccount is a RQHeaderDefaultCodeBlock
        PrmBuyer is a Buyer
        PrmCommodityCode is a CommCodes
        PrmPOCode is a POCode
        PrmSourcingEventRequired is Boolean
        PrmDeliverTo is a DeliverTo
        PrmDropshipAddress is a PostalAddressV2
        PrmReceivingOptions is Numeric size 1
            States
                ReceivingRequired value is 0
                ReceivingNotAllowed value is 1
        PrmDropship is Boolean
        PrmDropshipPhoneNumber is an TelephoneNumber
        PrmDropshipContact is an AddressContact
        PrmDropshipName is a Name
```


```
    Parameter Rules
        PrmCompany
            required
            initial value is actor.agent(Employee).Employee.Requester.Company
            default to actor.agent(Employee).Employee.Requester.Company
        PrmRequestingLocation
            required
                "Field_\Requesting_\LocationIsRequired"
            initial value is actor.agent(Employee).Employee.Requester.RequestingLocation
            default to actor.agent(Employee).Employee.Requester.RequestingLocation
        PrmDropship
            if (PrmReceivingOptions.ReceivingRequired)
                PrmDropship = false
            else
                PrmDropship = true
        PrmAllocationPriority
            if (RSSLPLConfigurationParameterRel exists)
                default to PrmAllocationPriorityLevel
                default to RSSLPLAllocationPriorityLevelRel.AllocationPriorityLevel
        PrmRequestedDeliveryDate
            if (RequesterRel.RequireRequestedDeliveryDate)
                required
                    "Field_Requested_Delivery_DateIsRequired"
        PrmDeliverTo
            if (RequesterRel.RequireDeliverTo)
                required
                    "Field_Deliver_ToIsRequired"
```


```
    Action Rules
        invoke Create this instance
            invoked.Requester = actor.agent(Employee).Employee
            invoked.Company = PrmCompany
            invoked.RequestingLocation = PrmRequestingLocation
            invoked.FromCompanyLocation = PrmFromCompanyLocation
            invoked.ReferenceNumber = PrmReferenceNumber
            invoked.RequisitionDescription = PrmRequisitionDescription
            invoked.RequestedDeliveryDate = PrmRequestedDeliveryDate
            invoked.AllocationPriority = PrmAllocationPriority
            if (RSSLPLConfigurationParameterRel exists)
                invoked.TransientAllocationPriorityLevel = PrmAllocationPriority
            invoked.Vendor = PrmVendor
            invoked.PurchaseFromLocation = PrmPurchaseFromLocation
            invoked.TransactionCurrencyCode = PrmTransactionCurrencyCode
            invoked.PurchaseTaxable = PrmPurchaseTaxable
            invoked.PurchaseTaxCode = PrmPurchaseTaxCode
            if (PrmCurrentDefaultDistributionAccount not = PrmRequestingLocation.IssueAccount)
                invoked.DefaultDistributionAccount = PrmDefaultDistributionAccount
            invoked.Buyer = PrmBuyer
            invoked.CommodityCode = PrmCommodityCode
            invoked.POCode = PrmPOCode
            invoked.SourcingEventRequired = PrmSourcingEventRequired
            invoked.DeliverTo = PrmDeliverTo
            invoked.Dropship = PrmDropship
            invoked.DropshipName = PrmDropshipName
            invoked.DropshipPhoneNumber = PrmDropshipPhoneNumber
            invoked.DropshipContact = PrmDropshipContact
            invoked.DropshipAddress = PrmDropshipAddress
            invoked.RequisitionSource = RqSource.RequisitionSelfService
```


```
    Exit Rules
        invoke UpdateCurrentRequisitionForRQC this instance.Requester
            invoked.PrmCompany = PrmCompany
            invoked.PrmRequisition = this instance.Requisition
```

This example demonstrates:
- **Local Fields**: Temporary variables for action processing
- **Extensive Parameters**: 20+ parameters with various data types
- **Parameter States**: State field parameters with defined values
- **Parameter Rules**: Comprehensive validation and defaulting logic
- **Conditional Requirements**: Dynamic required field validation
- **Actor Context**: Using actor.agent() to access current user context
- **invoke Create**: Creating the business class instance
- **Conditional Field Assignment**: Using if statements in Action Rules
- **Exit Rules**: Post-creation processing
- **invoke Action**: Calling another action after creation


### Example: Requisition - Instance Action with Complex Business Logic

From the Requisition business class, demonstrating the Release action with extensive validation:

```
Release is an Instance Action
    completion message is "<ReleaseMessage>"
    valid when (IsForRelease)
    Local Fields
        LocalTotalAmountPerContract is an UnsignedInternationalAmount
        LocalTotalAmountPerContractLine is an UnsignedInternationalAmount
        LocalTotalQuantityPerContractLine is a Quantity
        LocalTotalQuantityPerLocation is a Quantity
        LocalItem is an Item
        LocalFromCompany is a Company
        LocalItemLocation is an InventoryLocation
        TransactionError is Boolean
        TransactionErrorMessage is Alpha 300
        FESFundingSourceTable
        FESCounter is Numeric 4
```


```
    Action Rules
        constraint (RequisitionLinesUnreleasedRel exists)
            "NoRequisitionLinesAreAvailableToBeReleased"
        constraint (RequestingLocation.Active)
            "RequestingLocationIsInactive"
        if (RequestingLocation.ProcessLevel entered)
            constraint (RequestingLocation.ProcessLevel.Active)
                "ProcessLevel<RequestingLocation.ProcessLevel>IsInactive"
        constraint (FromCompanyLocation.FromLocation.IsActive)
            "DeliverFromLocationIsInactive"
        constraint (Status.Unreleased or Status.InProcess)
            "RequisitionHasAlreadyBeenReleased"
        constraint (Requester entered)
            "MustEnterARequesterBeforeReleasingTheRequisition"
        
        if (VendorEditRequisitionLineRel exists)
            include ReleaseLimitsValidation
        
        constraint (RequisitionReleaseMessages not entered)
            "<DerivedRequisitionReleaseMessages>"
```


```
        for each RequisitionLineByContract
            if (LocalContract not = each.Contract)
                LocalContract = each.Contract
                initialize LocalTotalAmountPerContract
                initialize LocalContractLine
            
            if (LocalContractLine not = each.ContractLine)
                LocalContractLine = each.ContractLine
                initialize LocalTotalAmountPerContractLine
                initialize LocalTotalQuantityPerContractLine
            
            if (each.Contract.MaximumAmount > 0)
                LocalTotalAmountPerContract += each.DerivedCostInContractCurrency
                constraint (LocalTotalAmountPerContract <= each.Contract.AmountRemaining)
                    "RequisitionAmountExceedsMaximumAmountRemainingForContract<each.Contract>"
```


```
        for each RequisitionLinesUnreleasedRel
            invoke Unreleased.EditRelease each
            
            if(FESEnabled)
                invoke Unreleased.FrontEndSplitDistributions each
                    resume on error
                        TransactionError = true
                        TransactionErrorMessage = error message
                constraint(not TransactionError)
                    "RQFES:\Front_\End_\Split<TransactionErrorMessage>"
        
        if (RQGeneralLedgerSystemCodeRel.EncumbranceOption.TrackAndEdit
        and CommitmentsExist)
            if (TransientFromMassRQCreateAndRelease = false)
                if (HeaderBudgetErrorsExist)
                    invoke Delete HeaderBudgetEditErrorRel
                if (DistributionBudgetErrorsExist)
                    for each CommittedRequisitionLineDistributionRel
                        invoke DeleteBudgetErrors each
            BudgetEditProcessing = BudgetEditProcessing.InProcess
            invoke EditTotalsRoute BudgetTemplateGroupTotal
                invoked.PrmFinanceEnterpriseGroup = Company.ItemGroup.FinanceEnterpriseGroup
                invoked.PrmBudgetEditGroup = UniqueID
                invoked.PrmBusinessClassName = "Requisition"
                invoked.PrmEditContext = "Release"
        else
            invoke FinalRelease
```


This example demonstrates:
- **valid when**: Conditional action availability
- **Local Fields**: Multiple local variables for calculations
- **constraint**: Extensive validation with error messages
- **Conditional Logic**: Complex if/else structures
- **include**: Including rule blocks for reusable logic
- **for each**: Iterating over related records
- **initialize**: Resetting local variables
- **Accumulation**: Building totals across iterations
- **invoke**: Calling other actions on related records
- **resume on error**: Error handling with transaction error capture
- **Budget Integration**: Complex budget editing and commitment tracking
- **State Transitions**: Implicit state changes through action execution


### Example: Requisition - Set Action with Background Processing

From the Requisition business class, demonstrating a Set action for batch operations:

```
CreateUsingCopyFromRequisitionBatch is a Set Action
    run in background
    completion message is "CreateUsingCopyFromRequisitionSubmitted"
    disable resume on error
    restricted
    Local Fields
        LocalFromLocationChanged is Boolean
        LocalCompletionMessage is Text
        NewRequisitionView is a Requisition view
    Parameters
        PrmCompany is an InventoryCompany
        PrmCopyFromRequisition is a Requisition
        PrmRequester is a Requester
        PrmRequestingLocation is a RequestingLocation
        PrmFromCompanyLocation is a FromCompanyLocation
        PrmRequestedDeliveryDate is Date
        PrmVendor is a Vendor
        PrmPurchaseFromLocation is a PurchaseFromLocation
        PrmRequisitionSource is a RqSource
        PrmRequisitionDescription is Alpha size 30
```


```
    Parameter Rules
        PrmCompany
            required
        PrmCopyFromRequisition
            required
            constraint (PrmCopyFromRequisition exists)
                "CopyFromRequisition<PrmCopyFromRequisition>DoesNotExist"
        PrmRequester
            initial value is PrmCopyFromRequisition.Requester
            force default to PrmCopyFromRequisition.Requester
        PrmRequestingLocation
            initial value is PrmCopyFromRequisition.RequestingLocation
            default to PrmCopyFromRequisition.RequestingLocation
        PrmFromCompanyLocation
            initial value is PrmCopyFromRequisition.FromCompanyLocation
            if (PrmFromCompanyLocation != PrmCopyFromRequisition.FromCompanyLocation)
                LocalFromLocationChanged = true
            default to PrmCopyFromRequisition.FromCompanyLocation
        PrmRequestedDeliveryDate
            if(PrmCopyFromRequisition.RequestedDeliveryDate >= current corporate date)
                default to PrmCopyFromRequisition.RequestedDeliveryDate
        PrmRequisitionSource
            default to RqSource.Requisition
```


```
    Instance Selection
        where (Company = PrmCompany and Requisition = PrmCopyFromRequisition)
    Action Rules
        Instance Rules
            invoke Create Requisition
                assign result to NewRequisitionView
                fill in fields from PrmCopyFromRequisition
                    except invoked.Requisition
                    except invoked.CreationDate
                    except invoked.Status
                    except invoked.NumberOfLines
                    except invoked.Closed
                    except invoked.OperatorID
                    except invoked.CreatedBy
                    except invoked.LastUpdateDate
                    except invoked.LastUpdateBy
                invoked.Company = PrmCompany
                invoked.Requisition = 0
                invoked.Requester = PrmRequester
                invoked.RequestingLocation = PrmRequestingLocation
                invoked.FromCompanyLocation = PrmFromCompanyLocation
                if (PrmRequestedDeliveryDate entered)
                    invoked.RequestedDeliveryDate = PrmRequestedDeliveryDate
                if (PrmRequisitionDescription entered)
                    invoked.RequisitionDescription = PrmRequisitionDescription
                if (PrmVendor entered)
                    invoked.Vendor = PrmVendor
                else
                    invoked.Vendor = PrmCopyFromRequisition.Vendor
                invoked.RequisitionSource = PrmRequisitionSource
```


```
            for each PrmCopyFromRequisition.RequisitionCommentRel
                invoke Create RequisitionComment
                    fill in fields from each
                        except invoked.Attachment.FSMAttachmentIDM
                    invoked.Requisition = Requisition
                    invoked.Attachment.File = each.Attachment.File
                    invoked.Attachment.MimeType = each.Attachment.MimeType
                    invoked.Attachment.Title = each.Attachment.Title
            
            for each PrmCopyFromRequisition.RequisitionLinesToCopyRel
                invoke CopyRequisitionLine each
                    invoked.PrmCompany = NewRequisitionView.Company
                    invoked.PrmRequisition = NewRequisitionView.Requisition
                    invoked.PrmFromLocationChanged = LocalFromLocationChanged
                    invoked.PrmVendor = PrmVendor
            
            LastRequisitionLine = NumberOfLines
            LocalCompletionMessage = CopyFromRequisitionCreatedMessage
```


This example demonstrates:
- **run in background**: Asynchronous execution for long-running operations
- **disable resume on error**: Error handling strategy
- **restricted**: Action only callable from other actions
- **view**: Using view variables to capture created instances
- **Instance Selection**: Filtering records for set processing
- **Instance Rules**: Rules executed for each instance
- **fill in fields from**: Copying fields from source record
- **except**: Excluding specific fields from copy
- **for each**: Iterating over related records to copy them
- **Nested invokes**: Creating related records within action

### Example: SimpleProduct - Instance Action with Discount Calculation

From the SimpleProduct business class, demonstrating an instance action with parameter validation and local field calculations:

```
ApplyDiscount is an Instance Action
	default label is untranslatable:"Apply_Discount"
	completion message is untranslatable:"Discount_of_<PrmDiscountPercent>%_applied_to_<PRODUCT_CODE>"
	
	Parameters
		PrmDiscountPercent is Numeric size 5 decimals 2
			default label is untranslatable:"Discount_Percent"
	
	Local Fields
		NewPrice is Numeric size 15 decimals 2
	
	Parameter Rules
		PrmDiscountPercent
			required
				untranslatable:"Discount_Percent_is_required"
			
			constraint is PrmDiscountPercent > 0 and PrmDiscountPercent <= 100
				untranslatable:"Discount_must_be_between_0_and_100_percent"
	
	Action Rules
		NewPrice = UNIT_PRICE * (1 - (PrmDiscountPercent / 100))
		UNIT_PRICE = NewPrice
```

This example demonstrates:
- **Instance Action**: Updates record without firing field rules
- **untranslatable labels**: Using untranslatable syntax with underscores for labels
- **Parameter with validation**: Numeric parameter with range constraint
- **required**: Mandatory parameter validation
- **constraint**: Range validation (0 < percent <= 100)
- **Local Fields**: Temporary calculation variable
- **Percentage calculation**: Computing discount amount
- **Direct field update**: Updating persistent field from local variable
- **Message substitution**: Using parameter values in completion messageecuted for each instance in the set
- **assign result to**: Capturing the result of an invoked action
- **fill in fields from**: Copying fields from source record
- **except**: Excluding specific fields from copy operation
- **Nested for each**: Iterating over related records within instance processing
- **Conditional field assignment**: Using if statements for optional fields
- **Complex copying logic**: Copying header, comments, and lines


### Example: Requisition - StateCycle Actions

From the Requisition business class, demonstrating actions within a StateCycle:

```
StateCycles
    RequisitionLifeCycle is a StateCycle
        state field is Status
        
        Unreleased is a State
            
            Create is a Create Action
                Action Rules
                Exit Rules
            
            Update is an Update Action
                valid when (BudgetEditAndInterfaceNotInProcess)
                Entrance Rules
                    constraint (not IsClosedStatus)
                        "CannotChange;RequisitionIsClosed"
                    include InventoryLocationStatusValidation
                Field Rules
                    Requester
                        cannot be changed
                Action Rules
                    include DefaultDropShipInfo
                Exit Rules
                    if (DisplayOfProcedureInfo)
                        include CheckPHIProcedureInformation
                    if (DefaultCommentCode entered)
                        include CopyStandardCommentCode
```


```
            Delete is a Delete Action
                valid when (BudgetEditAndInterfaceNotInProcess)
                Entrance Rules
                    constraint (not IsClosedStatus)
                        "CannotDelete;RequisitionIsClosed"
                    
                    if (RQGeneralLedgerSystemCodeRel.EncumbranceOption.TrackAndEdit
                    or  RQGeneralLedgerSystemCodeRel.EncumbranceOption.Track)
                        invoke Delete BudgetRebuildBTGTRel
                        invoke Delete HeaderBudgetEditErrorRel
                        invoke Delete DistributionBudgetEditErrorRel
                        for each CommittedRequisitionLineDistributionRel
                            invoke PurgeGLCommitment each
                        invoke Delete BudgetTemplateGroupTotalRel
                        invoke Delete BudgetEditBatchRel
                    
                    for each RequisitionLinesRel
                        if (each.PatientProcedure entered)
                            invoke DeletePatientProcedure RequisitionLinesRel
                    
                    if (PatientProcedureInfoExists)
                        LocalDeleteHeader = true
                        invoke DeletePatientProcedure
                    
                    include ClearCurrentPunchoutContextForRQC
                Exit Rules
                    include ClearCurrentRequisitionForRQC
```


```
        Released is a State
            
            Process is an Instance Action
                restricted
                Action Rules
                    if (!RequisitionApprovalType.HeaderMatrixApproval)
                        if (AttainedLevel = ApprovalLevel)
                            ApprovedRejectedDate = current timestamp
                            ApprovingRejectingOperatorID = actor
                    LastUpdateDate = current timestamp
                    LastUpdateBy = actor
                    
                    if (not Requisition.RequisitionApprovalType.LineApproval)
                        if (NumberOfLines <= DerivedForegroundLimit)
                            invoke BatchProcess RequisitionLine in foreground
                                invoked.PrmCompany = Company
                                invoked.PrmRequisition = Requisition
                        else
                            invoke BatchProcess RequisitionLine in background
                                invoked.PrmCompany = Company
                                invoked.PrmRequisition = Requisition
```


```
            ManualApprove is an Instance Action
                valid when (NeedsHeaderApproval)
                Action Rules
                    invoke DirectAndManualApprove
                    
                    if (RequisitionApprovalType.HeaderApproval)
                        cancel RequisitionApproval process
                    else
                        invoke ManualApprove ResponsibilityMatrixApprovalProcessorRel
            
            ManualReject is an Instance Action
                valid when (NeedsHeaderApproval)
                Action Rules
                    invoke DirectAndManualReject
                    
                    if (RequisitionApprovalType.HeaderApproval)
                        cancel RequisitionApproval process
                    else
                        invoke ManualReject ResponsibilityMatrixApprovalProcessorRel
        
        Processed is a State
            
            Close is an Instance Action
                restricted
                valid when (not IsClosedStatus)
                Parameters
                    PrmWarehouseShipment is like WarehouseShipment
                    SkipLineLevelClose is Boolean
                Action Rules
                    if (!SkipLineLevelClose)
                        for each RequisitionLinesOpenRel
                            if (each.Status.Processed)
                                invoke Processed.Close each
                                    invoked.PrmWarehouseShipment = PrmWarehouseShipment
                    
                    if (instance count of RequisitionLinesOpenRel = 0)
                        Closed = true
                        LastUpdateDate = current timestamp
                        LastUpdateBy = actor
```


This example demonstrates:
- **StateCycle Structure**: Organizing actions by business object state
- **State-Specific Actions**: Actions available only in certain states
- **State Transitions**: Implicit transitions through action execution
- **Entrance Rules**: Pre-execution validation and cleanup
- **Exit Rules**: Post-execution processing
- **Field Rules in Actions**: Controlling field behavior within actions
- **cannot be changed**: Preventing field modifications
- **include**: Reusing rule blocks across actions
- **Conditional Cleanup**: State-specific cleanup logic
- **Workflow Integration**: cancel process for workflow cancellation
- **invoke on Relations**: Calling actions on related business classes
- **Foreground vs Background**: Dynamic execution mode selection
- **instance count**: Checking related record counts
- **actor**: Accessing current user for audit fields
- **current timestamp**: Recording action execution time


### Example 11: Comprehensive Set Action - Process Pending Invoices

A complete Set Action demonstrating batch processing with filtering, validation, error handling, and accumulation:

```
ProcessPendingInvoices is a Set Action
    default label is "Process Pending Invoices"
    completion message is "Successfully processed <POCounter> invoices in batch <LocalBatchID>"
    no records message is "No pending invoices found matching the criteria"
    run in background
    allow unlimited concurrency
    
    // Local variables for processing
    Local Fields
        LocalBatchID is Alpha size 50
        LocalProcessedCount is Numeric 9
        LocalErrorCount is Numeric 9
        LocalCurrentUser is an Employee
        LocalProcessDate is Date
        LocalHasError is Boolean
        LocalErrorMessage is Alpha size 255
        LocalTotalAmount is Decimal size 15.2
        
    // Parameters for filtering
    Parameters
        PrmCompany is a Company
            default label is "Company"
            
        PrmCustomer is a Customer
            default label is "Customer"
            
        PrmPriority is like PRIORITY
            default label is "Priority"
            
        PrmInvoiceDateFrom is Date
            default label is "Invoice Date From"
            
        PrmInvoiceDateTo is Date
            default label is "Invoice Date To"
            
        PrmMinAmount is Decimal size 15.2
            default label is "Minimum Amount"
            
        PrmMaxAmount is Decimal size 15.2
            default label is "Maximum Amount"
            
        PrmAutoApprove is Boolean
            default label is "Auto Approve"
            
    // Parameter validation
    Parameter Rules
        PrmCompany
            required
                "Company is required for batch processing"
        
        PrmInvoiceDateFrom
            constraint (PrmInvoiceDateFrom <= current corporate date)
                "Invoice date from cannot be in the future"
        
        PrmInvoiceDateTo
            constraint (PrmInvoiceDateTo >= PrmInvoiceDateFrom)
                "Invoice date to must be greater than or equal to invoice date from"
            default to current corporate date
        
        PrmMinAmount
            constraint (PrmMinAmount >= 0)
                "Minimum amount cannot be negative"
            default to 0
        
        PrmMaxAmount
            constraint (PrmMaxAmount >= PrmMinAmount)
                "Maximum amount must be greater than or equal to minimum amount"
        
        PrmAutoApprove
            default to false
    
    // Define which invoices to process
    Instance Selection
        where (Company = PrmCompany
        and    Status = Status.Pending
        and   (PrmCustomer not entered
        or     Customer = PrmCustomer)
        and   (PrmPriority not entered
        or     Priority = PrmPriority)
        and   (PrmInvoiceDateFrom not entered
        or     InvoiceDate >= PrmInvoiceDateFrom)
        and   (PrmInvoiceDateTo not entered
        or     InvoiceDate <= PrmInvoiceDateTo)
        and   (PrmMinAmount not entered
        or     TotalAmount >= PrmMinAmount)
        and   (PrmMaxAmount not entered
        or     TotalAmount <= PrmMaxAmount))
    
    // Process in priority order, then by date
    Sort Order
        Priority descending
        InvoiceDate
        InvoiceNumber
    
    // Accumulators for tracking
    Accumulators
        POCounter
        TotalAmountAccumulator
    
    // Action processing rules
    Action Rules
        // Set-level entrance rules (run once at start)
        Set Rules
            Entrance Rules
                // Generate unique batch ID
                LocalBatchID = "BATCH_" + current timestamp
                LocalProcessDate = current corporate date
                LocalCurrentUser = current user
                LocalProcessedCount = 0
                LocalErrorCount = 0
                LocalTotalAmount = 0
                
                // Log batch start
                log "Starting invoice batch processing: " + LocalBatchID
                log "Company: " + PrmCompany.CompanyName
                log "Date range: " + PrmInvoiceDateFrom + " to " + PrmInvoiceDateTo
                
            // Set-level exit rules (run once at end)
            Exit Rules
                // Log batch completion
                log "Batch processing completed: " + LocalBatchID
                log "Total invoices processed: " + LocalProcessedCount
                log "Total errors: " + LocalErrorCount
                log "Total amount: " + LocalTotalAmount
                
                // Send notification if errors occurred
                if (LocalErrorCount > 0)
                    log "WARNING: " + LocalErrorCount + " invoices failed processing"
        
        // Priority-specific rules (run when priority changes)
        Priority Set Rules
            Entrance Rules
                log "Processing priority group: " + Priority
            
            Exit Rules
                log "Completed priority group: " + Priority
        
        // Instance-level rules (run for each invoice)
        Instance Rules
            // Initialize error tracking
            LocalHasError = false
            LocalErrorMessage = blank
            
            // Validate invoice data
            constraint (Subtotal > 0)
                LocalHasError = true
                LocalErrorMessage = "Subtotal must be greater than zero"
            
            constraint (TotalAmount = Subtotal + TaxAmount)
                LocalHasError = true
                LocalErrorMessage = "Total amount does not match subtotal plus tax"
            
            constraint (DueDate >= InvoiceDate)
                LocalHasError = true
                LocalErrorMessage = "Due date cannot be before invoice date"
            
            constraint (CustomerRel exists)
                LocalHasError = true
                LocalErrorMessage = "Customer record not found"
            
            // Process if no errors
            if (not LocalHasError)
                // Update batch tracking
                BatchID = LocalBatchID
                ProcessedDate = LocalProcessDate
                ProcessedBy = LocalCurrentUser
                
                // Auto-approve if requested and amount is within limit
                if (PrmAutoApprove and TotalAmount <= 10000)
                    Status = Status.Approved
                    ApprovedBy = LocalCurrentUser
                    ApprovalDate = LocalProcessDate
                    log "Auto-approved invoice: " + InvoiceNumber
                else
                    Status = Status.Approved
                    log "Processed invoice: " + InvoiceNumber
                
                // Update counters
                LocalProcessedCount = LocalProcessedCount + 1
                LocalTotalAmount = LocalTotalAmount + TotalAmount
                POCounter = POCounter + 1
                TotalAmountAccumulator = TotalAmountAccumulator + TotalAmount
                
                // Clear any previous error message
                ErrorMessage = blank
            else
                // Record error
                ErrorMessage = LocalErrorMessage
                LocalErrorCount = LocalErrorCount + 1
                log "ERROR processing invoice " + InvoiceNumber + ": " + LocalErrorMessage
        
        // Rules when no invoices found
        Empty Set Rules
            log "No pending invoices found for company: " + PrmCompany.CompanyName
            log "Date range: " + PrmInvoiceDateFrom + " to " + PrmInvoiceDateTo
```

This comprehensive example demonstrates:
- **Set Action Structure**: Complete batch processing action
- **Local Fields**: Variables for tracking processing state
- **Multiple Parameters**: Flexible filtering with 8 parameters
- **Parameter Rules**: Validation and defaulting for all parameters
- **Instance Selection**: Complex multi-condition filtering with optional parameters
- **Sort Order**: Multi-level sorting (priority, date, number)
- **Accumulators**: Tracking counts and totals across instances
- **Set Rules**: Entrance and Exit rules for batch-level processing
- **Sort-Specific Set Rules**: Rules triggered when sort field changes
- **Instance Rules**: Processing logic for each record
- **Error Handling**: Tracking and logging errors per instance
- **Conditional Processing**: Auto-approval based on amount threshold
- **Logging**: Comprehensive logging for audit trail
- **Empty Set Rules**: Handling case when no records match criteria
- **Batch ID Generation**: Creating unique batch identifiers
- **Counter Management**: Incrementing multiple counters
- **Message Substitution**: Using accumulators in completion messages
- **Background Execution**: Running asynchronously with unlimited concurrency
- **Validation Constraints**: Multiple constraint rules with error tracking
- **Audit Fields**: Recording who processed and when

## Best Practices

### Action Design

- **Keep actions focused**: Each action should have a single, clear purpose
- **Use appropriate action types**: Choose the right type for the operation
- **Validate early**: Use constraint rules in Action Rules or Parameter Rules
- **Provide clear messages**: Use completion messages and error messages
- **Consider performance**: Use bypass options carefully, consider background execution
- **Document complex logic**: Add comments for maintainability

### Parameter Design

- **Required parameters**: Mark essential parameters as required
- **Default values**: Provide sensible defaults where appropriate
- **Validation**: Add constraint rules to Parameter Rules
- **Clear labels**: Use descriptive default labels
- **Type consistency**: Use `type is like` for consistency

### Set Action Design

- **Instance Selection**: Make filtering flexible with optional parameters
- **Sort Order**: Define logical processing order
- **Accumulators**: Track important metrics
- **Error Handling**: Handle errors gracefully, don't fail entire batch
- **Logging**: Log key events for troubleshooting
- **Empty Set Rules**: Handle no-match scenario appropriately
- **Background Execution**: Use for long-running processes
- **Batch Tracking**: Use batch IDs for traceability

### Rule Organization

- **Action Rules**: Overall validation and setup
- **Entrance Rules**: Pre-processing, initialization
- **Exit Rules**: Post-processing, cleanup
- **Instance Rules**: Per-record processing (Set Actions)
- **Set Rules**: Batch-level processing (Set Actions)
- **Empty Set Rules**: No-match handling (Set Actions)

### Performance Considerations

- **bypass field rules**: Use for performance, but lose validation
- **use array insert**: Faster bulk inserts (Create Actions)
- **run in background**: For long-running operations
- **allow unlimited concurrency**: For independent batch processes
- **disable checkpoint**: For non-restartable processes
- **Instance Selection**: Filter efficiently at database level
- **Sort Order**: Consider index usage

### Security

- **privileged**: Require elevated permissions
- **restricted**: Prevent direct UI execution
- **valid when**: Control action availability
- **allow anonymous access**: Use with extreme caution
- **Audit fields**: Track who, when, what

### Maintenance

- **Clear naming**: Use descriptive action names
- **Consistent patterns**: Follow established conventions
- **Documentation**: Comment complex logic
- **Testing**: Test all branches and edge cases
- **Error messages**: Provide actionable error messages

### Example 11: User - Simple CRUD Actions with Audit Tracking

From the User business class, demonstrating a simple CRUD pattern with Create, Update, and Purge actions:

```
Actions
	Create is a Create Action
		default label is "Create User"
		completion message is "User <UserID> created successfully"
		
		Parameters
			PrmUserID is Alpha size 20
				default label is "User ID"
			PrmUserName is Alpha size 100
				default label is "User Name"
			PrmEmail is Alpha size 255
				default label is "Email Address"
			PrmActive is Boolean
				default label is "Active"
		
		Parameter Rules
			PrmUserID
				required
					"User ID is required"
			
			PrmUserName
				required
					"User Name is required"
			
			PrmEmail
				required
					"Email Address is required"
			
			PrmActive
				initial value is true
				default to true
		
		Action Rules
			UserID = PrmUserID
			UserName = PrmUserName
			Email = PrmEmail
			Active = PrmActive
			CreatedDate = current timestamp
			CreatedBy = current user
			LastUpdateDate = current timestamp
			LastUpdateBy = current user
	
	Update is an Update Action
		default label is "Update User"
		completion message is "User <UserID> updated successfully"
		
		Action Rules
			LastUpdateDate = current timestamp
			LastUpdateBy = current user
	
	Purge is a Purge Action
		default label is "Purge User"
		completion message is "User <UserID> purged successfully"
		confirmation required
			"Are you sure you want to permanently delete this user? This action cannot be undone."
		
		Action Rules
			constraint (Active = false)
				"Cannot purge an active user. Please deactivate the user first."
```

This example demonstrates:
- **Simple CRUD pattern**: Create, Update, and Purge actions
- **Parameters**: Input parameters for Create action
- **Parameter Rules**: Validation and default values for parameters
- **required**: Mandatory parameter validation
- **initial value and default to**: Setting default parameter values
- **Audit tracking**: Automatic tracking of creation and update timestamps/users
- **completion message**: User-friendly success messages with field substitution
- **confirmation required**: User confirmation for destructive operations
- **constraint in Action Rules**: Business rule validation before purge
- **current timestamp**: System-provided timestamp
- **current user**: System-provided user context
- **Protected fields**: CreatedDate, CreatedBy, LastUpdateDate, LastUpdateBy are protected

## Related Topics

- [Action Rules](action-rules.md) - Rules within actions
- [Action Forms](action-forms.md) - UI forms for actions
- [Field Rules](field-rules.md) - Field-level validation
- [StateCycles](statecycles.md) - State-based action organization
- [Rule Blocks](rule-blocks.md) - Reusable rule logic
- [Parameters](parameters.md) - Action parameters
- [Sets](sets.md) - Record sets for Set Actions
- [Conditions](conditions.md) - Conditional logic
- [Relations](relations.md) - Related business classes

## Summary

Actions are the primary mechanism for defining business operations in LPL. They provide:
- Controlled data manipulation
- Validation and business rules
- Workflow integration
- Batch processing capabilities
- Audit trail
- Security controls

Proper action design is critical for building robust, maintainable business applications.


## Multi-Threading Patterns in Actions

Multi-threading in LPL allows parallel processing of data to improve performance for large-scale operations. The pattern involves a coordinator action that spawns multiple worker threads, each processing a partition of the data.

### Multi-Threading Architecture

The multi-threading pattern consists of:

1. **Configuration Fields** - Fields that control multi-threading behavior
2. **Coordinator Action** - Spawns and manages worker threads
3. **Worker Action** - Processes partitioned data
4. **Locking Actions** - Prevent duplicate execution
5. **Cleanup Actions** - Release locks and finalize processing

### Example: RepSetBC - Complete Multi-Threading Pattern

From the RepSetBC business class, demonstrating the complete multi-threading implementation:

**Configuration Fields:**
```
Persistent Fields
    MultiThreadFullReplications		is Boolean
    MultiThreadNumThreads			is Numeric size 2
        default label is "NumberOfThreads"
    MultiThreadField				is a BusinessField
        context of BusinessClass
    MultiThreadSchemaBuilt          is Boolean
        default label is untranslatable
```

**Coordinator Action:**
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
            invoke Replicate in background group (ReplicationSet)
                invoked.PrmThreadNum = -1
```

**Worker Action:**
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
            
        LocalStarted = system current timestamp
        
    Exit Rules
        if (ExpectedNumberOfRecordsIsValid)
            ExpectedNumberOfRecords = ExpectedNumberOfRecords + LocalNumberCreated - LocalNumberDeleted
        
        invoke UnlockReplicateThread
            invoked.PrmThreadNum = PrmThreadNum
```

**Locking Actions:**
```
LockReplicateLoop is an Instance Action 
    restricted                          
    
    Entrance Rules
        constraint (not ReplicateLoopLocked)
            "ReplicateLoopIsAlreadyLocked.ThisPotentiallyIndicatesADuplicateThread."

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
            "ReplicateThread<LocalThreadNum>IsAlreadyLocked."
    
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

UnlockAllReplicateThreads is an Instance Action
    restricted	
    
    Parameters
        WasIncremental is Boolean
            default label is "Incremental"
    
    Local Fields
        ReplLocalThreadNum	is Numeric size 2
        
    Action Rules
        ReplLocalThreadNum = 0
        
        if (MultiThreadFullReplications 
        and (DisableIncrementalReplication 
        or not IncrementalReplication 
        or not WasIncremental))
            while (ReplLocalThreadNum < MultiThreadNumThreads)
                invoke UnlockReplicateThread
                    invoked.PrmThreadNum = ReplLocalThreadNum
                ReplLocalThreadNum = ReplLocalThreadNum + 1
        else
            invoke UnlockReplicateThread
                invoked.PrmThreadNum = 0
```

### Multi-Threading Key Concepts

**Background Groups:**
- Coordinate related background actions
- Enable "run after" dependencies
- Group threads for collective completion

**Thread Numbering:**
- PrmThreadNum = -1: Single-threaded mode
- PrmThreadNum >= 0: Multi-threaded mode (0, 1, 2, ...)
- Each thread receives unique number

**Locking Strategy:**
- Loop lock: Prevents duplicate coordinator execution
- Thread locks: Prevent duplicate worker execution
- Native fields: Connection-level locking
- Automatic release: Locks released at end of work

**Data Partitioning:**
- MultiThreadField: Field used to partition data
- Must be Numeric, Integer, Date, TimeStamp, or UniqueID
- Each thread processes subset based on field value

**Checkpoint Management:**
- Separate checkpoints for each thread
- Enables restart from failure point
- Cleared when operation completes

### Multi-Threading Best Practices

1. **Configuration Validation**
   - Validate MultiThreadNumThreads > 1
   - Ensure MultiThreadField is appropriate type
   - Check field exists and is indexed

2. **Thread Coordination**
   - Use background groups for related threads
   - Implement proper locking mechanisms
   - Handle single-threaded fallback

3. **Error Handling**
   - Lock threads before processing
   - Unlock in Exit Rules (always executes)
   - Provide force unlock for stuck locks

4. **Performance**
   - Choose appropriate thread count
   - Balance load across threads
   - Monitor thread execution time

5. **Testing**
   - Test single-threaded mode
   - Test multi-threaded mode
   - Test with various thread counts
   - Test error scenarios

### When to Use Multi-Threading

**Good Use Cases:**
- Large data replication
- Batch processing of thousands of records
- Independent record processing
- CPU-intensive calculations
- Parallel data transformations

**Avoid Multi-Threading When:**
- Small datasets (overhead > benefit)
- Sequential dependencies between records
- Shared resource contention
- Complex transaction requirements
- Debugging/troubleshooting

### Multi-Threading Performance Considerations

**Thread Count:**
- Too few: Underutilizes resources
- Too many: Overhead and contention
- Typical: 2-8 threads for most operations
- Consider: CPU cores, database connections, I/O

**Data Partitioning:**
- Even distribution across threads
- Minimize cross-thread dependencies
- Use indexed partition field
- Consider data skew

**Lock Contention:**
- Minimize shared resource access
- Use thread-local variables
- Batch database operations
- Consider lock granularity

This multi-threading pattern enables efficient parallel processing of large datasets while maintaining data integrity and providing robust error handling.
