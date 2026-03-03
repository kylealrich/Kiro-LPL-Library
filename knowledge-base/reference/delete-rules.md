# Delete Rules

## Overview

Delete Rules define business logic that executes when any Delete-type action is executed. They provide a centralized location for logic that should apply to all record deletion scenarios, regardless of which specific Delete action is invoked. These rules are essential for enforcing consistent deletion behavior, validating delete operations, and managing cascading effects.

## Syntax

```
Delete Rules
    <Rule>
    …
```

## When Delete Rules Fire

Delete Rules execute:
- **During any Delete action** - Regardless of which Delete action is invoked
- **Before actual deletion** - Before record is removed from database
- **Before action-specific rules** - Before individual Delete action rules
- **Before commit** - Before Commit Rules execute

## Execution Order

For a Delete action:

1. **Field Rules** - Field validation (if applicable)
2. **Delete Rules** - Common deletion logic (fires here)
3. **Specific Delete Action Rules** - Action-specific logic
4. **Commit Rules** - Final validation and updates
5. **Database Delete** - Record removed (or soft-deleted)

## Use Cases

### Delete Validation
- Verify delete permissions
- Check for dependencies
- Validate business rules
- Prevent invalid deletions

### Cascade Operations
- Delete related records
- Clean up dependencies
- Remove references
- Archive related data

### Update Related Records
- Update parent counters
- Adjust aggregates
- Synchronize related data
- Maintain referential integrity

### Audit and Logging
- Log deletion events
- Create audit trail
- Archive deleted data
- Track deletion reasons

### Notifications
- Alert stakeholders
- Notify affected users
- Trigger workflows
- Send deletion confirmations

## Examples

### Example 1: Validate Delete Permission

```
Delete Rules
    constraint current.user.has.permission("DeleteRecords")
        "You do not have permission to delete records"
    
    if record.status = "Approved"
        constraint current.user.has.permission("DeleteApproved")
            "You do not have permission to delete approved records"
```

Enforces delete permissions based on record state.

### Example 2: Check for Dependencies

```
Delete Rules
    constraint not exists(child.records)
        "Cannot delete record with existing child records"
    
    constraint not exists(active.references)
        "Cannot delete record that is actively referenced"
```

Prevents deletion when dependencies exist.

### Example 3: Archive Before Delete

```
Delete Rules
    invoke CREATE.DELETED_RECORD_ARCHIVE
        invoked.record.id = record.id
        invoked.record.data = record.snapshot
        invoked.deleted.by = current.user
        invoked.deleted.date = today
        invoked.deletion.reason = deletion.reason
```

Archives record data before deletion.

### Example 4: Cascade Delete

```
Delete Rules
    for each child.record set
        invoke DELETE.CHILD_RECORD each
```

Deletes all child records.

### Example 5: Update Parent Counters

```
Delete Rules
    decrement parent.child.count
    parent.last.modified = today
    
    if parent.child.count = 0
        parent.status = "Empty"
```

Updates parent record statistics.

### Example 6: Log Deletion

```
Delete Rules
    log "Record deleted: " + record.id + " by " + current.user + 
        " Reason: " + deletion.reason
    
    invoke CREATE.DELETION_LOG
        invoked.record.id = record.id
        invoked.record.type = record.type
        invoked.deleted.by = current.user
        invoked.deletion.date = today
```

Creates comprehensive deletion audit trail.

### Example 7: Notify Stakeholders

```
Delete Rules
    send notification
        to record.owner
        description is "Record deleted"
        detail is "Record " + record.id + " has been deleted by " + 
                  current.user
        priority is medium
```

Notifies owner of record deletion.

### Example 8: Validate Business Rules

```
Delete Rules
    constraint deletion.reason not empty
        "Deletion reason is required"
    
    if record.value > 10000
        constraint manager.approval not empty
            "Manager approval required to delete high-value records"
```

Enforces business rules for deletion.

### Example 9: Clean Up References

```
Delete Rules
    for each referencing.record set
        each.reference.id = null
        each.reference.deleted = true
        each.reference.deleted.date = today
```

Cleans up references in related records.

### Example 10: Update Aggregates

```
Delete Rules
    department.total.amount -= record.amount
    department.record.count -= 1
    department.last.modified = today
```

Adjusts department aggregates.

### Example 11: Soft Delete Implementation

```
Delete Rules
    if soft.delete.enabled
        record.deleted = true
        record.deleted.date = today
        record.deleted.by = current.user
        
        // Prevent actual deletion
        constraint false
            "Record marked as deleted"
```

Implements soft delete pattern.

### Example 12: Validate State

```
Delete Rules
    constraint status in ("Draft", "Cancelled")
        "Only draft or cancelled records can be deleted"
    
    constraint not locked
        "Cannot delete locked records"
```

Validates record state before deletion.

### Example 13: Sync External Systems

```
Delete Rules
    send ion bod
        bod is deletion.notification.xml
        bod type is "Sync.RecordDelete"
        assign message id to sync.message.id
```

Notifies external systems of deletion.

### Example 14: Remove File Attachments

```
Delete Rules
    for each attachment set
        invoke DELETE.ATTACHMENT each
            invoked.delete.physical.file = true
```

Deletes associated file attachments.

### Example 15: Update Search Index

```
Delete Rules
    invoke REMOVE_FROM_SEARCH_INDEX
        invoked.record.id = record.id
        invoked.index.name = "MAIN_INDEX"
```

Removes record from search index.

### Example 16: Validate Timing

```
Delete Rules
    if effective.date <= today
        constraint days.since.effective >= minimum.retention.days
            "Record must be retained for at least " + 
            minimum.retention.days + " days"
```

Enforces retention policies.

### Example 17: Conditional Cascade

```
Delete Rules
    if cascade.delete.enabled
        for each dependent.record set
            if each.can.be.deleted
                invoke DELETE.DEPENDENT each
            else
                each.parent.reference = null
```

Conditionally cascades deletions.

### Example 18: Financial Validation

```
Delete Rules
    if record.type = "Financial"
        constraint fiscal.period.status = "Open"
            "Cannot delete financial records in closed periods"
        
        constraint not reconciled
            "Cannot delete reconciled financial records"
```

Enforces financial controls.

### Example 19: Workflow Cancellation

```
Delete Rules
    if active.workflow not empty
        cancel active.workflow workflow
            message id is workflow.message.id
        
        log "Workflow cancelled due to record deletion"
```

Cancels active workflows.

### Example 20: Compliance Logging

```
Delete Rules
    if compliance.relevant
        invoke CREATE.COMPLIANCE_LOG
            invoked.action = "Delete"
            invoked.record.id = record.id
            invoked.user = current.user
            invoked.timestamp = today
            invoked.justification = deletion.reason
            invoked.approval = deletion.approval
```

Creates compliance audit trail.

## Best Practices

1. **Validate Before Deleting**
   - Check permissions
   - Verify no dependencies
   - Validate business rules
   - Confirm deletion is allowed

2. **Archive Important Data**
   - Save record snapshot
   - Preserve audit trail
   - Archive related data
   - Document deletion reason

3. **Handle Dependencies**
   - Check for child records
   - Validate references
   - Cascade or prevent deletion
   - Clean up orphaned data

4. **Update Related Records**
   - Adjust parent counters
   - Update aggregates
   - Synchronize related data
   - Maintain consistency

5. **Provide Clear Feedback**
   - Explain why deletion failed
   - Suggest corrective actions
   - Confirm successful deletion
   - Notify affected users

6. **Consider Soft Delete**
   - For recoverable deletions
   - When audit trail is critical
   - For compliance requirements
   - When references must persist

7. **Test Thoroughly**
   - Test with dependencies
   - Verify cascade behavior
   - Test permission checks
   - Validate error messages

## Common Patterns

### Validation Pattern
```
Delete Rules
    constraint <can.delete.condition>
        <error.message>
```

### Archive Pattern
```
Delete Rules
    invoke CREATE.ARCHIVE
        invoked.record.data = <snapshot>
        invoked.deleted.by = current.user
```

### Cascade Pattern
```
Delete Rules
    for each <child.set>
        invoke DELETE.<child.action> each
```

### Update Parent Pattern
```
Delete Rules
    decrement <parent.counter>
    <parent.timestamp> = today
```

### Soft Delete Pattern
```
Delete Rules
    <deleted.flag> = true
    <deleted.date> = today
    constraint false
        "Record marked as deleted"
```

## Hard Delete vs Soft Delete

### Hard Delete
- Record physically removed from database
- Cannot be recovered
- Frees storage space
- May break references

### Soft Delete
- Record marked as deleted but remains in database
- Can be recovered
- Maintains references
- Requires filtering in queries

**Implementation:**
```
Delete Rules
    if use.soft.delete
        deleted = true
        deleted.date = today
        deleted.by = current.user
        constraint false  // Prevents actual deletion
            "Record marked as deleted"
```

## Delete Rules vs Relation Delete Behavior

| Mechanism | Purpose | Scope |
|-----------|---------|-------|
| Delete Rules | Business logic for deletion | Current record |
| delete restricted | Prevent deletion if children exist | Relation-level |
| delete cascades | Automatically delete children | Relation-level |

Both can be used together for comprehensive delete management.

## Troubleshooting

### Issue: Delete Rules not firing

**Possible Causes:**
- Not a Delete action
- Syntax errors in rules
- Rules in wrong section
- Soft delete preventing execution

**Solutions:**
- Verify action type
- Check rule syntax
- Verify rule location
- Review soft delete logic

### Issue: Cannot delete record

**Possible Causes:**
- Constraint violation
- Dependencies exist
- Permission denied
- Business rule violation

**Solutions:**
- Review constraint messages
- Check for child records
- Verify permissions
- Validate business rules

### Issue: Cascade delete failing

**Possible Causes:**
- Child records have dependencies
- Permission issues
- Circular references
- Transaction timeout

**Solutions:**
- Check child dependencies
- Verify permissions
- Handle circular references
- Optimize cascade logic

### Issue: Related records not updated

**Possible Causes:**
- Incorrect related link
- Transaction rolled back
- Locking conflicts
- Logic error

**Solutions:**
- Verify related links
- Check for errors
- Handle locking
- Review update logic

### Issue: Soft delete not working

**Possible Causes:**
- Constraint not preventing deletion
- Flag not set correctly
- Logic error
- Configuration issue

**Solutions:**
- Verify constraint logic
- Check flag assignment
- Review soft delete implementation
- Test thoroughly

## Related Concepts

- [Create Rules](create-rules.md) - Common creation logic
- [Update Action Rules](update-action-rules.md) - Common update logic
- [Relations](relations.md) - Delete cascades and restrictions
- [Commit Rules](commit-rules.md) - Final transaction rules
- [Business Class Definition](business-class-definition.md) - Business class structure

## Notes

- Delete Rules fire for ALL Delete-type actions
- Rules execute before actual deletion occurs
- Use Delete Rules for logic common to all deletion scenarios
- Action-specific Delete action rules can extend Delete Rules
- Always validate before allowing deletion
- Consider archiving important data before deletion
- Handle dependencies appropriately (cascade or prevent)
- Update related records to maintain consistency
- Provide clear error messages for failed deletions
- Test delete behavior thoroughly with various scenarios
- Consider implementing soft delete for recoverable deletions
- Document deletion policies and procedures
- Ensure compliance with data retention requirements
- Log deletions for audit trail
- Notify affected stakeholders of deletions
