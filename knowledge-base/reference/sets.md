# Sets

## Overview

Sets define indexes and sort orders for business class instances. They control how data is organized, retrieved, and displayed, and determine uniqueness constraints. Sets are essential for data access patterns, query optimization, and maintaining data integrity.

## Syntax

```
<SetName>
    [<SqlName>]
    [<ClassicName>]
    [primary]
    [override primary]
    [( no duplicates | duplicates )]
    [bypass no duplicates validation]
    [( indexed | not indexed )]
    [primary when importing]
    [filter instance selection]
    Sort Order
    <FullFieldName> [descending]
    …
    [Instance Selection]
    where ( <Parens><RelatedCondition><Parens>
          | <Condition>)
```

## Components

### Set Name

A unique identifier for the set definition following LPL naming conventions (uppercase alphanumeric).

### Naming Attributes

| Attribute | Description |
|-----------|-------------|
| `<SqlName>` | SQL index name override |
| `<ClassicName>` | Classic system name override |

### Primary Index

```
primary
```

Designates this set as the primary index for the business class.

**Rules:**
- Only one index can be primary
- Primary index cannot have duplicates
- Required if no Ontology section defines the primary key
- Determines default sort order and retrieval

### Override Primary

```
override primary
```

Overrides the set automatically generated from the Ontology definition, allowing custom primary key definition.

### Duplicate Control

#### No Duplicates (Default)
```
no duplicates
```

Enforces uniqueness constraint on the sort order fields. No two instances can have the same combination of values.

#### Allow Duplicates
```
duplicates
```

Allows multiple instances with the same sort order field values.

#### Bypass Validation
```
bypass no duplicates validation
```

Skips uniqueness validation for performance or special cases. Use with caution.

### Physical Indexing

#### Indexed
```
indexed
```

Forces creation of a physical SQL index for faster retrieval.

**Benefits:**
- Faster queries
- Improved sort performance
- Better join performance

**Limitations:**
- Cannot be used if set includes related fields
- Increases storage requirements
- Slows down insert/update operations

#### Not Indexed
```
not indexed
```

Prevents physical SQL index creation; index remains virtual.

**Use Cases:**
- Sets with related fields
- Rarely-used sort orders
- Temporary or diagnostic sets

#### Dynamic (Default)
If neither `indexed` nor `not indexed` is specified, the build process dynamically creates physical indexes based on implementation parameters and usage patterns.

### Import Behavior

```
primary when importing
```

Uses this index as the primary key when importing data, even if it's not the primary index for normal operations.

**Use Cases:**
- Import data using alternate key
- Match on business keys during import
- Handle legacy data imports

### Instance Selection Filtering

```
filter instance selection
```

Filters instances at query time rather than building a subset index.

**Benefits:**
- Reduces index storage
- Simplifies maintenance
- Better for dynamic conditions

**Trade-offs:**
- May be slower than subset index
- Filtering happens at runtime

## Sort Order

Defines the fields used for sorting and their order.

```
<FullFieldName> [descending]
```

**Rules:**
- Multiple fields can be specified
- Default sort is ascending
- Use `descending` keyword for reverse order
- Order matters for multi-field sorts
- Fields can be simple, group, or related fields

## Instance Selection

Filters which instances are included in the set.

```
where ( <Parens><RelatedCondition><Parens>
      | <Condition>)
```

**Rules:**
- For non-filtered selections, condition must use only primary fields
- Filtered selections can use any fields
- Conditions evaluated at query time (filtered) or index build time (subset)
- Can reference related fields in conditions

## Examples

### Example 1: Simple Primary Index

```
BY_EMPLOYEE_ID
    primary
    no duplicates
    indexed
    employee.id
```

Primary index on employee ID with physical index.

### Example 2: Alternate Key with Duplicates

```
BY_LAST_NAME
    duplicates
    indexed
    last.name
    first.name
```

Allows duplicate last names, sorted by last name then first name.

### Example 3: Descending Sort

```
BY_HIRE_DATE_DESC
    duplicates
    indexed
    hire.date descending
    employee.id
```

Sorts by hire date in descending order (newest first).

### Example 4: Composite Key

```
BY_DEPT_AND_EMPLOYEE
    no duplicates
    indexed
    department
    employee.number
```

Unique combination of department and employee number.

### Example 5: Instance Selection

```
ACTIVE_EMPLOYEES
    duplicates
    indexed
    last.name
    first.name
    where status = "Active"
```

Subset index containing only active employees.

### Example 6: Filtered Instance Selection

```
HIGH_VALUE_CUSTOMERS
    duplicates
    filter instance selection
    customer.name
    where total.purchases > 100000
```

Filters at query time rather than building subset index.

### Example 7: Related Field Condition

```
EMPLOYEES_WITH_MANAGER
    duplicates
    not indexed
    last.name
    where (manager not empty)
```

Cannot be physically indexed due to related field in condition.

### Example 8: Primary When Importing

```
BY_LEGACY_ID
    no duplicates
    indexed
    primary when importing
    legacy.system.id
```

Uses legacy ID as key during data import.

### Example 9: Override Primary

```
BY_CUSTOM_KEY
    override primary
    no duplicates
    indexed
    custom.key.field1
    custom.key.field2
```

Replaces ontology-generated primary key with custom definition.

### Example 10: Complex Sort Order

```
BY_REGION_DEPT_NAME
    duplicates
    indexed
    region
    department
    last.name
    first.name
    employee.id
```

Multi-level sort for organizational hierarchy.

### Example 11: Bypass Validation

```
TEMP_IMPORT_INDEX
    no duplicates
    bypass no duplicates validation
    not indexed
    import.batch.id
    import.sequence
```

Skips validation for high-volume import operations.

### Example 12: Date Range Selection

```
RECENT_ORDERS
    duplicates
    indexed
    order.date descending
    order.number
    where order.date >= (today - 90 days)
```

Subset of orders from last 90 days.

### Example 13: Multi-Condition Selection

```
QUALIFIED_TRANSACTIONS
    duplicates
    filter instance selection
    transaction.date descending
    where (status = "Approved") and (amount > 1000)
```

Filtered set with multiple conditions.

### Example 14: Related Field Sort

```
BY_MANAGER_NAME
    duplicates
    not indexed
    manager.last.name
    manager.first.name
    employee.id
```

Sorts by related manager's name; cannot be physically indexed.

## Use Cases

### Primary Keys
- Unique record identification
- Default retrieval order
- Foreign key references
- Data integrity

### Alternate Keys
- Business key lookups
- Natural key access
- Import matching
- User-friendly identification

### Sort Orders
- List displays
- Report ordering
- User interface presentation
- Search result ranking

### Subset Indexes
- Status-based filtering
- Date range queries
- Category-based access
- Performance optimization

### Import Keys
- Data migration
- Legacy system integration
- Bulk data loading
- External system synchronization

## Best Practices

1. **Primary Index Design**
   - Choose stable, immutable fields
   - Keep primary key simple
   - Ensure uniqueness
   - Consider surrogate keys for complex cases

2. **Physical Indexing Strategy**
   - Use `indexed` for frequently-queried sets
   - Use `not indexed` for sets with related fields
   - Let system decide for most cases
   - Monitor query performance

3. **Duplicate Control**
   - Use `no duplicates` for unique constraints
   - Use `duplicates` for sort-only indexes
   - Document uniqueness requirements
   - Test edge cases

4. **Instance Selection**
   - Use subset indexes for stable, frequently-used filters
   - Use filtered selection for dynamic conditions
   - Keep conditions simple and efficient
   - Consider maintenance overhead

5. **Sort Order Design**
   - Order fields by selectivity (most selective first)
   - Include tie-breaker fields for consistent ordering
   - Consider user expectations
   - Test with realistic data

6. **Performance Optimization**
   - Index frequently-used access paths
   - Avoid over-indexing
   - Monitor index usage
   - Consider query patterns

7. **Import Considerations**
   - Define appropriate import keys
   - Handle duplicate detection
   - Consider performance during bulk loads
   - Test import scenarios

## Common Patterns

### Primary Key Pattern
```
BY_ID
    primary
    no duplicates
    indexed
    id
```

### Natural Key Pattern
```
BY_BUSINESS_KEY
    no duplicates
    indexed
    primary when importing
    company
    division
    account
```

### Display Order Pattern
```
BY_NAME
    duplicates
    indexed
    last.name
    first.name
    id
```

### Filtered Subset Pattern
```
ACTIVE_RECORDS
    duplicates
    indexed
    name
    where status = "Active"
```

### Temporal Pattern
```
BY_DATE_DESC
    duplicates
    indexed
    transaction.date descending
    transaction.time descending
    id
```

## Troubleshooting

### Issue: Duplicate key error

**Possible Causes:**
- Set marked as `no duplicates`
- Data violates uniqueness constraint
- Missing tie-breaker fields
- Concurrent inserts

**Solutions:**
- Change to `duplicates` if appropriate
- Add unique tie-breaker field (like ID)
- Clean up duplicate data
- Review business rules

### Issue: Slow query performance

**Possible Causes:**
- Set not physically indexed
- Missing appropriate set for query
- Large instance selection
- Related fields in sort order

**Solutions:**
- Add `indexed` keyword
- Create new set matching query pattern
- Use filtered selection instead of subset
- Optimize instance selection condition

### Issue: Index not created

**Possible Causes:**
- Set includes related fields
- Marked as `not indexed`
- Build process skipped index
- Database constraints

**Solutions:**
- Remove related fields from sort order
- Change to `indexed` if needed
- Check build logs
- Verify database permissions

### Issue: Import matching fails

**Possible Causes:**
- Wrong set used for import
- Missing `primary when importing`
- Key fields don't match import data
- Data transformation issues

**Solutions:**
- Add `primary when importing` to correct set
- Verify import key fields
- Check data mapping
- Test with sample data

### Issue: Instance selection not filtering

**Possible Causes:**
- Condition syntax error
- Related field not available
- Condition always true/false
- Using filtered vs. subset incorrectly

**Solutions:**
- Verify condition syntax
- Check field references
- Test condition logic
- Review filtered vs. subset usage

### Issue: Bypass validation causing issues

**Possible Causes:**
- Duplicate data inserted
- Data integrity compromised
- Unexpected behavior in queries

**Solutions:**
- Remove `bypass no duplicates validation`
- Clean up duplicate data
- Add application-level validation
- Review business requirements

## Set Types Comparison

| Type | Physical Index | Duplicates | Use Case |
|------|---------------|------------|----------|
| Primary | Yes | No | Unique identification |
| Alternate Key | Yes | No | Business key lookup |
| Sort Order | Optional | Yes | Display ordering |
| Subset | Yes | Yes | Filtered collections |
| Filtered | No | Yes | Dynamic filtering |
| Import Key | Yes | No | Data import matching |

## Related Concepts

- [Business Class Definition](business-class-definition.md) - Business class structure
- [Business Class Ontology](business-class-ontology.md) - Key definitions
- [Field Definition](field-definition.md) - Field structure
- [Conditions](conditions.md) - Instance selection conditions
- [Relations](relations.md) - Related field references
- [Datasource Mapping](datasource-mapping.md) - External data integration

## Notes

- NoKeyChange is no longer supported; all indexes can have keys changed
- Only one index can be primary (unless no Ontology section exists)
- Primary index cannot have duplicates
- Indexes with related fields cannot be physically indexed by the database
- If neither `indexed` nor `not indexed` is specified, the build process decides dynamically
- For non-filtered instance selections, conditions must use only primary fields
- Filtered instance selections can use any fields but filter at query time
- `bypass no duplicates validation` should be used with caution
- Physical indexes improve query performance but slow down insert/update operations
- Consider storage and maintenance costs when creating many indexes
- Test index effectiveness with realistic data volumes and query patterns
