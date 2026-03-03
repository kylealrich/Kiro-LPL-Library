# Dynamic Creation Rules

## Overview

Dynamic Creation Rules define business logic that executes when a Dynamic Creation is about to occur. These rules control whether the dynamic creation is allowed and what actions should occur during the creation process. They are essential for managing on-demand record creation and enforcing creation constraints.

## Syntax

```
Dynamic Creation Rules
    <Rule>
    …
```

## When Dynamic Creation Rules Fire

Dynamic Creation Rules execute:
- **Before dynamic creation occurs** - When system attempts to create record dynamically
- **On-demand creation** - When record is needed but doesn't exist
- **Before record is created** - Before the new instance is instantiated
- **During relationship traversal** - When following a relation that requires creation

## Rule Constraints

- **Constraint rules prevent creation** - Any constraint violation blocks dynamic creation
- **Exception thrown on failure** - Constraint failure causes exception
- **Access to context** - Use `datacontext` to access triggering business class
- **Validation focus** - Primarily used for validation and authorization

## DataContext Variable

Both Attach Rules and Dynamic Creation Rules can access contextual information about the business class causing these rules to fire through the special variable `datacontext`:

```
Dynamic Creation Rules
    constraint datacontext.status = "Active"
        "Cannot dynamically create from inactive context"
```

The `datacontext` variable provides access to the business object that triggered the dynamic creation.

## Use Cases

### Creation Validation
- Verify creation is allowed
- Check permissions
- Validate business rules
- Enforce creation constraints

### Context Validation
- Validate triggering context
- Check context state
- Verify context permissions
- Ensure context eligibility

### Creation Authorization
- Check user permissions
- Validate security rules
- Enforce access control
- Verify creation rights

### Creation Limits
- Enforce creation quotas
- Check capacity limits
- Validate resource availability
- Prevent excessive creation

### Creation Logging
- Log dynamic creation attempts
- Track creation patterns
- Audit creation activity
- Monitor creation frequency

## Examples

### Example 1: Validate Context Status

```
Dynamic Creation Rules
    constraint datacontext.status = "Active"
        "Cannot dynamically create records from inactive context"
    
    constraint datacontext.approved = true
        "Context must be approved for dynamic creation"
```

Ensures triggering context is in valid state.

### Example 2: Check Creation Permission

```
Dynamic Creation Rules
    constraint current.user.has.permission("DynamicCreate")
        "You do not have permission to dynamically create records"
```

Enforces creation permissions.

### Example 3: Validate Context Type

```
Dynamic Creation Rules
    constraint datacontext.type in allowed.context.types
        "Dynamic creation not allowed from this context type"
```

Restricts which contexts can trigger dynamic creation.

### Example 4: Enforce Creation Limits

```
Dynamic Creation Rules
    constraint dynamic.creation.count < maximum.dynamic.creations
        "Maximum dynamic creation limit reached"
```

Prevents excessive dynamic creation.

### Example 5: Log Dynamic Creation

```
Dynamic Creation Rules
    log "Dynamic creation triggered by: " + datacontext.id + 
        " User: " + current.user + " Date: " + today
```

Logs dynamic creation attempts.

### Example 6: Validate Business Rules

```
Dynamic Creation Rules
    if datacontext.requires.approval
        constraint datacontext.approval.status = "Approved"
            "Context must be approved before dynamic creation"
```

Enforces approval requirements.

### Example 7: Check Resource Availability

```
Dynamic Creation Rules
    constraint system.resources.available
        "Insufficient system resources for dynamic creation"
    
    constraint database.connections.available > minimum.connections
        "Insufficient database connections"
```

Validates resource availability.

### Example 8: Validate Temporal Constraints

```
Dynamic Creation Rules
    constraint datacontext.effective.date <= today
        "Cannot dynamically create from future-dated context"
    
    if datacontext.end.date not empty
        constraint today <= datacontext.end.date
            "Context has expired; dynamic creation not allowed"
```

Ensures temporal validity.

### Example 9: Enforce Security Rules

```
Dynamic Creation Rules
    constraint datacontext.security.level <= current.user.security.level
        "Insufficient security clearance for dynamic creation"
```

Validates security requirements.

### Example 10: Check Context Ownership

```
Dynamic Creation Rules
    if enforce.ownership
        constraint datacontext.owner = current.user or 
                   current.user in datacontext.authorized.users
            "You are not authorized to dynamically create from this context"
```

Enforces ownership-based authorization.

### Example 11: Validate Department Rules

```
Dynamic Creation Rules
    constraint datacontext.department = current.user.department or
               current.user.has.permission("CrossDepartmentCreate")
        "Cannot dynamically create across departments without permission"
```

Enforces departmental boundaries.

### Example 12: Check Quota Limits

```
Dynamic Creation Rules
    constraint datacontext.quota.available > 0
        "Context quota exhausted; dynamic creation not allowed"
    
    decrement datacontext.quota.available
```

Manages context-specific quotas.

### Example 13: Validate Financial Period

```
Dynamic Creation Rules
    if datacontext.type = "Financial"
        constraint fiscal.period.status = "Open"
            "Cannot dynamically create in closed fiscal period"
```

Enforces financial period rules.

### Example 14: Check System State

```
Dynamic Creation Rules
    constraint system.maintenance.mode = false
        "Dynamic creation disabled during maintenance"
    
    constraint system.load < maximum.load.threshold
        "System load too high for dynamic creation"
```

Validates system state.

### Example 15: Validate Hierarchy Rules

```
Dynamic Creation Rules
    constraint datacontext.level < maximum.hierarchy.depth
        "Maximum hierarchy depth reached; cannot dynamically create"
```

Enforces hierarchy constraints.

### Example 16: Update Creation Statistics

```
Dynamic Creation Rules
    increment total.dynamic.creations
    last.dynamic.creation.date = today
    
    datacontext.dynamic.creation.count += 1
```

Tracks dynamic creation statistics.

### Example 17: Notify on Dynamic Creation

```
Dynamic Creation Rules
    if notify.on.dynamic.creation
        send notification
            to system.administrator
            description is "Dynamic creation occurred"
            detail is "Context: " + datacontext.id + " User: " + current.user
            priority is low
```

Alerts administrators of dynamic creation.

### Example 18: Validate Category Rules

```
Dynamic Creation Rules
    constraint datacontext.category in allowed.categories
        "Dynamic creation not allowed for this category"
    
    constraint datacontext.category.active = true
        "Category must be active for dynamic creation"
```

Enforces category-based rules.

### Example 19: Check Compliance Requirements

```
Dynamic Creation Rules
    if compliance.mode.enabled
        constraint datacontext.compliance.status = "Compliant"
            "Context must be compliant for dynamic creation"
        
        log "Compliance check passed for dynamic creation"
```

Ensures compliance requirements are met.

### Example 20: Complex Validation

```
Dynamic Creation Rules
    constraint (datacontext.status = "Active") and
               (datacontext.approved = true) and
               (current.user.has.permission("DynamicCreate")) and
               (dynamic.creation.count < maximum.dynamic.creations) and
               (system.resources.available)
        "Dynamic creation validation failed"
```

Combines multiple validation criteria.

## Best Practices

1. **Validate Context State**
   - Check context status
   - Verify context approval
   - Validate context type
   - Ensure context eligibility

2. **Enforce Permissions**
   - Check user permissions
   - Validate security levels
   - Enforce access control
   - Verify authorization

3. **Use DataContext Effectively**
   - Access triggering context with `datacontext`
   - Validate context properties
   - Check context relationships
   - Use context for authorization

4. **Provide Clear Messages**
   - Explain why creation failed
   - Provide context
   - Suggest corrections
   - Be specific

5. **Keep Rules Simple**
   - Focus on validation
   - Avoid complex logic
   - Use constraints effectively
   - Test thoroughly

6. **Monitor Creation Activity**
   - Log dynamic creations
   - Track patterns
   - Monitor frequency
   - Audit activity

7. **Consider Performance**
   - Keep validation efficient
   - Avoid heavy queries
   - Minimize processing
   - Test with load

## Common Patterns

### Context Validation Pattern
```
Dynamic Creation Rules
    constraint datacontext.status = <valid.status>
        "Invalid context state"
```

### Permission Check Pattern
```
Dynamic Creation Rules
    constraint current.user.has.permission(<permission>)
        "Permission denied"
```

### Quota Enforcement Pattern
```
Dynamic Creation Rules
    constraint <quota.field> > 0
        "Quota exhausted"
```

### Logging Pattern
```
Dynamic Creation Rules
    log "Dynamic creation: Context=" + datacontext.id
```

### Resource Check Pattern
```
Dynamic Creation Rules
    constraint <resource.available>
        "Insufficient resources"
```

## Dynamic Creation Context

When Dynamic Creation Rules fire:
- **This object** - The object being dynamically created (may not be fully initialized)
- **datacontext** - The business object triggering the dynamic creation
- **current.user** - User whose action triggered the creation
- **System context** - Current system state and resources

## Constraint Behavior

**Critical:** Any constraint rule that fails prevents the dynamic creation and throws an exception:

```
Dynamic Creation Rules
    constraint datacontext.status = "Active"
        "Cannot create from inactive context"
```

If the constraint fails:
1. Dynamic creation is blocked
2. Exception is thrown
3. Error message is displayed
4. Triggering operation fails

## Dynamic Creation Scenarios

Dynamic creation typically occurs when:
- Following a relation that doesn't exist yet
- Accessing a required related object
- System needs to create supporting records
- On-demand instantiation is configured

## Troubleshooting

### Issue: Dynamic creation always fails

**Possible Causes:**
- Constraint always fails
- Context never valid
- Permission always denied
- Logic error

**Solutions:**
- Review constraint logic
- Check context state
- Verify permissions
- Test with valid scenarios

### Issue: Cannot access datacontext

**Possible Causes:**
- Incorrect syntax
- DataContext not available
- Field reference error
- Scope issue

**Solutions:**
- Use `datacontext.` prefix
- Verify context availability
- Check field names
- Review scope

### Issue: Exception not thrown on failure

**Possible Causes:**
- Not using constraint
- Logic error
- Exception handling
- Configuration issue

**Solutions:**
- Use constraint for validation
- Review logic
- Check exception handling
- Verify configuration

### Issue: Performance degradation

**Possible Causes:**
- Heavy validation logic
- Complex queries
- Frequent dynamic creation
- Resource constraints

**Solutions:**
- Optimize validation
- Use efficient queries
- Review creation patterns
- Monitor resources

### Issue: Unexpected dynamic creation

**Possible Causes:**
- Relation configuration
- Missing records
- Logic triggering creation
- Configuration issue

**Solutions:**
- Review relation definitions
- Check data completeness
- Review triggering logic
- Verify configuration

## Related Concepts

- [Attach Rules](attach-rules.md) - Attachment validation (also uses datacontext)
- [Create Rules](create-rules.md) - Standard creation logic
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Relations](relations.md) - Relationship definitions
- [Conditions](conditions.md) - Boolean expressions

## Notes

- Dynamic Creation Rules fire before dynamic creation occurs
- Any constraint violation prevents creation and throws exception
- Use `datacontext` to access the business object triggering creation
- Both Attach Rules and Dynamic Creation Rules can use `datacontext`
- Keep rules simple and focused on validation
- Provide clear, actionable error messages
- Log dynamic creation activity for audit trail
- Consider performance impact of validation logic
- Test with various creation scenarios
- Document dynamic creation constraints clearly
- Dynamic creation is typically triggered by system, not user directly
- Constraint failures cause exceptions, not just validation messages
- Use for authorization and validation, not complex business logic
- Monitor dynamic creation patterns to identify issues
- Consider resource implications of dynamic creation
