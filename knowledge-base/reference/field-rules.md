# Field Rules

## Overview

Field Rules define validation, defaulting, and behavior logic for individual fields within business classes. They control data entry, enforce business constraints, and automate field population. Field rules execute in the context of a specific field and can reference other fields in the same business class.

## Syntax

```
FieldRules ::=
    [(merge | replace) specifications]
    <FullFieldName>
    <Rule>
    …
```

**Merge/Replace Specifications:**
- Valid only for LHF (Landmark Hosted Framework) LPL
- `merge` - Merges specifications with base (default behavior)
- `replace` - Replaces base specifications entirely

Field name can include template operator `<A>`; if it does, rules can have matching template operators.

## Rule Types

Rules can be categorized into several types:

- **Control Rules** - Conditional logic and loops
- **General Rules** - Valid in all contexts
- **Field Rules** - Valid in field context only
- **Field Create Rules** - Valid in field context with Create action
- **Group Rules** - Valid in GroupField only
- **Array Rules** - Valid in ArrayField only
- **Action Rules** - Valid in Action context only
- **Form Invoke Rules** - Valid in Form Invokes section only

## Assignment Operators

```
AssignmentOp ::= ( =      // Direct assignment
                 | +=     // Add to existing value
                 | -=     // Subtract from existing value
                 | *=     // Multiply existing value
                 | /=     // Divide existing value
                 | ^=     // Exponentiation
                 | %=)    // Modulus operator
```

## Control Rules

Control rules define conditional logic and iteration.

### If-Else Statement

```
if <Condition>
    <Rule>
    …
[else]
    <Rule>
    …
```

Executes rules conditionally based on boolean expression.

### For Each Loop

```
for each [<Parens><LPLConstructName><Parens>]
    ( [<Distinct> in] <otm RelatedLink>
    | <array RelatedField>
    | <iteration FullFieldName>)
    [while <Condition>]
    <Rule>
    …
    [each[<Parens><LPLConstructName><Parens>].<RelatedField>]
```

**Addressing Fields in Loop:**
- Use `each.<FieldName>` to reference fields in the iterated object
- For nested loops, use `each1.<FieldName>` for outer loop
- If `each` qualifier not provided, uses innermost loop context

**Array Iteration:**
- Drives loop through each occurrence in array
- All array fields presumed to have same index as primary occurring field
- Must address occurring field directly (e.g., `each.OccurringField`)

**Iteration Field:**
- Must first be initialized with `first iteration of…`

### End For Each

```
end for each
```

Breaks out of `for each` loop. This is a control rule, not a syntactical structure to end the statement.

### While Loop

```
while <Condition>
    <Rule>
    …
```

Executes rules repeatedly while condition is true.

### End While

```
end while
```

Breaks out of `while` loop. This is a control rule, not a syntactical structure to end the statement.

## General Rules

Valid in all contexts.

### Confirmation Required

```
confirmation required [<Message>]
```

Prompts user for confirmation before proceeding.

### Constraint

```
constraint <Condition>
    <Message>
```

Enforces business rule constraint. Message is required.

### Assignment

```
<RelatedField> <AssignmentOp> <RelatedValue>
```

Assigns value to a field. `<RelatedField>` valid only in scope of business object (not field rules).

### Increment/Decrement

```
increment <RelatedField> [by <RelatedValue>] [when <Condition>]
decrement <RelatedField> [by <RelatedValue>] [when <Condition>]
```

Increments or decrements field value, optionally by specified amount and conditionally.

### Initialize

```
initialize [(<FullFieldName> | <FieldGroupName>)]
```

Resets field or field group to initial/default state.

### Display/Log Message

```
(display | log) <Message>
```

- `display` - Shows message on console (noop in production)
- `log` - Writes to standard log

### Add Action Tag

```
add action tag <ActionTag>
```

Adds an action tag to the execution path for tracking or conditional logic.

### Include Rule Block

```
include <RuleBlockName>
```

Copies named block of rules in-line.

### Round

```
round <FullFieldName> [(up | down)] to nearest [exact] <RelatedValue>
```

**Without `exact`:**
- Rounds to nearest multiple of `<RelatedValue>`
- Example: `round amount to nearest 0.01` rounds to hundredths

**With `exact`:**
- Rounds to exact specified value
- Example: `round price to nearest exact .95` always ends in `.95`

### Clear Cache

```
clear in-memory cache [for <BusinessClass>]
```

Clears in-memory cache of all business class instances or specific business class.

### Synchronize

```
synchronize on <RelatedLink>
```

Causes threads where RelatedLink points to same record to single-thread from this point until end of transaction.

## Field Rules

Valid in field context only.

### Default Value

```
[(force | dynamic)] default [<FullFieldName>] to <RelatedValue>
[( default as a group
 | default individual fields)]
```

**Standard Default:**
- Fills field if blank

**Force Default:**
- Defaults field whether entered or not

**Dynamic Default:**
- Leaves field blank and dynamically defaults when referenced

**Group Behavior:**
- `default as a group` - Default entire group together
- `default individual fields` - Default each field separately

### Required

```
required [<Message>]
```

Makes field mandatory. Default message: "Field is required"

### Cannot Be Entered

```
cannot be entered [<Message>]
```

Prevents user from entering value. Default message: "Field cannot be entered"

### Cannot Be Changed

```
cannot be changed [<Message>]
```

Prevents modification after initial entry. Default message: "Field cannot be changed"

### Must Be Numeric

```
must be numeric [<Message>]
```

Validates field contains numeric value. Default message: "Field must be numeric"

### Initial Value

```
initial value is <RelatedValue> [when <SimpleCondition>]
```

Sets initial value for field. Valid on Field Rules and Parameter Rules only.

### Field Size

```
field size is <RelatedField>
```

Dynamically sets field size based on another field's value.

## Field Create Rules

Valid in field context with Create action.

### Autosequence

```
autosequence [<FullFieldName>] [when blank]
    [using (<RelatedField> | <SetName>)]
    [manual range is <RelatedField>]
    [minimize contention [and gaps]]
```

**Behavior:**
- If no field specified: Find last instance in context and increment by 1
  - Field must be symbolic or relative key
- If SetName specified: Find last instance using SetName
  - SetName must contain this field
- If RelatedField specified: Increment RelatedField by 1 and use that value

**Options:**
- `when blank` - Only autosequence if field is blank (not valid with minimize contention)
- `manual range is <RelatedField>` - Must be group field implementing FieldRange
- `minimize contention` - Not valid with RelatedField; can result in gaps
  - Typically retrieves block of numbers
  - With `and gaps`: Retrieves one at a time to minimize gaps

## Group Rules

Valid in GroupField only.

### Always Default

```
always default ( individual fields
               | as a group)
```

- `individual fields` - Prevents group default rule from being overridden
- `as a group` - Forces group-level defaulting

### Mutually Exclusive

```
mutually exclusive [<Message>]
```

Ensures only one field in group can have a value.

## Array Rules

Valid in ArrayField only.

### Array Size

```
array size is <RelatedField>
```

Dynamically sets array size based on field value.

### Sequence Validation

```
edit increasing sequence
make increasing sequence
edit decreasing sequence
make decreasing sequence
```

- `edit` - Validates sequence order
- `make` - Enforces sequence order

### Contiguous Validation

```
edit contiguous
make contiguous
```

- `edit` - Validates no gaps in array
- `make` - Enforces no gaps in array

### No Duplicates

```
no duplicates [on <FullFieldName> [, <FullFieldName>…]]
```

Ensures unique values in array. Field must be member field within occurring GroupField.

## Examples

### Example 1: Simple Default

```
DISCOUNT_PERCENT
    default to 10
```

### Example 2: Conditional Default

```
SHIPPING_METHOD
    if order.total > 100
        default to "Free Shipping"
    else
        default to "Standard"
```

### Example 3: Required with Custom Message

```
CUSTOMER_NAME
    required "Customer name must be provided"
```

### Example 4: Cannot Be Changed

```
ORDER_NUMBER
    cannot be changed "Order number cannot be modified after creation"
```

### Example 5: Force Default

```
STATUS
    force default to "Active"
```

### Example 6: Dynamic Default

```
CALCULATED_TOTAL
    dynamic default to line.items.sum
```

### Example 7: Constraint

```
END_DATE
    constraint end.date >= start.date
        "End date must be on or after start date"
```

### Example 8: For Each Loop

```
TOTAL_AMOUNT
    total.amount = 0
    for each order.line set
        total.amount += each.line.amount
```

### Example 9: Nested For Each

```
GRAND_TOTAL
    grand.total = 0
    for each order set
        for each1 each.line set
            grand.total += each1.amount
```

### Example 10: Autosequence

```
INVOICE_NUMBER
    autosequence when blank
        using INVOICE_NUMBER_SET
        minimize contention and gaps
```

### Example 11: Increment

```
ATTEMPT_COUNT
    increment attempt.count by 1
        when status = "Retry"
```

### Example 12: Round

```
UNIT_PRICE
    round unit.price to nearest 0.01
```

### Example 13: Round Exact

```
SALE_PRICE
    round sale.price down to nearest exact .99
```

### Example 14: Mutually Exclusive

```
PAYMENT_METHOD
    mutually exclusive "Select only one payment method"
```

### Example 15: Array No Duplicates

```
SKILL_CODES
    no duplicates on skill.code
```

### Example 16: Conditional Assignment

```
DISCOUNT_AMOUNT
    if customer.type = "Premium"
        discount.amount = order.total * 0.15
    else if customer.type = "Standard"
        discount.amount = order.total * 0.10
    else
        discount.amount = 0
```

### Example 17: While Loop

```
BATCH_PROCESS
    while records.remaining > 0
        process.next.record
        decrement records.remaining
```

### Example 18: Initialize

```
RESET_FORM
    initialize ORDER_FIELDS
```

### Example 19: Synchronize

```
INVENTORY_UPDATE
    synchronize on product
    current.quantity -= order.quantity
```

### Example 20: Clear Cache

```
REFRESH_DATA
    clear in-memory cache for PRODUCT
```

## Use Cases

### Data Validation
- Required field enforcement
- Format validation
- Range checking
- Cross-field validation

### Automatic Defaulting
- Standard defaults
- Conditional defaults
- Calculated defaults
- Dynamic defaults

### Business Rules
- Constraints
- Mutually exclusive options
- Sequence validation
- Uniqueness enforcement

### Data Manipulation
- Calculations
- Aggregations
- Transformations
- Rounding

### Sequence Generation
- Auto-incrementing IDs
- Order numbers
- Invoice numbers
- Batch numbers

## Best Practices

1. **Keep Rules Simple**
   - One rule per logical operation
   - Avoid complex nested conditions
   - Use rule blocks for reusable logic

2. **Provide Clear Messages**
   - Custom messages for constraints
   - User-friendly error text
   - Actionable guidance

3. **Use Appropriate Default Types**
   - Standard default for user-overridable values
   - Force default for system-controlled values
   - Dynamic default for calculated values

4. **Optimize Performance**
   - Minimize loop iterations
   - Use efficient conditions
   - Cache frequently-accessed values
   - Avoid unnecessary calculations

5. **Handle Edge Cases**
   - Null/empty values
   - Boundary conditions
   - Concurrent updates
   - Error scenarios

6. **Document Complex Logic**
   - Comment non-obvious rules
   - Explain business rationale
   - Note dependencies
   - Describe expected behavior

## Related Concepts

- [Conditions](conditions.md) - Boolean expressions
- [Field Definition](field-definition.md) - Field structure
- [References](references.md) - Related values
- [Messages](messages.md) - User messages
- [Business Class Definition](business-class-definition.md) - Business class structure

## Notes

- Control rules have recursive nature; rules under conditions can be other control rules
- `each` keyword addresses fields in for-each loop scope
- Nested loops use `each1`, `each2`, etc. for outer loops
- `end for each` and `end while` are control rules, not syntactical structures
- Assignment rules with `<RelatedField>` valid only in business object scope
- Force defaults override user-entered values
- Dynamic defaults are evaluated when field is referenced
- Autosequence with minimize contention can result in gaps
- Synchronize causes single-threading until transaction end
- Display messages are noop in production systems
- Merge/replace specifications valid only for LHF LPL; merge is default behavior

