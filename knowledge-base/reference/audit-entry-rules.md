# Audit Entry Rules

## Overview

Audit Entry Rules define business logic that executes just before an audit log entry is created. They enable customization of audit data, validation of audit entries, and triggering of audit-related side effects. These rules are critical for maintaining comprehensive audit trails and ensuring audit data quality.

## Syntax

```
Audit Entry Rules
    <Rule>
    …
```

## When Audit Entry Rules Fire

Audit Entry Rules execute:
- **Just before audit log entry creation** - Before the audit record is written
- **Multiple times per transaction** - If effective date changes during transaction
- **On RequestAction completion** - Not when initiated, but when completed
- **On future-dated transaction initiation** - When the transaction is created

Audit Entry Rules do NOT fire:
- When a RequestAction is initiated (only when completed)
- When a future effective-dated transaction becomes effective (only at initiation)

## Key Timing Differences

### Audit Entry Rules vs Commit Rules

| Aspect | Audit Entry Rules | Commit Rules |
|--------|------------------|--------------|
| RequestAction | Fire when **completed** | Fire when **completed** |
| Future-Dated Transactions | Fire when **initiated** | Fire when **becomes effective** |
| Frequency | Multiple times if effective date changes | Once per transaction |
| Purpose | Audit log customization | Final validation/updates |

## Execution Context

Audit Entry Rules run in a special context:
- Just before audit log entry is written
- Access to audit entry fields
- Access to business object fields
- Can modify audit entry data
- Can validate audit requirements

## Use Cases

### Audit Data Enhancement
- Add custom audit fields
- Calculate audit metrics
- Enrich audit context
- Capture additional information

### Audit Validation
- Validate audit completeness
- Enforce audit requirements
- Check audit data quality
- Verify audit conditions

### Audit Classification
- Categorize audit entries
- Set audit severity
- Tag significant changes
- Flag critical updates

### Audit Notifications
- Alert on significant changes
- Notify audit reviewers
- Trigger compliance workflows
- Send audit reports

### Audit Metrics
- Calculate change magnitude
- Track modification patterns
- Measure data volatility
- Monitor audit frequency

## Examples

### Example 1: Enhance Audit Entry

```
Audit Entry Rules
    audit.entry.category = "Financial"
    audit.entry.severity = "High"
    
    if amount.changed and abs(amount - previous.amount) > 10000
        audit.entry.requires.review = true
        audit.entry.review.reason = "Large amount change"
```

Categorizes and flags significant financial changes.

### Example 2: Capture Change Magnitude

```
Audit Entry Rules
    if price.changed
        audit.entry.price.change.amount = price - previous.price
        audit.entry.price.change.percent = 
            ((price - previous.price) / previous.price) * 100
        
        if abs(audit.entry.price.change.percent) > 10
            audit.entry.significant.change = true
```

Calculates and records price change metrics.

### Example 3: Validate Audit Requirements

```
Audit Entry Rules
    if status.changed to "Approved"
        constraint approval.comments not empty
            "Approval comments required for audit trail"
        
        constraint approved.by not empty
            "Approver must be recorded"
```

Enforces audit data completeness.

### Example 4: Audit Classification

```
Audit Entry Rules
    audit.entry.change.type = "Standard"
    
    if critical.fields.changed
        audit.entry.change.type = "Critical"
        audit.entry.notification.required = true
    
    if security.fields.changed
        audit.entry.change.type = "Security"
        audit.entry.compliance.review = true
```

Classifies audit entries by change type.

### Example 5: Track Field Changes

```
Audit Entry Rules
    audit.entry.fields.changed.count = 0
    
    for each changed.field
        increment audit.entry.fields.changed.count
        
        if each.field.sensitive
            audit.entry.contains.sensitive.data = true
```

Counts and flags changed fields.

### Example 6: Audit Notifications

```
Audit Entry Rules
    if customer.credit.limit.changed
        send notification
            to credit.manager
            description is "Credit limit changed for " + customer.name
            detail is "Old: " + previous.credit.limit + 
                     " New: " + credit.limit
            priority is high
```

Notifies stakeholders of critical changes.

### Example 7: Compliance Tracking

```
Audit Entry Rules
    if gdpr.protected.fields.changed
        audit.entry.compliance.category = "GDPR"
        audit.entry.data.subject.id = customer.id
        audit.entry.legal.basis = data.processing.basis
        
        log "GDPR protected data modified for customer " + customer.id
```

Tracks compliance-related changes.

### Example 8: Change Reason Validation

```
Audit Entry Rules
    if price.changed or discount.changed
        constraint change.reason not empty
            "Change reason required for pricing modifications"
        
        constraint length(change.reason) >= 10
            "Change reason must be at least 10 characters"
```

Requires and validates change justification.

### Example 9: Effective Date Tracking

```
Audit Entry Rules
    audit.entry.effective.date = effective.date
    audit.entry.entry.date = today
    
    if effective.date > today
        audit.entry.future.dated = true
        audit.entry.days.until.effective = 
            days.between(today, effective.date)
```

Tracks effective dating information.

### Example 10: User Context Capture

```
Audit Entry Rules
    audit.entry.user.id = current.user
    audit.entry.user.role = current.user.role
    audit.entry.user.department = current.user.department
    audit.entry.session.id = current.session.id
    audit.entry.ip.address = current.ip.address
```

Captures comprehensive user context.

### Example 11: Business Context

```
Audit Entry Rules
    audit.entry.business.unit = department.business.unit
    audit.entry.cost.center = department.cost.center
    audit.entry.fiscal.period = current.fiscal.period
    
    if transaction.amount not empty
        audit.entry.financial.impact = true
```

Records business context for audit entry.

### Example 12: Multiple Effective Date Changes

```
Audit Entry Rules
    increment audit.entry.sequence.number
    
    if audit.entry.sequence.number > 1
        audit.entry.multiple.changes = true
        audit.entry.previous.effective.date = previous.effective.date
```

Tracks multiple audit entries in single transaction.

### Example 13: Audit Severity Assignment

```
Audit Entry Rules
    audit.entry.severity = "Low"
    
    if critical.fields.changed
        audit.entry.severity = "High"
    else if important.fields.changed
        audit.entry.severity = "Medium"
    
    if audit.entry.severity = "High"
        audit.entry.immediate.review = true
```

Assigns severity levels to audit entries.

### Example 14: Data Quality Checks

```
Audit Entry Rules
    if data.quality.score < minimum.quality.threshold
        audit.entry.data.quality.issue = true
        audit.entry.quality.score = data.quality.score
        
        send notification
            to data.quality.team
            description is "Data quality issue detected"
```

Monitors and flags data quality issues.

### Example 15: Regulatory Compliance

```
Audit Entry Rules
    if sox.controlled.fields.changed
        audit.entry.sox.relevant = true
        audit.entry.control.id = sox.control.id
        
        constraint change.authorization not empty
            "Authorization required for SOX-controlled changes"
        
        log "SOX-controlled field modified: " + changed.field.names
```

Ensures regulatory compliance in audit trail.

## Best Practices

1. **Capture Comprehensive Context**
   - Record user information
   - Capture business context
   - Note change magnitude
   - Track timing information

2. **Classify Audit Entries**
   - Categorize by change type
   - Assign severity levels
   - Flag significant changes
   - Tag compliance-relevant entries

3. **Validate Audit Data**
   - Require change reasons
   - Validate completeness
   - Check data quality
   - Enforce audit requirements

4. **Keep Rules Efficient**
   - Minimize processing
   - Avoid heavy calculations
   - Use efficient queries
   - Consider performance impact

5. **Handle Multiple Entries**
   - Track sequence numbers
   - Handle effective date changes
   - Maintain entry relationships
   - Document multiple-entry scenarios

6. **Document Audit Logic**
   - Explain classification rules
   - Note validation requirements
   - Describe notification triggers
   - Document compliance needs

7. **Test Thoroughly**
   - Test all change scenarios
   - Verify multiple entries
   - Test future-dated transactions
   - Validate RequestAction behavior

## Common Patterns

### Enhancement Pattern
```
Audit Entry Rules
    audit.entry.<custom.field> = <calculated.value>
    audit.entry.<classification> = <category>
```

### Validation Pattern
```
Audit Entry Rules
    constraint <audit.requirement>
        <error.message>
```

### Notification Pattern
```
Audit Entry Rules
    if <significant.change>
        send notification
            to <reviewer>
            description is <change.description>
```

### Metrics Pattern
```
Audit Entry Rules
    audit.entry.<metric> = <calculation>
    if <threshold.exceeded>
        audit.entry.<flag> = true
```

### Compliance Pattern
```
Audit Entry Rules
    if <regulated.fields.changed>
        audit.entry.<compliance.category> = <regulation>
        constraint <compliance.requirement>
            <compliance.message>
```

## Multiple Audit Entries

When effective date changes during a transaction:
1. First audit entry created for initial effective date
2. Audit Entry Rules fire for first entry
3. Effective date changed
4. Second audit entry created for new effective date
5. Audit Entry Rules fire again for second entry

**Best Practice:** Use sequence numbers or flags to track multiple entries.

## Timing Scenarios

### Scenario 1: Regular Transaction
1. Transaction executes
2. Audit Entry Rules fire
3. Audit log entry created
4. Commit Rules fire
5. Transaction commits

### Scenario 2: RequestAction
1. RequestAction initiated (Audit Entry Rules do NOT fire)
2. Background processing occurs
3. RequestAction completes
4. Audit Entry Rules fire
5. Audit log entry created

### Scenario 3: Future-Dated Transaction
1. Future-dated transaction created
2. Audit Entry Rules fire (records initiation)
3. Audit log entry created
4. Effective date arrives
5. Commit Rules fire (Audit Entry Rules do NOT fire again)

## Troubleshooting

### Issue: Audit Entry Rules not firing

**Possible Causes:**
- No audit log entry being created
- Business class not configured for auditing
- RequestAction not completed
- Syntax errors in rules

**Solutions:**
- Verify audit configuration
- Check RequestAction status
- Review rule syntax
- Check audit log settings

### Issue: Multiple unexpected audit entries

**Possible Causes:**
- Effective date changing in transaction
- Multiple updates in same transaction
- Audit configuration issues
- Logic creating multiple entries

**Solutions:**
- Review effective date logic
- Check transaction flow
- Verify audit configuration
- Add sequence tracking

### Issue: Audit validation failing

**Possible Causes:**
- Required audit data missing
- Constraint conditions too strict
- Data not available in context
- Timing issues

**Solutions:**
- Review constraint logic
- Check data availability
- Verify context
- Test with various scenarios

### Issue: Performance problems

**Possible Causes:**
- Heavy processing in rules
- Complex calculations
- Many notifications
- Inefficient queries

**Solutions:**
- Optimize calculations
- Minimize processing
- Batch notifications
- Use efficient queries

## Related Concepts

- [Commit Rules](commit-rules.md) - Final transaction rules
- [Field Rules](field-rules.md) - Field-level rules
- [Action Rules](action-rules.md) - Action-level rules
- [Audit Index Fields](audit-index-fields.md) - Audit indexing
- [Business Class Definition](business-class-definition.md) - Business class structure

## Notes

- Audit Entry Rules fire just before audit log entry creation
- Multiple audit entries can be created if effective date changes
- Rules do NOT fire when RequestAction is initiated, only when completed
- Rules DO fire when future-dated transaction is initiated
- Rules do NOT fire when future-dated transaction becomes effective
- This timing is opposite of Commit Rules for future-dated transactions
- Use Audit Entry Rules to enhance, validate, and classify audit data
- Keep rules efficient to avoid performance impact
- Consider multiple audit entries in same transaction
- Test thoroughly with RequestActions and future-dated transactions
- Document audit classification and validation logic
- Audit Entry Rules have access to both business object and audit entry fields
