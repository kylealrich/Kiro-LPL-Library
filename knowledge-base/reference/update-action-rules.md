# Update Action Rules

## Overview

Update Action Rules define business logic that executes when any Update-type action is executed. They provide a centralized location for logic that should apply to all record update scenarios, regardless of which specific Update action is invoked. These rules are essential for enforcing consistent update behavior across the business class.

## Syntax

```
Update Action Rules
    <Rule>
    …
```

## When Update Action Rules Fire

Update Action Rules execute:
- **During any Update action** - Regardless of which Update action is invoked
- **After field validation** - After field-level rules execute
- **Before action-specific rules** - Before individual Update action rules
- **Before commit** - Before Commit Rules execute

## Execution Order

For an Update action:

1. **Field Rules** - Field validation and defaults
2. **Update Action Rules** - Common update logic (fires here)
3. **Specific Update Action Rules** - Action-specific logic
4. **Commit Rules** - Final validation and updates
5. **Database Commit** - Changes persisted

## Use Cases

### Change Tracking
- Track field changes
- Record modification history
- Log significant updates
- Maintain audit trail

### Update Validation
- Enforce update constraints
- Validate business rules
- Check state transitions
- Verify permissions

### Automatic Updates
- Update timestamps
- Increment version numbers
- Recalculate derived fields
- Synchronize related data

### Notifications
- Alert on changes
- Notify stakeholders
- Trigger workflows
- Send update confirmations

### State Management
- Validate state transitions
- Update status fields
- Manage workflow states
- Track lifecycle changes

## Examples

### Example 1: Update Timestamps

```
Update Action Rules
    update.date = today
    update.time = current.time
    updated.by = current.user
    
    increment update.count
```

Tracks when and by whom record was updated.

### Example 2: Version Control

```
Update Action Rules
    if significant.fields.changed
        increment version.number
        previous.version.date = version.date
        version.date = today
```

Manages version numbers for significant changes.

### Example 3: Change Validation

```
Update Action Rules
    if status.changed
        constraint valid.status.transition(previous.status, status)
            "Invalid status transition from " + previous.status + 
            " to " + status
```

Validates state transitions.

### Example 4: Track Field Changes

```
Update Action Rules
    if price.changed
        price.change.date = today
        price.change.amount = price - previous.price
        price.change.percent = 
            ((price - previous.price) / previous.price) * 100
        
        log "Price changed from " + previous.price + " to " + price
```

Records price change details.

### Example 5: Notify on Changes

```
Update Action Rules
    if critical.fields.changed
        send notification
            to record.owner
            description is "Critical fields updated"
            detail is "Record " + record.id + " modified by " + current.user
            priority is high
```

Alerts owner of critical changes.

### Example 6: Recalculate Derived Fields

```
Update Action Rules
    if quantity.changed or unit.price.changed
        line.total = quantity * unit.price
        
        if discount.percent not empty
            discount.amount = line.total * (discount.percent / 100)
            final.amount = line.total - discount.amount
        else
            final.amount = line.total
```

Recalculates totals when components change.

### Example 7: Synchronize Related Records

```
Update Action Rules
    if department.changed
        for each subordinate.employee set
            each.department = department
            each.department.change.date = today
```

Cascades department changes to subordinates.

### Example 8: Validate Permissions

```
Update Action Rules
    if protected.fields.changed
        constraint current.user.has.permission("ModifyProtected")
            "You do not have permission to modify protected fields"
```

Enforces field-level security.

### Example 9: Update Parent Aggregates

```
Update Action Rules
    if amount.changed
        parent.total += (amount - previous.amount)
        parent.last.modified = today
```

Updates parent totals when child amounts change.

### Example 10: Trigger Workflow

```
Update Action Rules
    if status = "Submitted" and previous.status = "Draft"
        initiate APPROVAL_WORKFLOW workflow
            Variables
                record.id
                submitted.by = current.user
                submit.date = today
```

Starts workflow on status change.

### Example 11: Maintain History

```
Update Action Rules
    if significant.fields.changed
        invoke CREATE.CHANGE_HISTORY
            invoked.record.id = record.id
            invoked.change.date = today
            invoked.changed.by = current.user
            invoked.change.description = change.summary
```

Creates history record for significant changes.

### Example 12: Validate Business Rules

```
Update Action Rules
    if effective.date.changed
        constraint effective.date >= create.date
            "Effective date cannot be before creation date"
        
        constraint effective.date <= today + 365 days
            "Effective date cannot be more than one year in future"
```

Enforces business rules on updates.

### Example 13: Update Search Index

```
Update Action Rules
    if searchable.fields.changed
        build text search field SEARCH_INDEX
            Fields
            name
            description
            keywords
            category.name
```

Rebuilds search index when searchable fields change.

### Example 14: Conditional Validation

```
Update Action Rules
    if status = "Approved"
        constraint approved.by not empty
            "Approver required for approved status"
        
        constraint approval.date not empty
            "Approval date required"
        
        constraint approval.date <= today
            "Approval date cannot be in future"
```

Validates required fields for specific status.

### Example 15: Track Modification Patterns

```
Update Action Rules
    if frequent.changes.detected
        record.volatility.score += 1
        
        if record.volatility.score > threshold
            record.requires.review = true
            
            send notification
                to data.quality.team
                description is "High volatility detected"
```

Monitors and flags frequently-changed records.

### Example 16: Synchronize External Systems

```
Update Action Rules
    if sync.required.fields.changed
        send ion bod
            bod is record.xml
            bod type is "Sync.RecordUpdate"
            assign message id to sync.message.id
```

Syncs changes with external systems.

### Example 17: Update Denormalized Data

```
Update Action Rules
    if name.changed or description.changed
        for each related.reference set
            each.cached.name = name
            each.cached.description = description
            each.cache.update.date = today
```

Maintains denormalized data in related records.

### Example 18: Validate Concurrent Updates

```
Update Action Rules
    constraint update.stamp = original.update.stamp
        "Record has been modified by another user. Please refresh and try again."
```

Implements optimistic locking.

### Example 19: Calculate Change Magnitude

```
Update Action Rules
    change.magnitude = 0
    
    for each changed.field
        if each.field.weight not empty
            change.magnitude += each.field.weight
    
    if change.magnitude > significant.threshold
        requires.manager.review = true
```

Calculates and evaluates change significance.

### Example 20: Update Lifecycle State

```
Update Action Rules
    if first.update and status = "Active"
        lifecycle.state = "In Use"
        first.use.date = today
    
    last.activity.date = today
```

Tracks lifecycle milestones.

## Best Practices

1. **Track Changes Consistently**
   - Update modification timestamps
   - Record modifier information
   - Log significant changes
   - Maintain change history

2. **Validate Updates**
   - Enforce business rules
   - Check state transitions
   - Validate permissions
   - Verify data consistency

3. **Keep Rules Efficient**
   - Minimize processing
   - Avoid heavy calculations
   - Use efficient queries
   - Consider performance impact

4. **Handle Concurrent Updates**
   - Implement optimistic locking
   - Validate update stamps
   - Handle conflicts gracefully
   - Provide clear error messages

5. **Maintain Data Integrity**
   - Synchronize related records
   - Update aggregates correctly
   - Maintain denormalized data
   - Ensure referential integrity

6. **Use Conditional Logic**
   - Check if fields actually changed
   - Use `field.changed` conditions
   - Avoid unnecessary processing
   - Optimize rule execution

7. **Document Change Logic**
   - Explain validation rules
   - Note synchronization behavior
   - Describe notification triggers
   - Document side effects

## Common Patterns

### Timestamp Pattern
```
Update Action Rules
    update.date = today
    updated.by = current.user
    increment update.count
```

### Change Tracking Pattern
```
Update Action Rules
    if <field>.changed
        <field>.change.date = today
        <field>.previous.value = previous.<field>
```

### Validation Pattern
```
Update Action Rules
    constraint <business.rule>
        <error.message>
```

### Notification Pattern
```
Update Action Rules
    if <significant.change>
        send notification
            to <stakeholder>
            description is <change.description>
```

### Synchronization Pattern
```
Update Action Rules
    if <field>.changed
        <related.record>.<field> = <field>
```

## Update Action Rules vs Other Rules

| Rule Type | Execution Timing | Purpose |
|-----------|-----------------|---------|
| Field Rules | During field validation | Field-level logic |
| Update Action Rules | Before action-specific rules | Common update logic |
| Specific Update Action Rules | During action execution | Action-specific logic |
| Commit Rules | Before commit | Final validation |

## Checking for Changes

Update Action Rules commonly use change detection:

```
if <field>.changed
    // Field value has changed
    previous.<field>  // Access previous value
    <field>          // Access new value
```

**Available Change Checks:**
- `<field>.changed` - Boolean indicating if field changed
- `previous.<field>` - Previous value before update
- `<field>` - Current/new value

## Troubleshooting

### Issue: Update Action Rules not firing

**Possible Causes:**
- Not an Update action
- Syntax errors in rules
- Rules in wrong section
- No fields actually changed

**Solutions:**
- Verify action type
- Check rule syntax
- Verify rule location
- Check if fields changed

### Issue: Rules firing when no changes

**Possible Causes:**
- Not checking for field changes
- Condition always true
- Field touched but not changed
- Logic error

**Solutions:**
- Use `field.changed` conditions
- Review condition logic
- Check field comparison
- Test with various scenarios

### Issue: Concurrent update conflicts

**Possible Causes:**
- No optimistic locking
- Update stamp not checked
- High contention
- Long transactions

**Solutions:**
- Implement update stamp validation
- Use optimistic locking
- Minimize transaction duration
- Handle conflicts gracefully

### Issue: Performance degradation

**Possible Causes:**
- Heavy processing on every update
- Complex calculations
- Many related record updates
- Inefficient queries

**Solutions:**
- Check for changes before processing
- Optimize calculations
- Batch related updates
- Use efficient queries

### Issue: Related records not synchronized

**Possible Causes:**
- Incorrect related link
- Condition not met
- Transaction isolation
- Locking conflicts

**Solutions:**
- Verify related link references
- Check condition logic
- Review transaction isolation
- Handle locking appropriately

## Related Concepts

- [Create Rules](create-rules.md) - Common creation logic
- [Field Rules](field-rules.md) - Field-level rules
- [Commit Rules](commit-rules.md) - Final transaction rules
- [Action Rules](action-rules.md) - Action-specific rules
- [Business Class Definition](business-class-definition.md) - Business class structure

## Notes

- Update Action Rules fire for ALL Update-type actions
- Rules execute after field validation but before action-specific rules
- Use Update Action Rules for logic common to all update scenarios
- Action-specific Update action rules can override or extend Update Action Rules
- Always check if fields actually changed before processing
- Use `field.changed` to detect changes
- Access previous values with `previous.field`
- Keep Update Action Rules general and avoid action-specific logic
- Update Action Rules are ideal for change tracking, validation, and synchronization
- Test Update Action Rules with all Update actions in the business class
- Consider performance impact since rules fire on every update
- Implement optimistic locking to handle concurrent updates
- Document change tracking and synchronization logic clearly
