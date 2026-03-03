# Action Exit Rules

## Overview

Action Exit Rules define business logic that executes on the exit of all actions (except Set Actions) defined on the business class. They provide a centralized location for post-action processing that should apply universally, regardless of which specific action is invoked. These rules are ideal for final cleanup, notifications, and operations that should occur after any action completes.

## Syntax

```
Action Exit Rules
    <Rule>
    …
```

## When Action Exit Rules Fire

Action Exit Rules execute:
- **After any action completes** - Create, Update, Delete, and custom actions
- **After Commit Rules** - After final validation and updates
- **Before transaction commit** - Still within the same transaction
- **Except for Set Actions** - Do not fire for Set Actions

## Execution Order

For any action (except Set Actions):

1. **Field Rules** - Field validation and defaults
2. **Type-Specific Rules** - Create/Update/Delete Rules
3. **Specific Action Rules** - Individual action logic
4. **Commit Rules** - Final validation and updates
5. **Type-Specific Exit Rules** - Create Exit Rules, etc.
6. **Action Exit Rules** - Universal post-action processing (fires here)
7. **Database Commit** - Changes persisted

## Use Cases

### Universal Notifications
- Send action completion notifications
- Alert monitoring systems
- Update dashboards
- Trigger external alerts

### Analytics and Metrics
- Log action events
- Update statistics
- Track usage patterns
- Measure performance

### External Integration
- Sync with external systems
- Send to message queues
- Update data warehouses
- Trigger webhooks

### Cache Management
- Clear relevant caches
- Refresh cached data
- Invalidate stale entries
- Update cache timestamps

### Audit and Compliance
- Create comprehensive audit trail
- Log all actions
- Track user activity
- Ensure compliance

## Examples

### Example 1: Log All Actions

```
Action Exit Rules
    log "Action completed: " + action.name + 
        " Record: " + record.id + 
        " User: " + current.user + 
        " Date: " + today
```

Logs every action execution.

### Example 2: Update Statistics

```
Action Exit Rules
    increment record.action.count
    record.last.action.date = today
    record.last.action.type = action.name
    record.last.action.user = current.user
```

Tracks action statistics on record.

### Example 3: Clear Cache

```
Action Exit Rules
    clear in-memory cache for RECORD_CACHE
    
    if affects.related.records
        clear in-memory cache for RELATED_CACHE
```

Invalidates caches after any action.

### Example 4: Send Completion Notification

```
Action Exit Rules
    if notify.on.action
        send notification
            to action.initiator
            description is "Action completed successfully"
            detail is action.name + " completed for record " + record.id
            priority is low
```

Notifies user of action completion.

### Example 5: Update Dashboard Metrics

```
Action Exit Rules
    invoke UPDATE.DASHBOARD_METRICS
        invoked.metric.type = "ActionCompleted"
        invoked.action.name = action.name
        invoked.record.type = record.type
        invoked.timestamp = current.time
```

Updates real-time dashboard.

### Example 6: Sync with External System

```
Action Exit Rules
    if sync.enabled
        send ion bod
            bod is action.result.xml
            bod type is "Sync.ActionCompleted"
            assign message id to sync.message.id
```

Synchronizes action results externally.

### Example 7: Create Audit Trail

```
Action Exit Rules
    invoke CREATE.ACTION_AUDIT
        invoked.record.id = record.id
        invoked.action.name = action.name
        invoked.user = current.user
        invoked.timestamp = today
        invoked.result = "Success"
```

Creates comprehensive audit entry.

### Example 8: Update Parent Record

```
Action Exit Rules
    if parent.record not empty
        parent.record.last.child.action = today
        parent.record.last.modified = today
        parent.record.last.modified.by = current.user
```

Updates parent with child action information.

### Example 9: Trigger Analytics Event

```
Action Exit Rules
    invoke CREATE.ANALYTICS_EVENT
        invoked.event.type = "ActionCompleted"
        invoked.action.name = action.name
        invoked.record.type = record.type
        invoked.user = current.user
        invoked.duration = action.duration
```

Logs analytics event for action.

### Example 10: Send to Message Queue

```
Action Exit Rules
    if message.queue.enabled
        invoke QUEUE_MESSAGE
            invoked.queue.name = "ActionEvents"
            invoked.message.type = action.name
            invoked.message.body = action.data.json
```

Sends action event to message queue.

### Example 11: Update Search Index

```
Action Exit Rules
    if searchable.record
        invoke UPDATE.SEARCH_INDEX
            invoked.record.id = record.id
            invoked.operation = "Update"
```

Updates search index after any action.

### Example 12: Notify Monitoring System

```
Action Exit Rules
    if monitoring.enabled
        invoke SEND.MONITORING_EVENT
            invoked.event.type = "ActionCompleted"
            invoked.record.id = record.id
            invoked.action.name = action.name
            invoked.success = true
```

Sends event to monitoring system.

### Example 13: Update Activity Log

```
Action Exit Rules
    invoke CREATE.ACTIVITY_LOG
        invoked.record.id = record.id
        invoked.activity.type = action.name
        invoked.user = current.user
        invoked.timestamp = today
        invoked.description = action.description
```

Maintains activity log for record.

### Example 14: Refresh Denormalized Data

```
Action Exit Rules
    if denormalized.data.affected
        for each dependent.record set
            each.cached.data = current.data
            each.cache.timestamp = today
```

Updates denormalized data in related records.

### Example 15: Trigger Workflow

```
Action Exit Rules
    if action.triggers.workflow
        initiate POST_ACTION_WORKFLOW workflow
            Variables
                record.id
                action.name
                completed.by = current.user
```

Initiates workflow after action completion.

### Example 16: Update Compliance Records

```
Action Exit Rules
    if compliance.tracking.required
        invoke CREATE.COMPLIANCE_RECORD
            invoked.record.id = record.id
            invoked.action = action.name
            invoked.user = current.user
            invoked.timestamp = today
            invoked.compliance.category = compliance.type
```

Creates compliance tracking record.

### Example 17: Send Email Notification

```
Action Exit Rules
    if email.notification.enabled
        send email
            to record.owner.email
            from system.email
            subject "Action Completed: " + action.name
            Contents
                "Action " + action.name + " completed successfully"
                "Record: " + record.id
                "Date: " + today
```

Sends email notification of action completion.

### Example 18: Update Aggregate Tables

```
Action Exit Rules
    invoke UPDATE.AGGREGATE_TABLES
        invoked.table.name = "ACTION_SUMMARY"
        invoked.operation = "Increment"
        invoked.key.values = aggregation.keys
```

Updates aggregate summary tables.

### Example 19: Log Performance Metrics

```
Action Exit Rules
    log "Performance: Action=" + action.name + 
        " Duration=" + action.duration + 
        " User=" + current.user
    
    if action.duration > performance.threshold
        send notification
            to performance.team
            description is "Slow action detected"
            priority is medium
```

Monitors and logs action performance.

### Example 20: Finalize Transaction Data

```
Action Exit Rules
    transaction.completion.time = current.time
    transaction.status = "Completed"
    transaction.result = "Success"
```

Records transaction completion information.

## Best Practices

1. **Use for Universal Operations**
   - Operations that apply to ALL actions
   - Avoid action-specific logic
   - Keep rules general and reusable
   - Document purpose clearly

2. **Keep Processing Light**
   - Minimize processing time
   - Use background processing for heavy operations
   - Avoid blocking operations
   - Consider performance impact

3. **Handle Errors Gracefully**
   - Don't fail transaction for non-critical operations
   - Use resume on error appropriately
   - Log errors for troubleshooting
   - Provide meaningful error messages

4. **Use Background Processing**
   - For external integrations
   - For heavy calculations
   - For non-critical operations
   - To avoid blocking user

5. **Maintain Transaction Integrity**
   - Remember rules are still in transaction
   - Failures will rollback entire action
   - Use commit transaction carefully
   - Consider transaction boundaries

6. **Document Side Effects**
   - Document external integrations
   - Note notification recipients
   - Describe triggered workflows
   - Explain analytics events

7. **Test Thoroughly**
   - Test with all action types
   - Verify execution order
   - Test error scenarios
   - Validate side effects

## Common Patterns

### Logging Pattern
```
Action Exit Rules
    log "Action: " + action.name + " completed"
```

### Notification Pattern
```
Action Exit Rules
    send notification
        to <stakeholder>
        description is "Action completed"
```

### Analytics Pattern
```
Action Exit Rules
    invoke CREATE.ANALYTICS_EVENT
        invoked.event.type = "ActionCompleted"
        invoked.action.name = action.name
```

### Cache Invalidation Pattern
```
Action Exit Rules
    clear in-memory cache for <cache.name>
```

### Audit Pattern
```
Action Exit Rules
    invoke CREATE.AUDIT_ENTRY
        invoked.action = action.name
        invoked.user = current.user
```

## Action Exit Rules vs Other Exit Rules

| Rule Type | Scope | Fires For |
|-----------|-------|-----------|
| Create Exit Rules | Create actions only | All Create actions |
| Action Exit Rules | All actions | All actions except Set Actions |

Both can coexist; Create Exit Rules fire first, then Action Exit Rules.

## Set Actions Exception

Action Exit Rules do NOT fire for Set Actions because:
- Set Actions process multiple instances
- Would fire once per instance (inefficient)
- Set Actions have their own rule structure
- Different execution model

For Set Actions, use Set Action-specific rules instead.

## Interaction with Transaction

Action Exit Rules execute:
- **Within transaction** - Still part of the same transaction
- **Before commit** - Database commit hasn't occurred yet
- **After all validation** - All validation has passed
- **Can cause rollback** - Errors will rollback entire action

**Important:** If Action Exit Rules fail, the entire action transaction rolls back.

## Available Context

Within Action Exit Rules, you have access to:

- `action.name` - Name of the action that executed
- `action.type` - Type of action (Create, Update, Delete, etc.)
- `current.user` - User who executed the action
- `today` - Current date
- `current.time` - Current timestamp
- All business class fields
- Action-specific context

## Troubleshooting

### Issue: Action Exit Rules not firing

**Possible Causes:**
- Set Action (rules don't fire for Set Actions)
- Syntax errors in rules
- Rules in wrong section
- Transaction rolled back before reaching exit rules

**Solutions:**
- Verify action type (not Set Action)
- Check rule syntax
- Verify rule location
- Check for earlier errors

### Issue: Transaction rolling back

**Possible Causes:**
- Error in Action Exit Rules
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

### Issue: Rules firing multiple times

**Possible Causes:**
- Multiple actions in transaction
- Nested action invocations
- Misunderstanding of execution model

**Solutions:**
- Review action invocation logic
- Check for nested actions
- Understand execution order
- Add guards if needed

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

- [Create Exit Rules](create-exit-rules.md) - Post-creation processing
- [Commit Rules](commit-rules.md) - Final transaction rules
- [Action Rules](action-rules.md) - Action-specific rules
- [Field Rules](field-rules.md) - Field-level rules
- [Business Class Definition](business-class-definition.md) - Business class structure

## Notes

- Action Exit Rules fire for all actions except Set Actions
- Rules execute after all other action logic but before database commit
- Still within the same transaction; failures cause rollback
- Ideal for universal post-action notifications and integrations
- Use background processing for heavy or non-critical operations
- Keep rules efficient to avoid performance impact
- Test with all action types in the business class
- Document external integrations and side effects
- Consider using resume on error for non-critical operations
- Remember that errors in Action Exit Rules will rollback the entire action
- Use for operations that should occur after any action completes
- Avoid operations that could fail and cause unnecessary rollbacks
- Action Exit Rules fire after type-specific exit rules (like Create Exit Rules)
- Do not use for Set Action processing; use Set Action rules instead
