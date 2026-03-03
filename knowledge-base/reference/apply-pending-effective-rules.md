# Apply Pending Effective Rules

## Overview

Apply Pending Effective Rules define business logic that executes when a pending effective-dated transaction is applied (when it becomes effective). These rules enable actions and validations that should occur at the moment a future-dated change takes effect, rather than when it was originally created.

## Syntax

```
Apply Pending Effective Rules
    <Rule>
    …
```

## When Apply Pending Effective Rules Fire

Apply Pending Effective Rules execute:
- **When effective date arrives** - When a future-dated transaction becomes effective
- **During application process** - As the pending transaction is applied
- **After transaction creation** - Not when initially created, but when activated

## Execution Context

Apply Pending Effective Rules run when:
- System processes pending effective-dated transactions
- Effective date threshold is reached
- Transaction transitions from pending to effective
- Changes are actually applied to the business object

## Timing Comparison

| Rule Type | Future-Dated Transaction Creation | Future-Dated Transaction Becomes Effective |
|-----------|----------------------------------|-------------------------------------------|
| Audit Entry Rules | ✓ Fire | ✗ Do not fire |
| Commit Rules | ✗ Do not fire | ✓ Fire |
| Apply Pending Effective Rules | ✗ Do not fire | ✓ Fire |

## Use Cases

### Effective Date Notifications
- Notify stakeholders when changes take effect
- Alert users of activated changes
- Send confirmation messages
- Trigger downstream processes

### Effective Date Validations
- Validate conditions at effective time
- Check current state compatibility
- Verify prerequisites met
- Ensure data consistency

### State Synchronization
- Update related records
- Synchronize dependent data
- Refresh cached values
- Trigger cascading updates

### Workflow Triggers
- Initiate follow-up processes
- Start approval workflows
- Trigger business events
- Launch scheduled tasks

### Audit and Logging
- Log effective date application
- Record activation timestamp
- Track application status
- Create audit trail entries

## Examples

### Example 1: Effective Date Notification

```
Apply Pending Effective Rules
    send notification
        to change.requester
        description is "Your scheduled change is now effective"
        detail is "Change to " + field.name + " is now active"
        priority is medium
```

Notifies requester when future-dated change takes effect.

### Example 2: Validate Current State

```
Apply Pending Effective Rules
    constraint employee.status = "Active"
        "Employee must be active when salary change takes effect"
    
    constraint department.budget.available >= salary.increase
        "Insufficient budget available for salary increase"
```

Validates conditions at the moment change becomes effective.

### Example 3: Update Related Records

```
Apply Pending Effective Rules
    department.total.salary += salary.increase
    department.budget.used += salary.increase
    
    employee.last.salary.change.date = today
    employee.salary.change.count += 1
```

Updates department totals when salary change takes effect.

### Example 4: Trigger Workflow

```
Apply Pending Effective Rules
    if price.change.percent > 10
        initiate PRICE_CHANGE_NOTIFICATION workflow
            Variables
                product.id
                old.price
                new.price
                effective.date
```

Initiates workflow when significant price change becomes effective.

### Example 5: Log Application

```
Apply Pending Effective Rules
    log "Effective-dated change applied: " + change.description
    
    application.date = today
    application.time = current.time
    applied.by.system = true
```

Records when and how change was applied.

### Example 6: Synchronize Inventory

```
Apply Pending Effective Rules
    for each warehouse.location set
        each.reorder.point = new.reorder.point
        each.reorder.quantity = new.reorder.quantity
    
    send notification
        to inventory.manager
        description is "Reorder parameters updated for " + product.name
```

Applies inventory parameter changes across locations.

### Example 7: Update Pricing

```
Apply Pending Effective Rules
    old.price = current.price
    current.price = new.price
    price.effective.date = today
    
    for each active.quote set
        if each.quote.date < today
            each.requires.repricing = true
```

Applies price change and flags quotes needing update.

### Example 8: Contract Activation

```
Apply Pending Effective Rules
    contract.status = "Active"
    contract.activation.date = today
    
    send notification
        to contract.owner
        description is "Contract " + contract.number + " is now active"
        priority is high
    
    send notification
        to customer.contact
        description is "Your contract is now in effect"
```

Activates contract and notifies parties.

### Example 9: Rate Change Application

```
Apply Pending Effective Rules
    previous.rate = current.rate
    current.rate = new.rate
    rate.change.effective.date = today
    
    for each active.loan set
        each.interest.rate = new.rate
        each.rate.change.date = today
        each.recalculation.required = true
```

Applies interest rate change to active loans.

### Example 10: Organization Change

```
Apply Pending Effective Rules
    for each employee in old.department set
        each.department = new.department
        each.transfer.date = today
        each.transfer.reason = "Department Reorganization"
    
    old.department.status = "Inactive"
    new.department.employee.count += old.department.employee.count
```

Applies organizational restructuring.

### Example 11: Policy Update

```
Apply Pending Effective Rules
    policy.version = new.version
    policy.effective.date = today
    
    for each covered.entity set
        each.policy.version = new.version
        each.requires.acknowledgment = true
    
    send email
        to policy.administrators
        subject "Policy Update Effective"
        Contents
            "Policy " + policy.name + " version " + new.version + 
            " is now in effect"
```

Activates policy update and requires acknowledgment.

### Example 12: Benefit Enrollment

```
Apply Pending Effective Rules
    enrollment.status = "Active"
    coverage.start.date = today
    
    employee.benefit.status = "Enrolled"
    employee.deduction.amount = plan.employee.cost
    
    send notification
        to employee
        description is "Your benefit enrollment is now active"
        detail is "Coverage began " + today
```

Activates benefit enrollment on effective date.

### Example 13: Territory Assignment

```
Apply Pending Effective Rules
    for each customer in old.territory set
        each.sales.territory = new.territory
        each.sales.rep = new.territory.sales.rep
        each.territory.change.date = today
    
    send notification
        to new.territory.sales.rep
        description is "New customers assigned to your territory"
        detail is customer.count + " customers transferred"
```

Applies territory reassignment.

### Example 14: Compliance Activation

```
Apply Pending Effective Rules
    regulation.status = "Active"
    compliance.required = true
    
    for each affected.process set
        each.compliance.check.required = true
        each.regulation.id = regulation.id
    
    log "Regulation " + regulation.id + " now in effect"
    
    send notification
        to compliance.officer
        description is "New regulation is now active"
        priority is high
```

Activates compliance requirements.

### Example 15: System Configuration

```
Apply Pending Effective Rules
    system.config.version = new.version
    config.effective.date = today
    
    clear in-memory cache for SYSTEM_CONFIG
    
    log "System configuration updated to version " + new.version
    
    send notification
        to system.administrators
        description is "System configuration change is now active"
        detail is "Version " + new.version + " effective " + today
```

Applies system configuration change.

## Best Practices

1. **Validate Current State**
   - Check conditions at effective time
   - Verify prerequisites still met
   - Ensure compatibility with current data
   - Handle state changes since scheduling

2. **Notify Stakeholders**
   - Inform affected users
   - Alert administrators
   - Confirm activation
   - Provide relevant details

3. **Update Related Data**
   - Synchronize dependent records
   - Update aggregates and totals
   - Refresh cached values
   - Maintain data consistency

4. **Log Application**
   - Record activation timestamp
   - Track application status
   - Create audit trail
   - Document system actions

5. **Handle Errors Gracefully**
   - Validate before applying
   - Provide clear error messages
   - Consider rollback scenarios
   - Alert administrators of failures

6. **Consider Performance**
   - Minimize processing time
   - Batch related updates
   - Use efficient queries
   - Avoid blocking operations

7. **Test Thoroughly**
   - Test with various effective dates
   - Verify state changes
   - Test error scenarios
   - Validate notifications

## Common Patterns

### Notification Pattern
```
Apply Pending Effective Rules
    send notification
        to <stakeholder>
        description is "Change is now effective"
        detail is <change.details>
```

### Validation Pattern
```
Apply Pending Effective Rules
    constraint <current.state.valid>
        "Cannot apply change: " + <reason>
```

### Synchronization Pattern
```
Apply Pending Effective Rules
    for each <related.record> set
        each.<field> = <new.value>
        each.<timestamp> = today
```

### Audit Pattern
```
Apply Pending Effective Rules
    log "Change applied: " + <description>
    <audit.field> = today
    <status.field> = "Applied"
```

### Workflow Pattern
```
Apply Pending Effective Rules
    if <condition>
        initiate <workflow> workflow
            Variables
                <relevant.data>
```

## Execution Sequence

For a future-dated transaction:

1. **Transaction Creation**
   - Audit Entry Rules fire
   - Transaction saved as pending
   - Effective date in future

2. **Effective Date Arrives**
   - System processes pending transactions
   - Apply Pending Effective Rules fire
   - Changes applied to business object
   - Commit Rules fire
   - Transaction commits

## Troubleshooting

### Issue: Rules not firing when effective

**Possible Causes:**
- Transaction not marked as pending
- Effective date not reached
- System not processing pending transactions
- Configuration issues

**Solutions:**
- Verify transaction status
- Check effective date
- Verify system scheduler running
- Review configuration

### Issue: Validation failing at effective time

**Possible Causes:**
- State changed since scheduling
- Prerequisites no longer met
- Data inconsistency
- Concurrent changes

**Solutions:**
- Review validation logic
- Check current state
- Handle state changes
- Implement conflict resolution

### Issue: Related records not updated

**Possible Causes:**
- Incorrect related link
- Records deleted since scheduling
- Locking conflicts
- Transaction isolation issues

**Solutions:**
- Verify related links
- Check record existence
- Handle missing records
- Review locking strategy

### Issue: Notifications not sent

**Possible Causes:**
- Notification configuration
- Recipient not available
- Email/notification service issues
- Condition not met

**Solutions:**
- Verify notification setup
- Check recipient validity
- Test notification service
- Review condition logic

## Related Concepts

- [Commit Rules](commit-rules.md) - Final transaction rules
- [Audit Entry Rules](audit-entry-rules.md) - Audit log entry rules
- [Action Rules](action-rules.md) - Action-level rules
- [Field Rules](field-rules.md) - Field-level rules
- [Dates and Timestamps](dates-timestamps.md) - Effective date handling

## Notes

- Apply Pending Effective Rules fire when future-dated transaction becomes effective
- Rules do NOT fire when transaction is initially created
- Rules fire during system processing of pending transactions
- Commit Rules also fire when transaction becomes effective
- Audit Entry Rules do NOT fire again when transaction becomes effective
- Use these rules for actions that should occur at effective time, not creation time
- Validate current state since conditions may have changed since scheduling
- Consider time zone implications for effective dates
- Test with various effective date scenarios
- Handle cases where prerequisites are no longer met
- Log application for audit trail
- Notify stakeholders of activation
- Keep rules efficient to avoid processing delays
