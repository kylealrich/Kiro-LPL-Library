# Relations

## Overview

Relations define navigational paths between business classes, establishing one-to-one (OTO) and one-to-many (OTM) relationships. They enable traversal of the business object model and enforce referential integrity through validation and delete rules.

## Syntax

### Basic Relation Definition

```
(<RelationName> [is a[n] (<BusinessClass> | <KeyField>) set]
| <KeyField> set)
    [<SqlName>]
    [<ClassicName>]
    [<DefaultLabel>]
    [(one-to-one | one-to-many) relation (to <BusinessClass> | using <RelatedLink>)]
    [valid when <Condition>]
    [<AsOfDate>]
    [required]
    [delete (restricted | cascades)]
    [(enable | disable) subset select on dependent relation]
    [disable filtering within db]
    [disable link cache]
    [include deleted record]
    [dynamic mapping is <RelatedValue>]
    [Field Mapping uses <Set>]
    [related.<FullFieldName> <RelationOperator> <RelatedValue>]
    …
    [Instance Selection]
    [include [only] deleted records]
    where <Condition>
```

### Conditional Relation

```
<RelationName>
    [<SqlName>]
    [<ClassicName>]
    <ConditionalRelationControl>

ConditionalRelationControl ::=
    if <Condition>
        (<RelationName> | <ConditionalRelationControl>)
        …
    [else]
        (<RelatedValue> | <ConditionalRelationControl>)
        …
```

### Supporting Definitions

```
Set ::= ( <SetName>
        | [<BusinessClass>] part of key
        | [<KeyField>] (symbolic | level) key
        | bod key
        | update stamp key
        | unique id key)

RelationOperator ::= ( =
                     | >=
                     | >
                     | <=
                     | <
                     | as of)
```

## Relation Types

### KeyField Set (Preferred for One-to-Many)

```
<KeyField> set
```

Automatically generates a one-to-many relation based on a key field. The relation name becomes `<KeyField>Set`.

**Alternative Named Syntax:**
```
<RelationName> is a <KeyField> set
```

Gives a specific name to a KeyField-based one-to-many relation.

### Explicit Business Class Set

```
<RelationName> is a[n] <BusinessClass> set
```

Defines a one-to-many relation to a specific business class.

### Standard Relation

```
<RelationName>
    (one-to-one | one-to-many) relation to <BusinessClass>
```

Explicitly defines the cardinality and target business class.

### Relative Relation

```
<RelationName>
    (one-to-one | one-to-many) relation using <RelatedLink>
```

Builds upon an existing relation to further refine or restrict it.

## Cardinality

### One-to-One (OTO)
- Single related object
- Navigation returns one instance or null
- Typically used for extensions or specializations

### One-to-Many (OTM)
- Multiple related objects
- Navigation returns a collection
- Typically used for detail records or child objects

## Attributes

### Naming Attributes

| Attribute | Description |
|-----------|-------------|
| `<SqlName>` | SQL table/column name override |
| `<ClassicName>` | Classic system name override |
| `<DefaultLabel>` | Human-readable label for UI |

### Validation Attributes

| Attribute | Description |
|-----------|-------------|
| `valid when <Condition>` | Relation is valid only when condition is true |
| `required` | Related object must exist (cannot be null) |

### Delete Behavior

| Attribute | Description |
|-----------|-------------|
| `delete restricted` | Prevents deletion if related records exist |
| `delete cascades` | Deletes related records when parent is deleted |

### Performance Attributes

| Attribute | Description |
|-----------|-------------|
| `disable filtering within db` | Performs filtering in application layer |
| `disable link cache` | Disables caching for this relation |
| `enable subset select on dependent relation` | Optimizes queries for dependent relations |
| `disable subset select on dependent relation` | Disables subset optimization |

### Special Behaviors

| Attribute | Description |
|-----------|-------------|
| `include deleted record` | Includes soft-deleted records (requires DeleteFlag) |
| `include [only] deleted records` | Filters to show only deleted records |
| `<AsOfDate>` | Temporal relation based on effective date |
| `dynamic mapping is <RelatedValue>` | Runtime field mapping logic |

## Field Mapping

### Using a Set

```
Field Mapping uses <Set>
```

Specifies which key fields to use for the relation mapping.

**Available Sets:**
- `<SetName>` - Named set definition
- `part of key` - Uses part-of relationship key
- `[<BusinessClass>] part of key` - Specific business class part-of key
- `symbolic key` - Uses symbolic key fields
- `[<KeyField>] symbolic key` - Specific symbolic key field
- `level key` - Uses array hierarchy level key (valid only on hierarchy fields)
- `bod key` - Uses BOD identifier (requires BODId implementation)
- `update stamp key` - Uses update timestamp (when not virtual)
- `unique id key` - Uses unique identifier (when not disabled)

### Explicit Field Mapping

```
related.<FullFieldName> <RelationOperator> <RelatedValue>
```

Maps specific fields between the current and related business class.

**Relation Operators:**
- `=` - Equality (can be used on any key field)
- `>=`, `>`, `<=`, `<` - Comparison (valid on last key field only)
- `as of` - Temporal matching (valid on date fields only)

## Instance Selection

Filters which instances are included in a one-to-many relation.

```
where <Condition>
```

- Valid with one-to-many relations only
- Can reference fields from both the parent and child objects
- Evaluated at query time
- Multiple dependent OTMs can be used if they have direct OTO back to target

## Conditional Relations

Dynamically selects which relation to use based on runtime conditions.

```
<RelationName>
    if <Condition>
        <RelationName>
    if <Condition>
        <RelationName>
    else
        <RelatedValue>
```

**Rules:**
- Must return the same underlying business object type
- Must maintain the same cardinality (OTO or OTM)
- Conditions evaluated in order
- First matching condition is used

## Examples

### Example 1: Simple KeyField Set

```
company set
```

Creates a one-to-many relation named `companySet` based on the `company` key field.

### Example 2: Named KeyField Set

```
ACTIVE_ORDERS is a order set
    where order.status = "Active"
```

Creates a named one-to-many relation with instance selection.

### Example 3: One-to-One Relation

```
EMPLOYEE_DETAIL
    one-to-one relation to EMPLOYEE_EXTENDED
    related.employee = employee
    required
```

Defines a required one-to-one relation to extended employee data.

### Example 4: One-to-Many with Delete Cascade

```
ORDER_LINES
    one-to-many relation to ORDER_LINE
    related.order.number = order.number
    delete cascades
```

Child records are automatically deleted when parent is deleted.

### Example 5: Delete Restricted

```
DEPARTMENT_EMPLOYEES
    one-to-many relation to EMPLOYEE
    related.department = department
    delete restricted
```

Prevents department deletion if employees exist.

### Example 6: Valid When Condition

```
PRIMARY_ADDRESS
    one-to-one relation to ADDRESS
    related.entity = entity
    related.address.type = "Primary"
    valid when entity not empty
```

Relation is only valid when entity is specified.

### Example 7: Using Symbolic Key

```
PARENT_ACCOUNT
    one-to-one relation to ACCOUNT
    Field Mapping uses symbolic key
    related.account = parent.account
```

Uses the symbolic key for mapping.

### Example 8: As Of Date

```
EFFECTIVE_RATE
    one-to-one relation to RATE_HISTORY
    related.rate.code = rate.code
    related.effective.date as of transaction.date
```

Finds the rate effective as of the transaction date.

### Example 9: Instance Selection with Complex Condition

```
HIGH_VALUE_ORDERS
    one-to-many relation to ORDER
    related.customer = customer
    where (order.total > 10000) and (order.status != "Cancelled")
```

Filters to high-value, non-cancelled orders.

### Example 10: Relative Relation

```
RECENT_ORDERS
    one-to-many relation using ORDER_LINES
    where order.date >= (today - 30 days)
```

Further restricts an existing relation to recent orders.

### Example 11: Include Deleted Records

```
ALL_ADDRESSES
    one-to-many relation to ADDRESS
    related.entity = entity
    include deleted records
```

Includes soft-deleted address records.

### Example 12: Conditional Relation

```
APPROVAL_MANAGER
    if approval.level = 1
        LEVEL1_MANAGER
    if approval.level = 2
        LEVEL2_MANAGER
    else
        DEFAULT_MANAGER
```

Selects different manager relations based on approval level.

### Example 13: Dynamic Mapping

```
FLEXIBLE_LINK
    one-to-one relation to REFERENCE_DATA
    dynamic mapping is user.mapping.config
    related.reference.type = dynamic.type
    related.reference.code = dynamic.code
```

Uses runtime configuration for field mapping.

### Example 14: Part-Of Key

```
PARENT_ENTITY
    one-to-one relation to ENTITY
    Field Mapping uses ORGANIZATION part of key
```

Uses the part-of relationship from ORGANIZATION.

### Example 15: Comparison Operators

```
FUTURE_EVENTS
    one-to-many relation to EVENT
    related.entity = entity
    related.event.date > current.date
```

Finds events with dates after the current date.

### Example 16: BOD Key

```
BOD_REFERENCE
    one-to-one relation to BUSINESS_OBJECT
    Field Mapping uses bod key
```

Uses the Business Object Document identifier.

### Example 17: Update Stamp Key

```
CONCURRENT_VERSION
    one-to-one relation to VERSIONED_DATA
    Field Mapping uses update stamp key
```

Uses the update timestamp for versioning.

### Example 18: Multiple Field Mappings

```
COMPOSITE_LINK
    one-to-one relation to DETAIL_DATA
    related.company = company
    related.division = division
    related.department = department
    related.effective.date as of transaction.date
```

Maps multiple fields including a temporal component.

## Use Cases

### Master-Detail Relationships
- Order headers to order lines
- Invoice headers to invoice details
- Document headers to document attachments

### Hierarchical Structures
- Employee to manager
- Account to parent account
- Organization to parent organization

### Reference Data
- Transaction to currency
- Product to product category
- Customer to customer type

### Temporal Relationships
- Current rate from rate history
- Effective configuration from configuration history
- Active version from version history

### Extension Patterns
- Core entity to extended attributes
- Base record to supplemental data
- Standard fields to custom fields

## Best Practices

1. **Use KeyField Sets When Possible**
   - Simpler syntax
   - Automatic relation generation
   - Consistent naming convention
   - Less error-prone

2. **Choose Appropriate Delete Behavior**
   - Use `delete restricted` for referential integrity
   - Use `delete cascades` for true parent-child relationships
   - Consider business rules and data retention requirements

3. **Optimize Performance**
   - Use `disable filtering within db` sparingly
   - Enable subset select for large collections
   - Consider indexing on mapped fields
   - Use instance selection to reduce result sets

4. **Validation and Required Relations**
   - Mark critical relations as `required`
   - Use `valid when` for conditional requirements
   - Provide clear error messages
   - Test validation logic thoroughly

5. **Temporal Relations**
   - Use `as of` operator for effective-dated data
   - Ensure date fields are properly indexed
   - Consider timezone implications
   - Document temporal logic clearly

6. **Instance Selection**
   - Keep conditions simple and efficient
   - Use indexed fields in where clauses
   - Test with representative data volumes
   - Document filtering logic

7. **Conditional Relations**
   - Ensure all branches return same type
   - Maintain consistent cardinality
   - Test all condition paths
   - Provide sensible else clause

## Common Patterns

### Master-Detail Pattern
```
<DetailClass> set
    delete cascades
```

### Optional Extension Pattern
```
EXTENSION
    one-to-one relation to EXTENDED_DATA
    related.key = key
    valid when needs.extension = true
```

### Filtered Collection Pattern
```
ACTIVE_ITEMS
    one-to-many relation to ITEM
    related.parent = key
    where item.status = "Active"
```

### Temporal Lookup Pattern
```
EFFECTIVE_VALUE
    one-to-one relation to HISTORY
    related.code = code
    related.effective.date as of transaction.date
```

## Troubleshooting

### Issue: Relation returns no data

**Possible Causes:**
- Field mapping incorrect
- Instance selection too restrictive
- Related records don't exist
- Valid when condition fails

**Solutions:**
- Verify field mappings match key structure
- Test without instance selection
- Check related data exists
- Review validation conditions

### Issue: Delete restricted prevents deletion

**Possible Causes:**
- Related records exist
- Soft-deleted records not considered
- Cascading deletes not configured

**Solutions:**
- Delete or reassign related records first
- Consider using delete cascades
- Check for soft-deleted records
- Review delete dependencies

### Issue: Performance problems

**Possible Causes:**
- Large one-to-many collections
- Complex instance selection
- Missing indexes
- Link cache disabled

**Solutions:**
- Add indexes on mapped fields
- Simplify instance selection
- Enable link cache
- Use subset select optimization
- Consider pagination

### Issue: Conditional relation fails

**Possible Causes:**
- Conditions not mutually exclusive
- Type mismatch between branches
- Cardinality mismatch
- Missing else clause

**Solutions:**
- Review condition logic
- Ensure all branches return same type
- Verify cardinality consistency
- Add else clause for safety

### Issue: Temporal relation incorrect

**Possible Causes:**
- Date comparison logic wrong
- Timezone issues
- Missing effective dates
- Incorrect as of field

**Solutions:**
- Verify date field usage
- Check timezone handling
- Ensure complete date coverage
- Test with various dates

## Related Concepts

- [Business Class Definition](business-class-definition.md) - Business class structure
- [Business Class Ontology](business-class-ontology.md) - Keys and relationships
- [Field Definition](field-definition.md) - Field structure
- [Conditions](conditions.md) - Boolean expressions
- [Dates and Timestamps](dates-timestamps.md) - Temporal operations
- [References](references.md) - Related values
- [Patterns](patterns.md) - Design patterns

## Notes

- By default, relations are valid only if key fields are entered
- KeyField sets are the preferred way to define one-to-many relations
- Relative relations can only further restrict, not expand, the base relation
- Non-equality operators (>, <, >=, <=) are valid on the last key field only
- The `as of` operator is valid on date fields only
- Instance selection can reference fields from both parent and child objects
- Multiple dependent OTMs in RelatedValue require direct OTO back to target
- Conditional relations must maintain consistent type and cardinality
- Include deleted records requires business class to implement DeleteFlag
- Link cache improves performance but uses memory
