# Columnar Relations

## Overview

Columnar Relations provide a specialized way to access analytical data stored in columnar format. Unlike traditional cube relations that work with multidimensional cubes, columnar relations work with business classes that have derived measure fields, enabling efficient aggregation and analysis of large datasets.

## Syntax

```
<ColumnarRelationName>
    (cell | matrix) relation to <BusinessClass>
    [period view is <PeriodOperator>]
    [dynamic mapping is <RelatedValue>]
    [dynamic preload measures are <RelatedValue>]
    [Dimensions]
    related.<RelatedField> [= <RelatedValue>]
    …
    [Preload Measures]
    <FullFieldName> [<PeriodOperator>]
    …
    [Instance Selection]
    where <Condition>
```

## Components

### Relation Type

**cell relation to**
- Retrieves a single aggregated value
- Optimized for point queries
- Returns one measure value for a specific dimension combination

**matrix relation to**
- Retrieves multiple aggregated values
- Optimized for range queries
- Returns multiple measure values across dimension combinations

### Target Business Class

The business class containing derived measure fields that will be aggregated. This class typically represents transactional or detailed data that needs to be analyzed.

### Optional Attributes

| Attribute | Description |
|-----------|-------------|
| `period view is <PeriodOperator>` | Specifies the time period perspective for the relation |
| `dynamic mapping is <RelatedValue>` | Provides runtime dimension mapping logic |
| `dynamic preload measures are <RelatedValue>` | Specifies measures to preload dynamically at runtime |

## Dimensions

Maps related fields from the target business class to values in the current context.

```
related.<RelatedField> [= <RelatedValue>]
```

- `<RelatedField>` - A field in the target business class
- `<RelatedValue>` - Optional value or expression to match against
- If no value is specified, the field is used as a grouping dimension

## Preload Measures

Specifies which derived measure fields should be preloaded and aggregated.

```
<FullFieldName> [<PeriodOperator>]
```

- `<FullFieldName>` - Must be a valid derived measure field on the target business class
- `<PeriodOperator>` - Optional period operator for time-based measures

## Instance Selection

Filters which instances from the target business class are included in the aggregation.

```
where <Condition>
```

- Valid only with `matrix relation`
- Can reference fields from both the target business class (OTM object) and the current object
- Enables selective aggregation based on complex criteria

## Examples

### Example 1: Simple Cell Relation

```
TOTAL_SALES
    cell relation to SALES_TRANSACTION
    related.product = current.product
    related.region = current.region
    sales.amount
```

Aggregates total sales amount for a specific product and region.

### Example 2: Matrix Relation with Multiple Dimensions

```
SALES_BY_MONTH
    matrix relation to SALES_TRANSACTION
    related.product = current.product
    related.month
    sales.amount
    sales.quantity
    sales.cost
```

Retrieves sales metrics grouped by month for a specific product.

### Example 3: Period View

```
YTD_REVENUE
    cell relation to REVENUE_TRANSACTION
    period view is year to date
    related.department = current.department
    revenue.amount year to date
```

Calculates year-to-date revenue for a department.

### Example 4: Instance Selection with Matrix

```
ACTIVE_ORDERS
    matrix relation to ORDER_LINE
    related.customer = current.customer
    related.product
    order.amount
    order.quantity
    where order.status = "Active"
```

Aggregates order data only for active orders, grouped by product.

### Example 5: Complex Instance Selection

```
QUALIFIED_SALES
    matrix relation to SALES_TRANSACTION
    related.salesperson
    related.quarter
    sales.amount
    commission.amount
    where (sales.amount > 1000) and (customer.type = "Premium")
```

Aggregates sales data filtered by amount and customer type.

### Example 6: Dynamic Mapping

```
FLEXIBLE_ANALYSIS
    matrix relation to TRANSACTION_DATA
    dynamic mapping is user.dimension.config
    dynamic preload measures are user.measure.config
    related.dimension1
    related.dimension2
    measure.value
```

Uses runtime configuration to determine dimensions and measures.

### Example 7: Multiple Period Operators

```
TREND_COMPARISON
    cell relation to SALES_DATA
    related.product = current.product
    sales.amount month to date
    sales.amount prior month
    sales.amount year to date
    sales.amount prior year
```

Retrieves multiple time-based perspectives of the same measure.

### Example 8: Ungrouped Dimensions

```
DEPARTMENT_TOTALS
    cell relation to EXPENSE_TRANSACTION
    related.department = current.department
    related.fiscal.year = current.year
    expense.amount
    expense.count
```

Aggregates expenses for a specific department and year without additional grouping.

### Example 9: Cross-Object Filtering

```
CUSTOMER_ORDERS
    matrix relation to ORDER_HEADER
    related.order.date
    order.total
    order.count
    where (order.customer = current.customer) and (current.include.cancelled = false or order.status != "Cancelled")
```

Filters orders using both the order data and current context fields.

## Use Cases

### Financial Analysis
- Transaction aggregation
- Period-over-period comparisons
- Department/cost center rollups
- Budget tracking

### Sales Reporting
- Sales by product/region/time
- Customer purchase patterns
- Sales representative performance
- Revenue trends

### Operational Metrics
- Transaction volumes
- Processing times
- Error rates
- Resource utilization

### Inventory Management
- Stock movements
- Usage patterns
- Reorder analysis
- Turnover rates

## Best Practices

1. **Choose the Right Relation Type**
   - Use `cell relation` for single aggregated values
   - Use `matrix relation` for multi-dimensional analysis
   - Consider query patterns and performance

2. **Optimize Dimension Mapping**
   - Map specific values when possible to reduce aggregation scope
   - Use ungrouped dimensions for filtering without grouping
   - Order dimensions by cardinality (low to high)

3. **Efficient Instance Selection**
   - Filter early with `where` clauses
   - Use indexed fields in conditions
   - Avoid complex calculations in filters
   - Test performance with representative data volumes

4. **Measure Selection**
   - Preload only necessary measures
   - Use appropriate period operators
   - Ensure derived measures are properly defined
   - Consider calculation complexity

5. **Dynamic Behavior**
   - Validate dynamic mappings at runtime
   - Provide sensible defaults
   - Handle missing or invalid configurations
   - Document dynamic behavior clearly

6. **Performance Considerations**
   - Index fields used in dimensions and filters
   - Limit matrix result set sizes
   - Use cell relations when possible
   - Monitor aggregation performance

## Common Patterns

### Aggregation Pattern
```
TOTAL_BY_CATEGORY
    cell relation to TRANSACTION
    related.category = current.category
    transaction.amount
    transaction.count
```

### Time Series Pattern
```
MONTHLY_TREND
    matrix relation to TRANSACTION
    related.month
    transaction.amount
    where transaction.year = current.year
```

### Filtered Aggregation Pattern
```
QUALIFIED_TOTAL
    cell relation to TRANSACTION
    related.account = current.account
    transaction.amount
    where transaction.status = "Approved"
```

### Multi-Dimensional Analysis Pattern
```
SALES_MATRIX
    matrix relation to SALES
    related.product
    related.region
    related.quarter
    sales.amount
    sales.quantity
```

## Troubleshooting

### Issue: Columnar relation returns no data

**Possible Causes:**
- Dimension mappings don't match any records
- Instance selection filters out all records
- Target business class has no data
- Derived measures not properly defined

**Solutions:**
- Verify dimension values exist in target class
- Test without instance selection first
- Check derived measure definitions
- Validate data exists in target class

### Issue: Performance degradation

**Possible Causes:**
- Large result sets in matrix relations
- Complex instance selection conditions
- Unindexed dimension fields
- Too many preload measures

**Solutions:**
- Add indexes to dimension fields
- Simplify instance selection logic
- Use cell relations when appropriate
- Reduce number of preloaded measures
- Add filters to reduce aggregation scope

### Issue: Incorrect aggregation results

**Possible Causes:**
- Derived measure calculation errors
- Instance selection logic issues
- Period operator misalignment
- Duplicate records in source data

**Solutions:**
- Verify derived measure formulas
- Test instance selection conditions
- Check period operator usage
- Investigate data quality issues
- Review aggregation logic

### Issue: Dynamic mapping not working

**Possible Causes:**
- Dynamic value returns invalid field reference
- Runtime value not available
- Type mismatch in mapping

**Solutions:**
- Validate dynamic value source
- Provide fallback mappings
- Check field types match
- Add error handling for invalid values

### Issue: Cross-object filtering fails

**Possible Causes:**
- Field references ambiguous
- Context not available in filter
- Incorrect field qualification

**Solutions:**
- Fully qualify field references
- Verify context availability
- Test filter conditions separately
- Use explicit object references

## Differences from Cube Relations

| Aspect | Columnar Relations | Cube Relations |
|--------|-------------------|----------------|
| Target | Business class with derived measures | Analytical cube business class |
| Storage | Transactional/detail data | Pre-aggregated cube data |
| Aggregation | Computed at query time | Pre-computed in cube |
| Flexibility | More flexible filtering | Fixed cube structure |
| Performance | Depends on data volume | Generally faster for large aggregations |
| Instance Selection | Supported with `where` clause | Uses measure conditions |
| Use Case | Ad-hoc analysis, detailed queries | Standard reporting, dashboards |

## Related Concepts

- [Cube Relations](cube-relations.md) - Multidimensional cube access
- [Derived Fields](derived-fields.md) - Calculated measure definitions
- [Conditions](conditions.md) - Boolean expressions for filtering
- [Dates and Timestamps](dates-timestamps.md) - Period operators
- [Measures](measures.md) - Analytical measures
- [Dimensions](dimensions.md) - Analytical dimensions
- [References](references.md) - Related values and field references

## Notes

- Columnar relations aggregate data at query time, unlike cube relations which use pre-aggregated data
- The `where` clause in instance selection can reference both source and target object fields
- Derived measures must be properly defined on the target business class
- Matrix relations can return large result sets; use appropriate filtering
- Period operators must be compatible with time-based fields
- Performance depends on data volume and indexing strategy
- Consider using cube relations for frequently-accessed aggregations
