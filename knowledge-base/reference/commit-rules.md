# Commit Rules

## Overview

Commit Rules define business logic that executes just before the final commit point of any transaction. They provide a last opportunity to validate data, perform calculations, update related records, or trigger side effects before changes are permanently saved to the database.

## Syntax

```
Commit Rules
    <Rule>
    …
```

## When Commit Rules Fire

Commit Rules execute:
- **Just before final commit** - After all other rules but before database commit
- **On RequestAction completion** - Not when initiated, but when completed
- **On effective date** - For future-dated transactions, when they become effective

Commit Rules do NOT fire:
- When a RequestAction is initiated (only when completed)
- When a future effective-dated transaction is initiated (only when effective)

## Execution Context

Commit Rules run in a special context:
- All field rules have executed
- All action rules have executed
- Data is in final state before commit
- Last chance to validate or modify
- Transaction is about to be committed

## Use Cases

### Final Validation
- Cross-record validation
- Business rule enforcement
- Data integrity checks
- Consistency validation

### Calculated Fields
- Final calculations
- Aggregate updates
- Derived values
- Summary fields

### Related Record Updates
- Update parent records
- Synchronize related data
- Maintain denormalized data
- Update audit trails

### Notifications
- Send completion notifications
- Trigger alerts
- Log significant events
- Update external systems

### State Management
- Update status fields
- Set completion timestamps
- Record final state
- Update counters

## Examples

### Example 1: Final Validation

```
Commit Rules
    constraint total.amount = line.items.sum
        "Total amount must equal sum of line items"
    
    constraint approved.by not empty when status = "Approved"
        "Approver required for approved records"
```

Validates data consistency before commit.

### Example 2: Update Parent Record

```
Commit Rules
    if order.status = "Completed"
        order.completion.date = today
        order.completed.by = current.user
        
        customer.last.order.date = today
        increment customer.order.count
```

Updates parent customer record when order completes.

### Example 3: Calculate Aggregates

```
Commit Rules
    order.total = 0
    for each order.line set
        order.total += each.line.total
    
    order.tax = order.total * tax.rate
    order.grand.total = order.total + order.tax
```

Calculates final totals before commit.

### Example 4: Synchronize Related Data

```
Commit Rules
    if inventory.quantity.changed
        for each warehouse.location set
            each.total.inventory = sum of inventory at location
        
        product.total.on.hand = sum of all inventory
```

Maintains denormalized inventory totals.

### Example 5: Send Notifications

```
Commit Rules
    if status.changed and status = "Approved"
        send notification
            to requester
            description is "Your request has been approved"
            priority is medium
```

Notifies users of status changes.

### Example 6: Audit Trail

```
Commit Rules
    if significant.fields.changed
        log "Record modified: " + record.id + " by " + current.user
        
        audit.entry.date = today
        audit.entry.user = current.user
        audit.entry.action = "Update"
```

Creates audit trail entries.

### Example 7: Update Counters

```
Commit Rules
    if status = "Completed" and previous.status != "Completed"
        increment department.completed.count
        decrement department.pending.count
```

Maintains status counters.

### Example 8: Validate Business Rules

```
Commit Rules
    if order.type = "Credit"
        constraint customer.credit.available >= order.total
            "Insufficient credit available"
    
    constraint ship.date >= order.date
        "Ship date cannot be before order date"
```

Enforces complex business rules.

### Example 9: Set Completion Timestamps

```
Commit Rules
    if status = "Closed" and close.date empty
        close.date = today
        close.time = current.time
        closed.by = current.user
```

Records completion information.

### Example 10: Maintain Derived Data

```
Commit Rules
    product.average.cost = total.cost / total.quantity
    product.margin.percent = ((selling.price - average.cost) / selling.price) * 100
    
    if margin.percent < minimum.margin
        product.review.required = true
```

Updates derived fields and flags.

### Example 11: Cross-Record Validation

```
Commit Rules
    for each allocation set
        total.allocated += each.amount
    
    constraint total.allocated <= budget.amount
        "Total allocations cannot exceed budget"
```

Validates across multiple related records.

### Example 12: Conditional Updates

```
Commit Rules
    if invoice.status = "Paid"
        customer.balance -= invoice.amount
        customer.last.payment.date = payment.date
        
        if customer.balance = 0
            customer.status = "Current"
```

Updates customer account based on payment.

### Example 13: State Transition Validation

```
Commit Rules
    if status.changed
        constraint valid.status.transition(previous.status, status)
            "Invalid status transition from " + previous.status + " to " + status
```

Validates state transitions.

### Example 14: External System Integration

```
Commit Rules
    if sync.required
        send ion bod
            bod is order.xml
            bod type is "Sync.SalesOrder"
            assign message id to ion.message.id
```

Synchronizes with external systems.

### Example 15: Complex Calculations

```
Commit Rules
    discount.amount = 0
    
    if order.total > 10000
        discount.amount = order.total * 0.10
    else if order.total > 5000
        discount.amount = order.total * 0.05
    
    if customer.type = "Premium"
        discount.amount += order.total * 0.05
    
    final.amount = order.total - discount.amount
```

Performs multi-step calculations.

## Best Practices

1. **Use for Final Operations**
   - Perform final validations
   - Calculate final values
   - Update summary data
   - Trigger completion actions

2. **Avoid Heavy Processing**
   - Keep commit rules efficient
   - Minimize database queries
   - Avoid long-running operations
   - Consider background processing for heavy tasks

3. **Handle Errors Gracefully**
   - Provide clear error messages
   - Use constraints for validation
   - Consider rollback implications
   - Test error scenarios

4. **Maintain Data Integrity**
   - Validate cross-record consistency
   - Update related records atomically
   - Handle concurrent updates
   - Use appropriate locking

5. **Consider Transaction Scope**
   - Commit rules are last in transaction
   - Changes here are part of same transaction
   - Rollback affects all changes
   - Plan for transaction boundaries

6. **Document Complex Logic**
   - Explain business rules
   - Note dependencies
   - Describe expected behavior
   - Document side effects

7. **Test Thoroughly**
   - Test all code paths
   - Verify rollback behavior
   - Test with concurrent updates
   - Validate error handling

## Common Patterns

### Validation Pattern
```
Commit Rules
    constraint <FinalValidation>
        <ErrorMessage>
```

### Aggregation Pattern
```
Commit Rules
    <aggregate.field> = 0
    for each <related.set>
        <aggregate.field> += each.<value.field>
```

### Synchronization Pattern
```
Commit Rules
    if <condition>
        <related.record>.<field> = <value>
```

### Notification Pattern
```
Commit Rules
    if <significant.change>
        send notification
            to <recipient>
            description is <message>
```

### Audit Pattern
```
Commit Rules
    log <audit.message>
    <audit.field> = <audit.value>
```

## Execution Order

1. **Field Rules** - Execute first
2. **Action Rules** - Execute for the action
3. **Commit Rules** - Execute last, just before commit
4. **Database Commit** - Changes persisted

## Timing Considerations

### RequestAction Timing
- Commit rules fire when RequestAction **completes**, not when initiated
- Allows for asynchronous processing
- Rules execute in completion context

### Future-Dated Transactions
- Commit rules fire when transaction **becomes effective**, not when created
- Enables proper effective-date handling
- Rules execute with effective-date context

### Regular Transactions
- Commit rules fire immediately before commit
- Part of same transaction
- Rollback includes commit rule changes

## Differences from Other Rules

| Rule Type | Execution Timing | Purpose |
|-----------|-----------------|---------|
| Field Rules | During field validation | Field-level logic |
| Action Rules | During action execution | Action-specific logic |
| Commit Rules | Just before commit | Final validation and updates |

## Troubleshooting

### Issue: Commit rules not firing

**Possible Causes:**
- Transaction rolled back before commit
- Error in earlier rules
- RequestAction not completed
- Future-dated transaction not effective

**Solutions:**
- Check for errors in earlier rules
- Verify transaction completes
- Check RequestAction status
- Verify effective date reached

### Issue: Performance problems

**Possible Causes:**
- Heavy processing in commit rules
- Complex queries
- Many related record updates
- Inefficient loops

**Solutions:**
- Optimize queries
- Minimize processing
- Use efficient algorithms
- Consider background processing

### Issue: Validation errors at commit

**Possible Causes:**
- Data changed by commit rules
- Constraint violations
- Concurrent updates
- Missing required data

**Solutions:**
- Review commit rule logic
- Check constraint conditions
- Handle concurrent updates
- Validate data completeness

### Issue: Unexpected rollback

**Possible Causes:**
- Constraint failure in commit rules
- Error in commit rule logic
- External system failure
- Database constraint violation

**Solutions:**
- Review error messages
- Check commit rule constraints
- Validate external integrations
- Verify database constraints

### Issue: Related records not updated

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

- [Field Rules](field-rules.md) - Field-level rules
- [Action Rules](action-rules.md) - Action-level rules
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Conditions](conditions.md) - Boolean expressions
- [References](references.md) - Related values and links

## Notes

- Commit rules execute just before final database commit
- They are the last rules to execute in a transaction
- All field and action rules have completed before commit rules fire
- Commit rules do not fire when RequestAction is initiated, only when completed
- For future-dated transactions, commit rules fire when transaction becomes effective
- Changes made in commit rules are part of the same transaction
- If commit rules fail, entire transaction rolls back
- Keep commit rules efficient to avoid performance issues
- Use commit rules for final validation and updates
- Commit rules have access to all fields in final state
- Consider transaction boundaries when designing commit rules
- Test commit rules thoroughly, including error scenarios
- Document complex commit rule logic clearly
