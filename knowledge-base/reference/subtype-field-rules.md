# SubType Field Rules

## Overview

SubType Field Rules define conditional field behavior based on business object subtypes. They enable different validation, defaulting, and business logic for the same field depending on the subtype or state of the business object. This supports polymorphic behavior and type-specific field rules.

## Syntax

```
SubType <RelatedCondition> Field Rules
    [(merge | replace) specifications]
    <FieldRules>
    …
```

**Merge/Replace Specifications:**
- Valid only for LHF (Landmark Hosted Framework) LPL
- `merge` - Merges specifications with base (default behavior)
- `replace` - Replaces base specifications entirely

## Components

### SubType Condition

The `<RelatedCondition>` defines when these field rules apply. This is typically based on:
- Type discriminator fields
- Status or state fields
- Category or classification fields
- Any boolean condition that defines a subtype

### Field Rules

Standard field rules that apply only when the subtype condition is true.

## Use Cases

### Type-Specific Validation
- Different required fields per type
- Type-specific constraints
- Conditional field visibility
- Type-dependent formats

### State-Based Behavior
- Status-specific validation
- State-dependent defaults
- Conditional editability
- Workflow-based rules

### Category-Specific Logic
- Category-dependent calculations
- Type-specific business rules
- Conditional field relationships
- Specialized validation

### Polymorphic Behavior
- Subclass-specific rules
- Variant-specific logic
- Conditional field behavior
- Type-dependent processing

## Examples

### Example 1: Type-Specific Required Fields

```
SubType document.type = "Invoice" Field Rules
    INVOICE_NUMBER
        required "Invoice number is required for invoice documents"
    
    DUE_DATE
        required "Due date is required for invoices"
        constraint due.date >= invoice.date
            "Due date must be on or after invoice date"
```

Different required fields for invoice document type.

### Example 2: Status-Based Editability

```
SubType status = "Approved" Field Rules
    AMOUNT
        cannot be changed "Amount cannot be changed after approval"
    
    DESCRIPTION
        cannot be changed "Description cannot be changed after approval"
```

Prevents field changes in approved status.

### Example 3: Category-Specific Defaults

```
SubType product.category = "Electronics" Field Rules
    WARRANTY_PERIOD
        default to 365
    
    RETURN_WINDOW
        default to 30
```

Category-specific default values.

### Example 4: State-Dependent Validation

```
SubType order.status = "Shipped" Field Rules
    TRACKING_NUMBER
        required "Tracking number required for shipped orders"
    
    SHIP_DATE
        required "Ship date required for shipped orders"
        constraint ship.date <= today
            "Ship date cannot be in the future"
```

Validation rules specific to shipped status.

### Example 5: Multiple Conditions

```
SubType (transaction.type = "Sale") and (amount > 10000) Field Rules
    MANAGER_APPROVAL
        required "Manager approval required for large sales"
    
    APPROVAL_DATE
        required "Approval date required"
        default to today
```

Rules based on multiple conditions.

### Example 6: Type-Specific Calculations

```
SubType employee.type = "Hourly" Field Rules
    HOURLY_RATE
        required "Hourly rate required for hourly employees"
    
    HOURS_WORKED
        required "Hours worked required"
    
    GROSS_PAY
        force default to hourly.rate * hours.worked
```

Calculation logic specific to employee type.

### Example 7: Conditional Field Relationships

```
SubType asset.type = "Vehicle" Field Rules
    VIN_NUMBER
        required "VIN required for vehicles"
    
    LICENSE_PLATE
        required "License plate required"
    
    MILEAGE
        required "Mileage required for vehicles"
        constraint mileage >= 0
            "Mileage cannot be negative"
```

Vehicle-specific field requirements.

### Example 8: State-Based Defaults

```
SubType request.status = "Draft" Field Rules
    PRIORITY
        default to "Medium"
    
    REQUESTED_BY
        default to current.user
    
    REQUEST_DATE
        default to today
```

Default values for draft status.

### Example 9: Type-Specific Constraints

```
SubType payment.method = "Credit Card" Field Rules
    CARD_NUMBER
        required "Card number required for credit card payments"
        must be numeric
    
    EXPIRATION_DATE
        required "Expiration date required"
        constraint expiration.date > today
            "Card has expired"
    
    CVV
        required "CVV required"
        constraint length(cvv) = 3 or length(cvv) = 4
            "CVV must be 3 or 4 digits"
```

Credit card specific validation.

### Example 10: Polymorphic Behavior

```
SubType contact.type = "Customer" Field Rules
    CUSTOMER_NUMBER
        required "Customer number required"
        cannot be changed
    
    CREDIT_LIMIT
        required "Credit limit required for customers"
        constraint credit.limit >= 0
            "Credit limit cannot be negative"
```

Customer-specific fields and validation.

### Example 11: Workflow State Rules

```
SubType approval.state = "Pending Manager Review" Field Rules
    MANAGER_COMMENTS
        required "Manager comments required during review"
    
    REVIEW_DATE
        force default to today
    
    REVIEWER
        force default to current.user
```

Rules specific to approval workflow state.

### Example 12: Complex Type Conditions

```
SubType (item.type = "Serialized") and (item.category = "Equipment") Field Rules
    SERIAL_NUMBER
        required "Serial number required for serialized equipment"
        constraint length(serial.number) >= 8
            "Serial number must be at least 8 characters"
    
    MANUFACTURER
        required "Manufacturer required"
    
    MODEL_NUMBER
        required "Model number required"
```

Rules for specific type and category combination.

## Best Practices

1. **Clear Subtype Definitions**
   - Use meaningful condition expressions
   - Document subtype purpose
   - Avoid overlapping conditions
   - Keep conditions simple

2. **Consistent Rule Organization**
   - Group related subtype rules
   - Use consistent naming
   - Document rule purpose
   - Maintain rule hierarchy

3. **Avoid Rule Conflicts**
   - Ensure subtypes are mutually exclusive when needed
   - Handle overlapping conditions explicitly
   - Test all subtype combinations
   - Document precedence rules

4. **Performance Considerations**
   - Keep conditions efficient
   - Minimize complex evaluations
   - Cache condition results when possible
   - Avoid redundant checks

5. **Maintainability**
   - Use merge specifications for extensions
   - Document subtype logic
   - Keep rules focused
   - Avoid deep nesting

6. **Testing**
   - Test each subtype independently
   - Verify condition boundaries
   - Test subtype transitions
   - Validate rule interactions

7. **Error Messages**
   - Provide subtype-specific messages
   - Include context in errors
   - Make messages actionable
   - Consider user perspective

## Common Patterns

### Type Discriminator Pattern
```
SubType <type.field> = "<TypeValue>" Field Rules
    <TypeSpecificFieldRules>
```

### Status-Based Pattern
```
SubType status = "<StatusValue>" Field Rules
    <StatusSpecificFieldRules>
```

### Multi-Condition Pattern
```
SubType (<condition1>) and (<condition2>) Field Rules
    <ConditionalFieldRules>
```

### Hierarchical Type Pattern
```
SubType category = "<Category>" Field Rules
    SubType subcategory = "<Subcategory>" Field Rules
        <SpecificFieldRules>
```

## Subtype Rule Evaluation

1. **Condition Evaluation** - SubType condition is evaluated
2. **Rule Application** - If true, subtype rules apply
3. **Rule Merging** - Rules merge with or replace base rules
4. **Execution** - Combined rules execute in context

## Merge vs Replace Behavior

### Merge (Default)
- Combines subtype rules with base field rules
- Subtype rules augment base behavior
- Both sets of rules apply
- Useful for adding type-specific validation

### Replace
- Subtype rules completely replace base rules
- Only subtype rules apply when condition is true
- Base rules ignored for this subtype
- Useful for completely different behavior

## Interaction with Other Rules

SubType Field Rules interact with:
- **Base Field Rules** - Merged or replaced based on specification
- **Translation Field Rules** - Can have subtype-specific translation rules
- **Action Rules** - Subtype conditions can affect action behavior
- **State Cycles** - Often used with state-based subtypes

## Troubleshooting

### Issue: Rules not applying for subtype

**Possible Causes:**
- Condition not evaluating to true
- Syntax error in condition
- Field not in scope
- Merge/replace conflict

**Solutions:**
- Verify condition logic
- Check field references
- Test condition independently
- Review merge/replace specification

### Issue: Conflicting rules between subtypes

**Possible Causes:**
- Overlapping subtype conditions
- Unclear precedence
- Multiple matching subtypes
- Merge conflicts

**Solutions:**
- Make conditions mutually exclusive
- Document precedence rules
- Use replace specification when needed
- Test all combinations

### Issue: Performance degradation

**Possible Causes:**
- Complex condition evaluation
- Too many subtype rules
- Inefficient field references
- Redundant evaluations

**Solutions:**
- Simplify conditions
- Consolidate rules
- Optimize field access
- Cache condition results

### Issue: Unexpected rule behavior

**Possible Causes:**
- Merge vs replace confusion
- Condition edge cases
- Rule execution order
- Context issues

**Solutions:**
- Verify merge/replace specification
- Test boundary conditions
- Review rule order
- Check context availability

## Related Concepts

- [Field Rules](field-rules.md) - Base field rules
- [Conditions](conditions.md) - Boolean expressions
- [Business Class Patterns](business-class-patterns.md) - Design patterns including specialization
- [Action Rules](action-rules.md) - Action-level rules
- [Translation Field Rules](translation-field-rules.md) - Translation-specific rules

## Notes

- SubType conditions are evaluated at runtime
- Merge is the default behavior for LHF LPL
- Replace specification completely overrides base rules
- Conditions can reference any field in scope
- Multiple SubType sections can exist for different conditions
- SubType rules support polymorphic behavior
- Conditions should be efficient to evaluate
- Test all subtype combinations thoroughly
- Document subtype logic clearly
- Consider using state cycles for state-based subtypes
- SubType rules are particularly useful with specialization pattern
- Merge/replace specifications valid only for LHF LPL
