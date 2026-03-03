# Form Invokes

## Overview

Form Invokes define automated form invocation behaviors that trigger when certain conditions are met. They enable cascading form operations, automatic data population, and complex multi-form workflows. Form invokes are particularly useful for creating related records, updating dependent data, or triggering business processes.

## Syntax

```
<FormInvokeName>
    (<FormInvokeRule> | <ControlRule>)
    …

FormInvokeRule ::=
    invoke <InvocationTarget>
    [fill in [blank] fields from <RelatedLink>]
    [except invoked.(<FullFieldName> | <FieldGroupName>)…]
    [invoked.<FullFieldName> <AssignmentOp> <RelatedValue>]
    …
    [<Rule>…]
```

## Components

### Form Invoke Name

A unique identifier for the form invoke definition following LPL naming conventions (uppercase alphanumeric).

### Invocation Target

The form or action to be invoked. This can be:
- A form name
- An action name
- A business task reference

### Fill In Fields

```
fill in [blank] fields from <RelatedLink>
```

Automatically populates fields in the invoked form from a related object.

**Behavior:**
- Copies field values from the specified related link
- `blank` keyword: Only fills fields that are currently empty
- Without `blank`: Fills all matching fields regardless of current value

**Constraint:**
- `<RelatedLink>` cannot be the same as the invoke's target `<RelatedLink>`

### Except Clause

```
except invoked.(<FullFieldName> | <FieldGroupName>)
…
```

Excludes specific fields or field groups from the fill-in operation.

**Use Cases:**
- Prevent overwriting critical fields
- Exclude calculated fields
- Skip fields that should remain user-entered

### Field Assignment

```
invoked.<FullFieldName> <AssignmentOp> <RelatedValue>
```

Explicitly sets field values in the invoked form.

**The `invoked` Keyword:**
- Addresses fields in the target form/relation being invoked
- Distinguishes invoked form fields from current form fields
- Required prefix for field references in the invoked context

### Rules

Additional business rules that apply to the form invoke, such as conditions, validations, or further processing logic.

## Assignment Operators

| Operator | Description |
|----------|-------------|
| `=` | Direct assignment |
| `+=` | Add to existing value |
| `-=` | Subtract from existing value |
| `*=` | Multiply existing value |
| `/=` | Divide existing value |

## Examples

### Example 1: Simple Form Invoke

```
CREATE_ORDER_LINE
    invoke ORDER_LINE_FORM
    invoked.order.number = order.number
    invoked.customer = customer
```

Invokes the order line form and sets the order number and customer.

### Example 2: Fill In Blank Fields

```
COPY_ADDRESS
    invoke ADDRESS_FORM
    fill in blank fields from customer.primary.address
    invoked.address.type = "Shipping"
```

Copies address data from customer's primary address, only filling empty fields, and sets the type to "Shipping".

### Example 3: Fill All Fields

```
DUPLICATE_RECORD
    invoke PRODUCT_FORM
    fill in fields from current.product
    invoked.product.code = new.product.code
    invoked.status = "Draft"
```

Copies all fields from current product and overrides specific fields.

### Example 4: Except Clause

```
COPY_EMPLOYEE
    invoke EMPLOYEE_FORM
    fill in blank fields from template.employee
    except invoked.employee.id
    except invoked.hire.date
    except invoked.salary
    invoked.status = "Pending"
```

Copies employee template data but excludes sensitive fields.

### Example 5: Field Group Exception

```
CREATE_FROM_TEMPLATE
    invoke DOCUMENT_FORM
    fill in fields from document.template
    except invoked.AUDIT_FIELDS
    except invoked.APPROVAL_FIELDS
    invoked.document.date = today
```

Fills from template but excludes entire field groups.

### Example 6: Conditional Invoke with Rules

```
ESCALATE_ISSUE
    invoke ESCALATION_FORM
    invoked.issue.number = issue.number
    invoked.escalation.level = current.level + 1
    invoked.assigned.to = manager
    if priority = "Critical"
        invoked.due.date = today + 1 days
    else
        invoked.due.date = today + 3 days
```

Invokes escalation form with conditional logic for due dates.

### Example 7: Multiple Field Assignments

```
CREATE_INVOICE
    invoke INVOICE_FORM
    fill in blank fields from order
    invoked.invoice.date = today
    invoked.due.date = today + payment.terms.days
    invoked.status = "Pending"
    invoked.created.by = current.user
```

Creates invoice from order with multiple calculated fields.

### Example 8: Cascading Invokes

```
COMPLETE_ORDER
    invoke SHIPMENT_FORM
    invoked.order.number = order.number
    invoked.ship.date = today
    
    invoke INVOICE_FORM
    invoked.order.number = order.number
    invoked.invoice.date = today
```

Triggers multiple form invokes in sequence.

### Example 9: Arithmetic Assignment

```
ADJUST_QUANTITY
    invoke INVENTORY_ADJUSTMENT
    invoked.product = product
    invoked.location = location
    invoked.adjustment.quantity += requested.quantity
    invoked.adjustment.date = today
```

Uses arithmetic operator to adjust quantity.

### Example 10: Complex Fill Pattern

```
CREATE_QUOTE_FROM_ORDER
    invoke QUOTE_FORM
    fill in blank fields from order
    except invoked.order.number
    except invoked.order.date
    except invoked.status
    except invoked.PRICING_FIELDS
    invoked.quote.number = next.quote.number
    invoked.quote.date = today
    invoked.status = "Draft"
    invoked.valid.until = today + 30 days
```

Creates a quote from an order with selective field copying.

### Example 11: Related Link Fill

```
CREATE_CONTACT
    invoke CONTACT_FORM
    fill in blank fields from customer.primary.contact
    invoked.customer = customer
    invoked.contact.type = "Secondary"
    invoked.active = true
```

Fills contact form from a related customer contact.

### Example 12: Conditional Fill

```
COPY_CONFIGURATION
    invoke CONFIG_FORM
    if use.template
        fill in fields from config.template
        except invoked.config.id
        except invoked.effective.date
    invoked.config.id = new.config.id
    invoked.effective.date = effective.date
    invoked.created.by = current.user
```

Conditionally fills from template based on user choice.

## Use Cases

### Record Creation Workflows
- Create related records automatically
- Duplicate existing records with modifications
- Generate child records from parent data

### Data Population
- Copy data from templates
- Populate forms from related objects
- Set default values based on context

### Multi-Step Processes
- Cascade form invocations
- Trigger dependent operations
- Implement approval workflows

### Data Transformation
- Convert between record types
- Migrate data with field mapping
- Create derived records

## Best Practices

1. **Use Fill In Blank Strategically**
   - Use `blank` keyword to preserve user-entered data
   - Without `blank`, be aware you're overwriting existing values
   - Document which approach you're using and why

2. **Explicit Field Assignments**
   - Always set key fields explicitly
   - Override template values when needed
   - Set audit fields (dates, users) appropriately

3. **Exception Handling**
   - Exclude auto-generated fields (IDs, timestamps)
   - Skip calculated or derived fields
   - Protect sensitive data fields
   - Use field groups for bulk exclusions

4. **The invoked Keyword**
   - Always use `invoked.` prefix for target form fields
   - Distinguishes source from target fields
   - Makes code more readable and maintainable

5. **Performance Considerations**
   - Minimize cascading invokes
   - Consider transaction boundaries
   - Test with realistic data volumes
   - Monitor form invoke chains

6. **Error Handling**
   - Validate required fields are set
   - Handle missing related data gracefully
   - Provide meaningful error messages
   - Test failure scenarios

7. **Documentation**
   - Document the purpose of each invoke
   - Explain field mapping logic
   - Note any business rules applied
   - Describe expected outcomes

## Common Patterns

### Template Pattern
```
CREATE_FROM_TEMPLATE
    invoke TARGET_FORM
    fill in blank fields from template
    except invoked.KEY_FIELDS
    invoked.id = new.id
    invoked.created.date = today
```

### Duplication Pattern
```
DUPLICATE_RECORD
    invoke SAME_FORM
    fill in fields from current.record
    except invoked.unique.id
    invoked.unique.id = generate.new.id
    invoked.status = "Draft"
```

### Cascading Creation Pattern
```
CREATE_MASTER_DETAIL
    invoke DETAIL_FORM
    invoked.master.key = master.key
    invoked.sequence = next.sequence
```

### Conversion Pattern
```
CONVERT_TYPE
    invoke NEW_TYPE_FORM
    fill in blank fields from old.type.record
    except invoked.TYPE_SPECIFIC_FIELDS
    invoked.type = new.type
    invoked.conversion.date = today
```

## Troubleshooting

### Issue: Fields not populating

**Possible Causes:**
- Related link returns null
- Field names don't match
- Using `blank` keyword but fields already have values
- Fields excluded by except clause

**Solutions:**
- Verify related link is valid
- Check field name spelling and case
- Remove `blank` keyword if overwriting is intended
- Review except clause exclusions
- Add explicit field assignments

### Issue: Wrong data being copied

**Possible Causes:**
- Incorrect related link reference
- Missing except clauses
- Field name collision
- Unexpected data in source

**Solutions:**
- Verify related link points to correct object
- Add except clauses for problematic fields
- Use explicit assignments for critical fields
- Validate source data quality

### Issue: Performance degradation

**Possible Causes:**
- Too many cascading invokes
- Large data copies
- Complex related link traversals
- Inefficient rules

**Solutions:**
- Reduce invoke chain depth
- Copy only necessary fields
- Optimize related link queries
- Simplify rule logic
- Consider batch operations

### Issue: Invoked keyword not working

**Possible Causes:**
- Missing `invoked.` prefix
- Incorrect field reference
- Scope confusion

**Solutions:**
- Always use `invoked.` prefix for target fields
- Verify field exists in target form
- Check field path is correct
- Review scope and context

### Issue: Except clause not excluding fields

**Possible Causes:**
- Field name mismatch
- Field group not defined
- Except clause after fill statement
- Syntax error

**Solutions:**
- Verify exact field names
- Check field group membership
- Place except clause immediately after fill statement
- Review syntax carefully

## Control Rules

Form invokes can include control rules that determine when and how the invoke executes:

```
<FormInvokeName>
    if <Condition>
        invoke <InvocationTarget>
        invoked.field = value
    else if <Condition>
        invoke <AlternativeTarget>
        invoked.field = other.value
```

Control rules enable conditional invocation logic based on runtime conditions.

## Related Concepts

- [Actions and Actors](actions-actors.md) - Action definitions and execution
- [Field Definition](field-definition.md) - Field structure and properties
- [Field Groups](field-groups.md) - Grouping fields for bulk operations
- [Relations](relations.md) - Navigating between business classes
- [References](references.md) - Related values and links
- [Conditions](conditions.md) - Boolean expressions for control flow
- [Business Class Definition](business-class-definition.md) - Business class structure

## Notes

- The `invoked` keyword is essential for addressing fields in the target form
- Related link in `fill in from` cannot be the same as the invoke's target
- Fill operations can be restricted to blank fields only using the `blank` keyword
- Except clauses can reference individual fields or entire field groups
- Multiple form invokes can be chained within a single definition
- Assignment operators support arithmetic operations (+=, -=, *=, /=)
- Rules within form invokes can include conditions, validations, and other logic
- Form invokes execute within the transaction context of the triggering form
- Consider security implications when copying data between forms
- Test form invoke chains thoroughly to ensure expected behavior
