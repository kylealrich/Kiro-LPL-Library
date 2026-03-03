# Create Rules

## Overview

Create Rules define business logic that executes when any Create-type action is executed. They provide a centralized location for logic that should apply to all record creation scenarios, regardless of which specific Create action is invoked. These rules are essential for enforcing consistent creation behavior across the business class.

## Syntax

```
Create Rules
    <Rule>
    …
```

## When Create Rules Fire

Create Rules execute:
- **During any Create action** - Regardless of which Create action is invoked
- **After field defaults** - After field-level default rules execute
- **Before action-specific rules** - Before individual Create action rules
- **Before commit** - Before Commit Rules execute

## Execution Order

For a Create action:

1. **Field Rules** - Field defaults and validation
2. **Create Rules** - Common creation logic (fires here)
3. **Specific Create Action Rules** - Action-specific logic
4. **Commit Rules** - Final validation and updates
5. **Database Commit** - Record persisted

## Use Cases

### Common Initialization
- Set standard default values
- Initialize system fields
- Assign sequence numbers
- Set creation timestamps

### Universal Validation
- Enforce creation requirements
- Validate business rules
- Check prerequisites
- Verify data consistency

### Automatic Relationships
- Create related records
- Establish parent-child links
- Initialize collections
- Set up dependencies

### Audit and Tracking
- Log record creation
- Track creation source
- Record creator information
- Initialize audit fields

### Notifications
- Notify stakeholders
- Send creation alerts
- Trigger workflows
- Update dashboards

## Examples

### Example 1: Initialize System Fields

```
Create Rules
    create.date = today
    create.time = current.time
    created.by = current.user
    
    status = "Active"
    version = 1
```

Sets standard system fields on all new records.

### Example 2: Assign Sequence Number

```
Create Rules
    if record.number empty
        autosequence record.number
            using RECORD_NUMBER_SET
            minimize contention and gaps
```

Automatically assigns unique record number.

### Example 3: Validate Prerequisites

```
Create Rules
    constraint parent.record not empty
        "Parent record required for creation"
    
    constraint parent.record.status = "Active"
        "Parent record must be active"
```

Ensures prerequisites are met before creation.

### Example 4: Create Related Records

```
Create Rules
    invoke CREATE.AUDIT_LOG
        invoked.record.id = record.id
        invoked.action = "Create"
        invoked.timestamp = today
        invoked.user = current.user
```

Automatically creates audit log entry.

### Example 5: Initialize Collections

```
Create Rules
    invoke CREATE.DEFAULT_SETTINGS this instance
        invoked.setting.type = "Standard"
        invoked.apply.defaults = true
```

Creates default settings for new record.

### Example 6: Send Notifications

```
Create Rules
    send notification
        to record.owner
        description is "New record created"
        detail is "Record " + record.id + " has been created"
        priority is low
```

Notifies owner of new record creation.

### Example 7: Set Calculated Defaults

```
Create Rules
    if discount.percent empty
        if customer.type = "Premium"
            discount.percent = 15
        else if customer.type = "Standard"
            discount.percent = 10
        else
            discount.percent = 5
```

Calculates default discount based on customer type.

### Example 8: Initialize Workflow

```
Create Rules
    if requires.approval
        initiate APPROVAL_WORKFLOW workflow
            Variables
                record.id
                record.type
                requested.by = current.user
```

Starts approval workflow for new records.

### Example 9: Update Parent Counters

```
Create Rules
    increment parent.child.count
    parent.last.child.created = today
```

Updates parent record statistics.

### Example 10: Validate Business Rules

```
Create Rules
    constraint effective.date >= today
        "Effective date cannot be in the past"
    
    if record.type = "Financial"
        constraint amount > 0
            "Amount must be positive for financial records"
```

Enforces business rules on creation.

### Example 11: Set Security Context

```
Create Rules
    owner = current.user
    owner.department = current.user.department
    security.group = current.user.default.security.group
    
    if confidential
        access.level = "Restricted"
    else
        access.level = "Standard"
```

Establishes security context for new record.

### Example 12: Initialize State

```
Create Rules
    if approval.required
        make transition to PENDING_APPROVAL
    else
        make transition to ACTIVE
```

Sets initial state based on conditions.

### Example 13: Create Dependencies

```
Create Rules
    for each required.component set
        invoke CREATE.COMPONENT_INSTANCE
            invoked.parent.id = record.id
            invoked.component.type = each.component.type
            invoked.quantity = each.default.quantity
```

Creates required dependent records.

### Example 14: Log Creation

```
Create Rules
    log "Record created: " + record.id + " by " + current.user
    
    if high.value.record
        log "High value record created: " + record.id + 
            " Amount: " + amount
```

Logs record creation for audit trail.

### Example 15: Initialize Metrics

```
Create Rules
    total.views = 0
    total.updates = 0
    last.accessed = today
    
    quality.score = calculate.initial.quality.score
```

Initializes tracking metrics.

### Example 16: Set Defaults from Template

```
Create Rules
    if template.id not empty
        fill in blank fields from template
            except record.id
            except create.date
            except created.by
```

Populates fields from template.

### Example 17: Validate Uniqueness

```
Create Rules
    constraint not exists duplicate.record
        "Record with same key already exists"
    
    if business.key not empty
        constraint unique.business.key
            "Business key must be unique"
```

Ensures uniqueness constraints.

### Example 18: Initialize Financial Data

```
Create Rules
    if record.type = "Financial"
        fiscal.period = current.fiscal.period
        fiscal.year = current.fiscal.year
        accounting.entity = default.accounting.entity
        
        constraint accounting.entity not empty
            "Accounting entity required for financial records"
```

Sets up financial context.

### Example 19: Create Notification Subscriptions

```
Create Rules
    invoke CREATE.SUBSCRIPTION
        invoked.record.id = record.id
        invoked.subscriber = created.by
        invoked.notification.type = "All Changes"
```

Automatically subscribes creator to notifications.

### Example 20: Initialize Hierarchy

```
Create Rules
    if parent.record not empty
        level = parent.record.level + 1
        root.record = parent.record.root.record
        hierarchy.path = parent.record.hierarchy.path + "/" + record.id
    else
        level = 1
        root.record = record.id
        hierarchy.path = "/" + record.id
```

Establishes hierarchical relationships.

## Best Practices

1. **Use for Common Logic**
   - Put logic that applies to ALL creates here
   - Avoid action-specific logic
   - Keep rules general and reusable
   - Document purpose clearly

2. **Initialize Consistently**
   - Set standard system fields
   - Assign default values
   - Initialize collections
   - Establish relationships

3. **Validate Early**
   - Check prerequisites
   - Enforce business rules
   - Validate data consistency
   - Provide clear error messages

4. **Keep Rules Efficient**
   - Minimize processing
   - Avoid heavy calculations
   - Use efficient queries
   - Consider performance impact

5. **Handle Defaults Properly**
   - Check if field is empty before defaulting
   - Allow action-specific overrides
   - Use force default when appropriate
   - Document default logic

6. **Maintain Audit Trail**
   - Log creation events
   - Track creator information
   - Record creation context
   - Initialize audit fields

7. **Test Thoroughly**
   - Test with all Create actions
   - Verify rule execution order
   - Test error scenarios
   - Validate side effects

## Common Patterns

### Initialization Pattern
```
Create Rules
    <system.field> = <default.value>
    <timestamp.field> = today
    <user.field> = current.user
```

### Validation Pattern
```
Create Rules
    constraint <business.rule>
        <error.message>
```

### Autosequence Pattern
```
Create Rules
    if <id.field> empty
        autosequence <id.field>
            using <set.name>
```

### Notification Pattern
```
Create Rules
    send notification
        to <stakeholder>
        description is "New record created"
```

### Related Record Pattern
```
Create Rules
    invoke CREATE.<related.action>
        invoked.<field> = <value>
```

## Create Rules vs Action-Specific Rules

| Aspect | Create Rules | Specific Create Action Rules |
|--------|-------------|------------------------------|
| Scope | All Create actions | Single Create action |
| Purpose | Common creation logic | Action-specific logic |
| Execution | Always fires | Fires for that action only |
| Use Case | Universal requirements | Specialized behavior |

## Interaction with Other Rules

### Field Rules
- Execute before Create Rules
- Provide field-level defaults
- Perform field validation

### Create Rules
- Execute after Field Rules
- Apply to all Create actions
- Provide common initialization

### Specific Create Action Rules
- Execute after Create Rules
- Override or extend Create Rules
- Provide action-specific logic

### Commit Rules
- Execute after all other rules
- Perform final validation
- Update related records

## Troubleshooting

### Issue: Create Rules not firing

**Possible Causes:**
- Not a Create action
- Syntax errors in rules
- Rules in wrong section
- Action bypassing rules

**Solutions:**
- Verify action type
- Check rule syntax
- Verify rule location
- Review action configuration

### Issue: Defaults not applying

**Possible Causes:**
- Field already has value
- Action-specific rules overriding
- Condition not met
- Execution order issue

**Solutions:**
- Check field value before defaulting
- Review action-specific rules
- Verify conditions
- Check execution order

### Issue: Validation failing unexpectedly

**Possible Causes:**
- Constraint too strict
- Data not available
- Timing issue
- Context problem

**Solutions:**
- Review constraint logic
- Check data availability
- Verify execution timing
- Validate context

### Issue: Performance problems

**Possible Causes:**
- Heavy processing in rules
- Complex queries
- Many related record creations
- Inefficient loops

**Solutions:**
- Optimize processing
- Use efficient queries
- Batch operations
- Minimize iterations

## Related Concepts

- [Field Rules](field-rules.md) - Field-level rules
- [Action Rules](action-rules.md) - Action-specific rules
- [Commit Rules](commit-rules.md) - Final transaction rules
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Actions and Actors](actions-actors.md) - Action definitions

## Notes

- Create Rules fire for ALL Create-type actions
- Rules execute after field defaults but before action-specific rules
- Use Create Rules for logic common to all creation scenarios
- Action-specific Create action rules can override or extend Create Rules
- Keep Create Rules general and avoid action-specific logic
- Create Rules are ideal for initialization, validation, and audit
- Test Create Rules with all Create actions in the business class
- Document the purpose and behavior of Create Rules clearly
- Consider performance impact since rules fire on every creation
- Use constraints for validation that should prevent creation
- Initialize system fields consistently in Create Rules
