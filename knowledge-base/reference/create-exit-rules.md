# Create Exit Rules

## Overview

Create Exit Rules define business logic that executes after any Create-type action is executed. They provide a centralized location for post-creation processing that should apply to all record creation scenarios. These rules are ideal for actions that should occur after the record is successfully created and all other creation logic has completed.

## Syntax

```
Create Exit Rules
    <Rule>
    …
```

## When Create Exit Rules Fire

Create Exit Rules execute:
- **After Create action completes** - After all other Create logic
- **After Commit Rules** - After final validation and updates
- **Before transaction commit** - Still within the same transaction
- **For all Create actions** - Regardless of which Create action is invoked

## Execution Order

For a Create action:

1. **Field Rules** - Field defaults and validation
2. **Create Rules** - Common creation logic
3. **Specific Create Action Rules** - Action-specific logic
4. **Commit Rules** - Final validation and updates
5. **Create Exit Rules** - Post-creation processing (fires here)
6. **Database Commit** - Record persisted

## Use Cases

### Post-Creation Notifications
- Send confirmation messages
- Alert stakeholders
- Trigger external notifications
- Update dashboards

### Integration and Synchronization
- Sync with external systems
- Send to message queues
- Update data warehouses
- Trigger ETL processes

### Cascade Operations
- Create dependent records
- Update related systems
- Trigger downstream processes
- Initialize workflows

### Analytics and Reporting
- Update statistics
- Refresh aggregates
- Log analytics events
- Update metrics

### Cleanup and Finalization
- Clean up temporary data
- Finalize calculations
- Archive creation data
- Release resources

## Examples

### Example 1: Send Confirmation Notification

```
Create Exit Rules
    send notification
        to created.by
        description is "Record successfully created"
        detail is "Record " + record.id + " has been created and saved"
        priority is low
```

Confirms successful creation to user.

### Example 2: Sync with External System

```
Create Exit Rules
    send ion bod
        bod is record.xml
        bod type is "Sync.Record"
        assign message id to sync.message.id
        expect acknowledgement
```

Synchronizes new record with external system.

### Example 3: Update Statistics

```
Create Exit Rules
    increment department.total.records
    department.last.record.created = today
    
    if record.type = "High Priority"
        increment department.high.priority.count
```

Updates department statistics after creation.

### Example 4: Trigger Workflow

```
Create Exit Rules
    if requires.onboarding
        initiate ONBOARDING_WORKFLOW workflow
            Variables
                record.id
                record.type
                created.by
                create.date
```

Starts onboarding workflow after record creation.

### Example 5: Create Dependent Records

```
Create Exit Rules
    for each required.attachment.type set
        invoke CREATE.ATTACHMENT_PLACEHOLDER
            invoked.parent.id = record.id
            invoked.attachment.type = each.type
            invoked.status = "Pending"
```

Creates placeholder records for required attachments.

### Example 6: Log Analytics Event

```
Create Exit Rules
    log "Analytics: Record created - Type: " + record.type + 
        " User: " + created.by + " Date: " + today
    
    invoke CREATE.ANALYTICS_EVENT
        invoked.event.type = "RecordCreated"
        invoked.record.id = record.id
        invoked.timestamp = current.time
```

Logs creation event for analytics.

### Example 7: Send Email Confirmation

```
Create Exit Rules
    send email
        to created.by.email
        from system.email
        subject "Record Created: " + record.id
        Contents
            "Your record has been successfully created."
            "Record ID: " + record.id
            "Created: " + today
```

Sends email confirmation of creation.

### Example 8: Update Parent Record

```
Create Exit Rules
    parent.record.child.count += 1
    parent.record.last.child.created = today
    parent.record.last.modified = today
    
    if parent.record.status = "Empty"
        parent.record.status = "Active"
```

Updates parent record after child creation.

### Example 9: Trigger Background Processing

```
Create Exit Rules
    if requires.processing
        invoke PROCESS_RECORD in background
            invoked.record.id = record.id
            invoked.priority = processing.priority
```

Initiates background processing for new record.

### Example 10: Update Cache

```
Create Exit Rules
    clear in-memory cache for RECORD_SUMMARY
    
    log "Cache cleared after record creation: " + record.id
```

Refreshes cache after record creation.

### Example 11: Create Audit Summary

```
Create Exit Rules
    invoke CREATE.AUDIT_SUMMARY
        invoked.record.id = record.id
        invoked.action = "Create"
        invoked.user = created.by
        invoked.timestamp = today
        invoked.field.count = populated.field.count
```

Creates audit summary after successful creation.

### Example 12: Notify Subscribers

```
Create Exit Rules
    for each subscriber in notification.list set
        send notification
            to each.subscriber
            description is "New " + record.type + " created"
            detail is "Record " + record.id + " by " + created.by
```

Notifies all subscribers of new record.

### Example 13: Update Dashboard

```
Create Exit Rules
    invoke UPDATE.DASHBOARD_METRICS
        invoked.metric.type = "RecordCreated"
        invoked.record.type = record.type
        invoked.timestamp = today
```

Updates real-time dashboard metrics.

### Example 14: Initialize Monitoring

```
Create Exit Rules
    invoke CREATE.MONITORING_ENTRY
        invoked.record.id = record.id
        invoked.monitor.type = "Lifecycle"
        invoked.start.date = today
        invoked.status = "Active"
```

Sets up monitoring for new record.

### Example 15: Trigger Integration

```
Create Exit Rules
    if integration.enabled
        invoke INTEGRATION_SERVICE in background
            invoked.record.id = record.id
            invoked.operation = "Create"
            invoked.target.system = integration.target
```

Triggers integration with external systems.

### Example 16: Update Aggregate Tables

```
Create Exit Rules
    invoke UPDATE.AGGREGATE_TABLES
        invoked.table.name = "RECORD_SUMMARY"
        invoked.operation = "Increment"
        invoked.key.values = aggregation.keys
```

Updates denormalized aggregate tables.

### Example 17: Send to Message Queue

```
Create Exit Rules
    invoke QUEUE_MESSAGE
        invoked.queue.name = "RecordCreated"
        invoked.message.body = record.json
        invoked.priority = message.priority
```

Sends creation event to message queue.

### Example 18: Create Search Index

```
Create Exit Rules
    build text search field SEARCH_INDEX
        Fields
        record.id
        record.name
        record.description
        record.tags
```

Builds search index for new record.

### Example 19: Finalize Calculations

```
Create Exit Rules
    final.score = calculate.final.score
    ranking = calculate.ranking
    
    log "Final calculations completed for record " + record.id
```

Performs final calculations after all creation logic.

### Example 20: Archive Creation Data

```
Create Exit Rules
    if archive.creation.data
        invoke CREATE.CREATION_ARCHIVE
            invoked.record.id = record.id
            invoked.creation.data = creation.snapshot
            invoked.archive.date = today
```

Archives creation data for historical reference.

## Best Practices

1. **Use for Post-Creation Actions**
   - Actions that should occur after creation completes
   - Operations that depend on complete record state
   - External notifications and integrations
   - Analytics and reporting

2. **Keep Processing Light**
   - Avoid heavy processing
   - Use background processing for long operations
   - Minimize transaction duration
   - Consider performance impact

3. **Handle Errors Gracefully**
   - Don't fail transaction for non-critical operations
   - Log errors appropriately
   - Use resume on error for background tasks
   - Provide meaningful error messages

4. **Use Background Processing**
   - For external integrations
   - For heavy calculations
   - For non-critical operations
   - To avoid blocking user

5. **Maintain Transaction Integrity**
   - Remember rules are still in transaction
   - Failures will rollback entire creation
   - Use commit transaction carefully
   - Consider transaction boundaries

6. **Document Side Effects**
   - Document external integrations
   - Note notification recipients
   - Describe triggered workflows
   - Explain analytics events

7. **Test Thoroughly**
   - Test with all Create actions
   - Verify execution order
   - Test error scenarios
   - Validate side effects

## Common Patterns

### Notification Pattern
```
Create Exit Rules
    send notification
        to <stakeholder>
        description is "Record created successfully"
```

### Integration Pattern
```
Create Exit Rules
    send ion bod
        bod is <record.data>
        bod type is <message.type>
```

### Analytics Pattern
```
Create Exit Rules
    log "Analytics: " + <event.description>
    invoke CREATE.ANALYTICS_EVENT
        invoked.<fields>
```

### Background Processing Pattern
```
Create Exit Rules
    invoke <processing.action> in background
        invoked.<parameters>
```

### Update Statistics Pattern
```
Create Exit Rules
    increment <counter.field>
    <timestamp.field> = today
```

## Create Exit Rules vs Other Rules

| Rule Type | Execution Timing | Purpose |
|-----------|-----------------|---------|
| Field Rules | During field validation | Field-level logic |
| Create Rules | Before action-specific rules | Common creation logic |
| Action Rules | During action execution | Action-specific logic |
| Commit Rules | Before commit | Final validation |
| Create Exit Rules | After all creation logic | Post-creation processing |

## Interaction with Transaction

Create Exit Rules execute:
- **Within transaction** - Still part of the same transaction
- **Before commit** - Database commit hasn't occurred yet
- **After all validation** - All validation has passed
- **Can cause rollback** - Errors will rollback entire creation

**Important:** If Create Exit Rules fail, the entire Create transaction rolls back, including the record creation.

## Troubleshooting

### Issue: Create Exit Rules not firing

**Possible Causes:**
- Not a Create action
- Syntax errors in rules
- Rules in wrong section
- Transaction rolled back before reaching exit rules

**Solutions:**
- Verify action type
- Check rule syntax
- Verify rule location
- Check for earlier errors

### Issue: Transaction rolling back

**Possible Causes:**
- Error in Create Exit Rules
- Constraint violation
- External system failure
- Resource unavailable

**Solutions:**
- Review error messages
- Check constraint logic
- Use resume on error for non-critical operations
- Handle external failures gracefully

### Issue: Performance degradation

**Possible Causes:**
- Heavy processing in exit rules
- Synchronous external calls
- Complex calculations
- Many notifications

**Solutions:**
- Move heavy processing to background
- Use asynchronous operations
- Optimize calculations
- Batch notifications

### Issue: Notifications not sent

**Possible Causes:**
- Notification configuration
- Recipient not available
- Service unavailable
- Condition not met

**Solutions:**
- Verify notification setup
- Check recipient validity
- Test notification service
- Review condition logic

### Issue: External integration failing

**Possible Causes:**
- External system unavailable
- Network issues
- Authentication failure
- Data format issues

**Solutions:**
- Use background processing
- Implement retry logic
- Use resume on error
- Validate data format

## Related Concepts

- [Create Rules](create-rules.md) - Common creation logic
- [Commit Rules](commit-rules.md) - Final transaction rules
- [Action Rules](action-rules.md) - Action-specific rules
- [Field Rules](field-rules.md) - Field-level rules
- [Business Class Definition](business-class-definition.md) - Business class structure

## Notes

- Create Exit Rules fire for ALL Create-type actions
- Rules execute after all other creation logic but before database commit
- Still within the same transaction; failures cause rollback
- Ideal for post-creation notifications and integrations
- Use background processing for heavy or non-critical operations
- Keep rules efficient to avoid performance impact
- Test with all Create actions in the business class
- Document external integrations and side effects
- Consider using resume on error for non-critical operations
- Remember that errors in Create Exit Rules will rollback the entire creation
- Use for operations that depend on complete, validated record state
- Avoid operations that could fail and cause unnecessary rollbacks
