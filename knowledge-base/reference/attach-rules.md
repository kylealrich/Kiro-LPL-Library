# Attach Rules

## Overview

Attach Rules define business logic that executes when another business object is seeking to attach itself to this object. They control whether the attachment is allowed and what actions should occur during the attachment process. These rules are essential for managing relationships and enforcing attachment constraints.

## Syntax

```
Attach Rules
    <Rule>
    …
```

## When Attach Rules Fire

Attach Rules execute:
- **When another object attempts to attach** - During attachment operation
- **Before attachment is established** - Before relationship is created
- **On the target object** - Rules defined on the object being attached to
- **During relationship creation** - When foreign key is being set

## Rule Constraints

- **Only general rules allowed** - Rules valid in all contexts
- **No Field Rules** - Cannot use field-specific rules
- **Constraint rules prevent attachment** - Any constraint violation blocks attachment
- **Simple validation focus** - Keep rules straightforward

## Use Cases

### Attachment Validation
- Verify attachment is allowed
- Check business rules
- Validate state compatibility
- Enforce attachment limits

### Attachment Constraints
- Limit number of attachments
- Restrict attachment types
- Enforce status requirements
- Validate permissions

### Attachment Logging
- Log attachment attempts
- Track relationship creation
- Audit attachment activity
- Monitor attachment patterns

### Attachment Notifications
- Alert on new attachments
- Notify stakeholders
- Trigger workflows
- Update dashboards

### Attachment Counters
- Update attachment counts
- Track relationship metrics
- Maintain statistics
- Monitor capacity

## Examples

### Example 1: Validate Attachment Allowed

```
Attach Rules
    constraint status = "Active"
        "Cannot attach to inactive records"
    
    constraint not locked
        "Cannot attach to locked records"
```

Prevents attachment to inactive or locked records.

### Example 2: Limit Number of Attachments

```
Attach Rules
    constraint attachment.count < maximum.attachments
        "Maximum number of attachments reached"
```

Enforces attachment limit.

### Example 3: Validate Attachment Type

```
Attach Rules
    constraint attaching.object.type in allowed.types
        "This type of object cannot be attached"
```

Restricts which object types can attach.

### Example 4: Check Permissions

```
Attach Rules
    constraint current.user.has.permission("AttachObjects")
        "You do not have permission to attach objects"
```

Enforces attachment permissions.

### Example 5: Validate State Compatibility

```
Attach Rules
    if attaching.object.status = "Draft"
        constraint status != "Approved"
            "Cannot attach draft objects to approved records"
```

Ensures state compatibility between objects.

### Example 6: Log Attachment

```
Attach Rules
    log "Attachment attempt: " + attaching.object.id + 
        " to " + record.id + " by " + current.user
```

Logs attachment attempts.

### Example 7: Update Attachment Counter

```
Attach Rules
    increment attachment.count
    last.attachment.date = today
```

Tracks attachment statistics.

### Example 8: Notify on Attachment

```
Attach Rules
    send notification
        to record.owner
        description is "New object attached"
        detail is "Object " + attaching.object.id + " attached by " + 
                  current.user
        priority is low
```

Notifies owner of new attachment.

### Example 9: Validate Capacity

```
Attach Rules
    constraint available.capacity >= attaching.object.size
        "Insufficient capacity for attachment"
```

Checks capacity before allowing attachment.

### Example 10: Enforce Business Rules

```
Attach Rules
    if record.type = "Restricted"
        constraint attaching.object.security.level >= "High"
            "Only high-security objects can attach to restricted records"
```

Enforces security-based attachment rules.

### Example 11: Validate Date Constraints

```
Attach Rules
    constraint attaching.object.effective.date >= record.start.date
        "Attached object effective date must be on or after record start date"
    
    if record.end.date not empty
        constraint attaching.object.effective.date <= record.end.date
            "Attached object effective date must be on or before record end date"
```

Validates temporal compatibility.

### Example 12: Check Duplicate Attachments

```
Attach Rules
    constraint not exists(duplicate.attachment)
        "This object is already attached"
```

Prevents duplicate attachments.

### Example 13: Validate Hierarchy

```
Attach Rules
    constraint attaching.object.level = record.level + 1
        "Attached object must be one level below in hierarchy"
    
    constraint attaching.object not in ancestors
        "Cannot create circular attachment"
```

Enforces hierarchical attachment rules.

### Example 14: Conditional Attachment

```
Attach Rules
    if record.requires.approval
        constraint record.approval.status = "Approved"
            "Record must be approved before attachments allowed"
```

Requires approval before allowing attachments.

### Example 15: Validate Ownership

```
Attach Rules
    if strict.ownership.mode
        constraint attaching.object.owner = record.owner
            "Attached object must have same owner"
```

Enforces ownership consistency.

### Example 16: Check Resource Limits

```
Attach Rules
    constraint department.attachment.quota.available > 0
        "Department attachment quota exceeded"
    
    decrement department.attachment.quota.available
```

Manages department-level quotas.

### Example 17: Validate Category Compatibility

```
Attach Rules
    constraint attaching.object.category compatible.with record.category
        "Incompatible categories for attachment"
```

Ensures category compatibility.

### Example 18: Enforce Temporal Rules

```
Attach Rules
    if record.is.historical
        constraint false
            "Cannot attach objects to historical records"
```

Prevents attachment to historical records.

### Example 19: Validate Financial Rules

```
Attach Rules
    if record.type = "Financial"
        constraint fiscal.period.status = "Open"
            "Cannot attach to records in closed fiscal periods"
```

Enforces financial period rules.

### Example 20: Complex Validation

```
Attach Rules
    constraint (status = "Active") and 
               (attachment.count < maximum.attachments) and
               (current.user.has.permission("AttachObjects"))
        "Attachment not allowed: check status, limits, and permissions"
```

Combines multiple validation criteria.

## Best Practices

1. **Keep Rules Simple**
   - Use straightforward validation
   - Avoid complex logic
   - Focus on constraints
   - Provide clear error messages

2. **Validate Early**
   - Check critical constraints first
   - Fail fast on violations
   - Provide specific error messages
   - Guide user to resolution

3. **Use Constraints Effectively**
   - Any constraint violation blocks attachment
   - Combine related checks
   - Provide actionable feedback
   - Document validation rules

4. **Track Attachments**
   - Update counters
   - Log attachment activity
   - Maintain statistics
   - Monitor patterns

5. **Enforce Limits**
   - Set reasonable limits
   - Check capacity
   - Validate quotas
   - Prevent overload

6. **Consider Performance**
   - Keep rules efficient
   - Avoid heavy queries
   - Minimize processing
   - Test with load

7. **Document Rules**
   - Explain validation logic
   - Note business rules
   - Describe constraints
   - Provide examples

## Common Patterns

### Basic Validation Pattern
```
Attach Rules
    constraint <validation.condition>
        <error.message>
```

### Limit Enforcement Pattern
```
Attach Rules
    constraint <count.field> < <maximum>
        "Maximum attachments reached"
```

### Permission Check Pattern
```
Attach Rules
    constraint current.user.has.permission(<permission>)
        "Permission denied"
```

### State Validation Pattern
```
Attach Rules
    constraint status in <allowed.states>
        "Invalid state for attachment"
```

### Counter Update Pattern
```
Attach Rules
    increment <attachment.counter>
    <timestamp.field> = today
```

## Attach Rules vs Parent Attach Rules

| Aspect | Attach Rules | Parent Attach Rules |
|--------|-------------|---------------------|
| Context | Target object | Parent object |
| Trigger | Object being attached to | Parent in hierarchy |
| Use Case | General attachments | Hierarchical attachments |
| Scope | Any attachment | Parent-child relationships |

## Understanding Attachment Context

When Attach Rules fire:
- **This object** - The object being attached to (target)
- **Attaching object** - The object seeking to attach (source)
- **Current user** - User performing the attachment
- **Relationship** - The relationship being established

## Constraint Behavior

**Critical:** Any constraint rule that fails prevents the attachment:

```
Attach Rules
    constraint status = "Active"
        "Cannot attach to inactive records"
```

If `status != "Active"`, the attachment is blocked and the error message is displayed.

## Troubleshooting

### Issue: Attachment always blocked

**Possible Causes:**
- Constraint always fails
- Condition too strict
- Data not available
- Logic error

**Solutions:**
- Review constraint logic
- Check data values
- Verify conditions
- Test with various scenarios

### Issue: Attachment allowed when shouldn't be

**Possible Causes:**
- Missing constraint
- Condition too loose
- Logic error
- Validation not comprehensive

**Solutions:**
- Add missing constraints
- Tighten conditions
- Review validation logic
- Test edge cases

### Issue: Unclear error messages

**Possible Causes:**
- Generic error messages
- Missing context
- Vague descriptions
- No guidance

**Solutions:**
- Provide specific messages
- Include relevant context
- Explain what's wrong
- Suggest corrective action

### Issue: Performance problems

**Possible Causes:**
- Heavy validation logic
- Complex queries
- Inefficient checks
- Many attachments

**Solutions:**
- Optimize validation
- Use efficient queries
- Simplify checks
- Add indexes if needed

### Issue: Counters not updating

**Possible Causes:**
- Constraint failing after counter update
- Transaction rollback
- Logic error
- Timing issue

**Solutions:**
- Update counters after validation
- Check for constraint failures
- Review transaction flow
- Test thoroughly

## Related Concepts

- [Parent Attach Rules](parent-attach-rules.md) - Parent-specific attachment rules
- [Relations](relations.md) - Relationship definitions
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Conditions](conditions.md) - Boolean expressions
- [Field Rules](field-rules.md) - Field-level rules

## Notes

- Attach Rules fire when another object attempts to attach to this object
- Only general rules are allowed; no field-specific rules
- Any constraint violation prevents the attachment
- Rules execute before the attachment relationship is established
- Keep rules simple and focused on validation
- Provide clear, actionable error messages
- Update counters and statistics as needed
- Log attachment activity for audit trail
- Consider performance impact of validation logic
- Test with various attachment scenarios
- Document attachment constraints clearly
- Attach Rules are defined on the target object (being attached to)
- Use constraints to enforce business rules
- Combine multiple checks in single constraint when appropriate
- Remember that constraint failure blocks the entire attachment operation
