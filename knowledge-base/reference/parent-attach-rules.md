# Parent Attach Rules

## Overview

Parent Attach Rules define business logic that executes when a child attempts to attach to a parent in a hierarchical relationship. These rules are valid only on Parent Hierarchy patterns and control whether the parent-child attachment is allowed. They provide specialized validation for hierarchical relationships beyond general attachment rules.

## Syntax

```
Parent Attach Rules
    <Rule>
    …
```

## When Parent Attach Rules Fire

Parent Attach Rules execute:
- **When child attaches to parent** - During parent-child relationship creation
- **In Parent Hierarchy only** - Valid only for hierarchical patterns
- **Before attachment is established** - Before relationship is created
- **On the parent object** - Rules defined on the parent business class

## Rule Constraints

- **Valid on Parent Hierarchy only** - Requires Parent Hierarchy pattern
- **Only general rules allowed** - Rules valid in all contexts
- **No Field Rules** - Cannot use field-specific rules
- **Constraint rules prevent attachment** - Any constraint violation blocks attachment
- **Access to child object** - Use `child` related link to access attaching child

## Child Related Link

Within Parent Attach Rules, use the `child` keyword to access the attaching child object:

```
Parent Attach Rules
    constraint child.status = "Active"
        "Child must be active to attach to parent"
```

## Use Cases

### Hierarchical Validation
- Validate parent-child compatibility
- Enforce hierarchy rules
- Check level constraints
- Validate hierarchy depth

### Child Validation
- Verify child state
- Check child attributes
- Validate child permissions
- Ensure child eligibility

### Capacity Management
- Limit number of children
- Check parent capacity
- Enforce child quotas
- Manage hierarchy size

### Hierarchy Integrity
- Prevent circular references
- Validate hierarchy levels
- Enforce branching rules
- Maintain tree structure

### Business Rules
- Enforce organizational rules
- Validate reporting structures
- Check approval hierarchies
- Maintain category trees

## Examples

### Example 1: Validate Child Status

```
Parent Attach Rules
    constraint child.status = "Active"
        "Only active children can attach to this parent"
    
    constraint child.approved = true
        "Child must be approved before attachment"
```

Ensures child is in valid state for attachment.

### Example 2: Limit Number of Children

```
Parent Attach Rules
    constraint child.count < maximum.children
        "Maximum number of children reached for this parent"
```

Enforces parent capacity limits.

### Example 3: Validate Hierarchy Level

```
Parent Attach Rules
    constraint child.level = level + 1
        "Child must be exactly one level below parent"
    
    constraint child.level <= maximum.hierarchy.depth
        "Maximum hierarchy depth would be exceeded"
```

Maintains proper hierarchy structure.

### Example 4: Prevent Circular References

```
Parent Attach Rules
    constraint child not in ancestors
        "Cannot create circular parent-child relationship"
    
    constraint child.id != id
        "Object cannot be its own parent"
```

Prevents invalid hierarchical relationships.

### Example 5: Validate Child Type

```
Parent Attach Rules
    constraint child.type in allowed.child.types
        "This child type cannot attach to this parent type"
```

Restricts which child types can attach.

### Example 6: Check Parent State

```
Parent Attach Rules
    constraint status = "Active"
        "Parent must be active to accept children"
    
    constraint not locked
        "Cannot attach children to locked parent"
```

Validates parent is ready to accept children.

### Example 7: Validate Ownership

```
Parent Attach Rules
    if enforce.ownership
        constraint child.owner = owner
            "Child must have same owner as parent"
```

Enforces ownership consistency in hierarchy.

### Example 8: Log Parent-Child Attachment

```
Parent Attach Rules
    log "Child attachment: " + child.id + " to parent " + id + 
        " by " + current.user
    
    increment child.attachment.count
```

Tracks parent-child relationship creation.

### Example 9: Validate Date Ranges

```
Parent Attach Rules
    constraint child.start.date >= start.date
        "Child start date must be on or after parent start date"
    
    if end.date not empty
        constraint child.end.date <= end.date
            "Child end date must be on or before parent end date"
```

Ensures temporal consistency in hierarchy.

### Example 10: Notify on Child Attachment

```
Parent Attach Rules
    send notification
        to owner
        description is "New child attached"
        detail is "Child " + child.id + " attached to parent " + id
        priority is low
```

Alerts parent owner of new child.

### Example 11: Validate Category Hierarchy

```
Parent Attach Rules
    constraint child.category.parent = category
        "Child category must be subcategory of parent category"
```

Maintains category hierarchy integrity.

### Example 12: Check Approval Requirements

```
Parent Attach Rules
    if requires.approval.for.children
        constraint child.approval.status = "Approved"
            "Child must be approved before attaching to this parent"
        
        constraint child.approved.by not empty
            "Child must have approver recorded"
```

Enforces approval requirements for children.

### Example 13: Validate Organizational Structure

```
Parent Attach Rules
    constraint child.department = department
        "Child must be in same department as parent"
    
    constraint child.cost.center = cost.center
        "Child must have same cost center as parent"
```

Maintains organizational alignment.

### Example 14: Enforce Branching Rules

```
Parent Attach Rules
    if parent.type = "Leaf"
        constraint false
            "Leaf nodes cannot have children"
    
    if child.type = "Root"
        constraint false
            "Root nodes cannot be children"
```

Enforces tree structure rules.

### Example 15: Validate Security Levels

```
Parent Attach Rules
    constraint child.security.level <= security.level
        "Child security level cannot exceed parent security level"
```

Maintains security hierarchy.

### Example 16: Check Geographic Constraints

```
Parent Attach Rules
    constraint child.region = region
        "Child must be in same region as parent"
    
    if country not empty
        constraint child.country = country
            "Child must be in same country as parent"
```

Enforces geographic hierarchy rules.

### Example 17: Validate Financial Hierarchy

```
Parent Attach Rules
    constraint child.fiscal.year = fiscal.year
        "Child must be in same fiscal year as parent"
    
    constraint child.budget.amount <= available.budget
        "Insufficient budget for child"
```

Maintains financial hierarchy integrity.

### Example 18: Update Parent Statistics

```
Parent Attach Rules
    increment total.children
    last.child.attached = today
    
    if child.type = "Critical"
        increment critical.children.count
```

Tracks parent statistics.

### Example 19: Validate Skill Hierarchy

```
Parent Attach Rules
    constraint child.skill.level = skill.level + 1
        "Child skill must be one level more specific than parent"
    
    constraint child.skill.category = skill.category
        "Child must be in same skill category"
```

Maintains skill taxonomy hierarchy.

### Example 20: Complex Hierarchy Validation

```
Parent Attach Rules
    constraint (status = "Active") and
               (child.status = "Active") and
               (child.level = level + 1) and
               (child.count < maximum.children) and
               (child not in ancestors)
        "Parent-child attachment validation failed"
```

Combines multiple hierarchy validation rules.

## Best Practices

1. **Validate Hierarchy Integrity**
   - Check level consistency
   - Prevent circular references
   - Validate depth limits
   - Maintain tree structure

2. **Use Child Related Link**
   - Access child properties with `child.`
   - Validate child state
   - Check child attributes
   - Compare parent and child

3. **Enforce Capacity Limits**
   - Limit number of children
   - Check parent capacity
   - Validate quotas
   - Prevent overload

4. **Maintain Consistency**
   - Align ownership
   - Match categories
   - Synchronize dates
   - Enforce security levels

5. **Provide Clear Messages**
   - Explain validation failures
   - Provide context
   - Suggest corrections
   - Be specific

6. **Keep Rules Simple**
   - Focus on validation
   - Avoid complex logic
   - Use constraints effectively
   - Test thoroughly

7. **Document Hierarchy Rules**
   - Explain hierarchy structure
   - Note validation rules
   - Describe constraints
   - Provide examples

## Common Patterns

### Child State Validation Pattern
```
Parent Attach Rules
    constraint child.status = <valid.status>
        "Child must be in valid state"
```

### Hierarchy Level Pattern
```
Parent Attach Rules
    constraint child.level = level + 1
        "Invalid hierarchy level"
```

### Capacity Limit Pattern
```
Parent Attach Rules
    constraint child.count < <maximum>
        "Maximum children reached"
```

### Circular Reference Prevention Pattern
```
Parent Attach Rules
    constraint child not in ancestors
        "Cannot create circular reference"
```

### Ownership Consistency Pattern
```
Parent Attach Rules
    constraint child.owner = owner
        "Owner mismatch"
```

## Parent Attach Rules vs Attach Rules

| Aspect | Parent Attach Rules | Attach Rules |
|--------|-------------------|--------------|
| Context | Parent Hierarchy only | Any attachment |
| Child Access | `child` related link available | Attaching object reference |
| Use Case | Parent-child relationships | General attachments |
| Specificity | Hierarchical validation | General validation |

Both can coexist; Parent Attach Rules provide hierarchy-specific validation.

## Understanding Parent Hierarchy Context

When Parent Attach Rules fire:
- **This object** - The parent object
- **child** - The child object attempting to attach
- **level** - Current hierarchy level
- **ancestors** - Parent's ancestors in hierarchy
- **current.user** - User performing the attachment

## Constraint Behavior

**Critical:** Any constraint rule that fails prevents the parent-child attachment:

```
Parent Attach Rules
    constraint child.level = level + 1
        "Child must be one level below parent"
```

If the constraint fails, the attachment is blocked and the error message is displayed.

## Troubleshooting

### Issue: Parent Attach Rules not firing

**Possible Causes:**
- Not using Parent Hierarchy pattern
- Rules in wrong section
- Syntax errors
- Business class not configured for hierarchy

**Solutions:**
- Verify Parent Hierarchy pattern is implemented
- Check rule location
- Validate syntax
- Review business class configuration

### Issue: Cannot access child properties

**Possible Causes:**
- Not using `child` keyword
- Incorrect field reference
- Child object not available
- Syntax error

**Solutions:**
- Use `child.` prefix for child fields
- Verify field names
- Check child object availability
- Review syntax

### Issue: Circular reference not detected

**Possible Causes:**
- Missing ancestor check
- Incorrect logic
- Ancestor list not populated
- Validation incomplete

**Solutions:**
- Add `child not in ancestors` check
- Review validation logic
- Verify ancestor tracking
- Test with circular scenarios

### Issue: Hierarchy depth not enforced

**Possible Causes:**
- Missing level validation
- Incorrect level calculation
- Maximum depth not set
- Logic error

**Solutions:**
- Add level validation
- Verify level calculation
- Set maximum depth
- Test with deep hierarchies

### Issue: Performance problems

**Possible Causes:**
- Complex validation logic
- Heavy ancestor checks
- Inefficient queries
- Many children

**Solutions:**
- Optimize validation
- Cache ancestor lists
- Use efficient queries
- Add appropriate indexes

## Related Concepts

- [Attach Rules](attach-rules.md) - General attachment rules
- [Business Class Patterns](business-class-patterns.md) - Hierarchy pattern
- [Relations](relations.md) - Relationship definitions
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Conditions](conditions.md) - Boolean expressions

## Notes

- Parent Attach Rules are valid only on Parent Hierarchy pattern
- Only general rules are allowed; no field-specific rules
- Any constraint violation prevents the parent-child attachment
- Use `child` related link to access attaching child object
- Rules execute before the parent-child relationship is established
- Keep rules simple and focused on hierarchical validation
- Provide clear, actionable error messages
- Prevent circular references in hierarchy
- Validate hierarchy levels and depth
- Update parent statistics as needed
- Log parent-child attachment activity for audit trail
- Consider performance impact of ancestor checks
- Test with various hierarchy scenarios
- Document hierarchy constraints clearly
- Parent Attach Rules complement general Attach Rules
- Use for validation specific to parent-child relationships
