# Cube Relations

## Overview

Cube Relations establish connections between business classes and analytical cubes, enabling multidimensional data analysis. They define how data from a business class maps to cube dimensions and measures, supporting both cell-based and matrix-based retrieval patterns.

## Syntax

```
<CubeRelationName>
    (cell | matrix) relation to <BusinessClass>
    [allow dimension reordering]
    [period view is <PeriodOperator>]
    [dynamic mapping is <RelatedValue>]
    [dynamic preload measures are <RelatedValue>]
    [Dimension Mapping]
    related.<DimensionField> ( = <RelatedValue> [and blank dimension]
                             | where (<MeasureCondition>))
    …
    [Preload Measures]
    <FullFieldName> [<PeriodOperator>]
    …
```

### Supporting Definitions

```
MeasureCondition ::= <Parens><MeasureConditionNode><Parens>
                     [<Conjunction> <Parens><MeasureConditionNode><Parens>…]

MeasureConditionNode ::= <FullFieldName> [not] empty
                         // must be a valid Measure on <BusinessClass>
```

## Components

### Relation Type

**cell relation to**
- Retrieves a single cell value from the cube
- Optimized for point queries
- Returns one measure value for a specific dimension combination

**matrix relation to**
- Retrieves multiple cells as a matrix
- Optimized for range queries
- Returns multiple measure values across dimension combinations

### Target Business Class

The analytical cube business class that contains the dimensions and measures to be accessed.

### Optional Attributes

| Attribute | Description |
|-----------|-------------|
| `allow dimension reordering` | Enables dynamic query optimization by reordering dimensions for better performance |
| `period view is <PeriodOperator>` | Specifies the time period perspective for the relation |
| `dynamic mapping is <RelatedValue>` | Provides runtime dimension mapping logic |
| `dynamic preload measures are <RelatedValue>` | Specifies measures to preload dynamically at runtime |

## Dimension Mapping

Maps dimensions from the target cube to values in the current context.

### Basic Mapping

```
related.<DimensionField> = <RelatedValue>
```

Maps a cube dimension to a specific value or field reference.

### Blank Dimension Handling

```
related.<DimensionField> = <RelatedValue> and blank dimension
```

Includes the blank/null dimension value in the mapping, useful for aggregated totals.

### Conditional Mapping

```
related.<DimensionField> where (<MeasureCondition>)
```

Maps dimensions based on measure conditions, enabling dynamic filtering.

## Measure Conditions

Define boolean conditions based on measure values.

### Empty Check

```
<FullFieldName> empty
<FullFieldName> not empty
```

Tests whether a measure has a value.

### Complex Conditions

```
(<Measure1> not empty) and (<Measure2> empty)
(<Measure1> not empty) or (<Measure2> not empty)
```

Combine multiple measure checks with logical operators.

## Preload Measures

Specifies which measures should be preloaded for performance optimization.

```
<FullFieldName> [<PeriodOperator>]
```

Each measure can optionally include a period operator to specify the time perspective.

## Examples

### Example 1: Simple Cell Relation

```
BUDGET_AMOUNT
    cell relation to BUDGET_CUBE
    related.Department = department
    related.Account = account
    related.FiscalYear = fiscal.year
    budget.amount
```

Retrieves a single budget amount for a specific department, account, and fiscal year.

### Example 2: Matrix Relation with Reordering

```
SALES_MATRIX
    matrix relation to SALES_CUBE
    allow dimension reordering
    related.Product = product
    related.Region = region
    related.Month = current.month
    sales.amount
    sales.quantity
    sales.profit
```

Retrieves multiple sales measures across products and regions, allowing the system to optimize dimension ordering.

### Example 3: Period View

```
YTD_REVENUE
    cell relation to REVENUE_CUBE
    period view is year to date
    related.Division = division
    related.Account = revenue.account
    revenue.amount year to date
```

Retrieves year-to-date revenue using period operators.

### Example 4: Blank Dimension

```
TOTAL_WITH_UNASSIGNED
    cell relation to COST_CUBE
    related.CostCenter = cost.center and blank dimension
    related.FiscalPeriod = current.period
    total.cost
```

Includes costs from both assigned cost centers and unassigned (blank) entries.

### Example 5: Conditional Dimension Mapping

```
ACTIVE_PROJECTS
    matrix relation to PROJECT_CUBE
    related.Department = department
    related.Project where (project.hours not empty)
    project.hours
    project.cost
```

Maps only projects that have recorded hours.

### Example 6: Dynamic Mapping

```
FLEXIBLE_ANALYSIS
    matrix relation to ANALYSIS_CUBE
    dynamic mapping is user.dimension.selection
    dynamic preload measures are user.measure.selection
    related.Dimension1 = dynamic.value1
    related.Dimension2 = dynamic.value2
```

Uses runtime values to determine dimension mappings and measures to preload.

### Example 7: Complex Measure Conditions

```
VARIANCE_ANALYSIS
    matrix relation to BUDGET_CUBE
    related.Account = account
    related.Period where ((actual.amount not empty) and (budget.amount not empty))
    actual.amount
    budget.amount
    variance.amount
```

Maps only periods where both actual and budget amounts exist.

### Example 8: Multiple Preload Measures with Periods

```
TREND_ANALYSIS
    cell relation to SALES_CUBE
    period view is month to date
    related.Product = product
    related.Store = store
    sales.amount month to date
    sales.amount prior month
    sales.amount year to date
    sales.amount prior year
```

Preloads multiple time-based perspectives of the same measure.

## Use Cases

### Financial Reporting
- Budget vs. actual comparisons
- Variance analysis
- Period-over-period trends
- Consolidated financial statements

### Sales Analytics
- Product performance by region
- Sales trends over time
- Customer segmentation analysis
- Territory comparisons

### Operational Metrics
- Resource utilization
- Project tracking
- Inventory analysis
- Performance dashboards

### Strategic Planning
- Forecasting
- Scenario analysis
- What-if modeling
- Trend projection

## Best Practices

1. **Choose the Right Relation Type**
   - Use `cell relation` for single-value lookups
   - Use `matrix relation` for multi-dimensional analysis
   - Consider query patterns when selecting type

2. **Optimize Performance**
   - Use `allow dimension reordering` for complex queries
   - Preload only necessary measures
   - Map dimensions efficiently

3. **Handle Missing Data**
   - Use `and blank dimension` when totals include unassigned values
   - Use measure conditions to filter incomplete data
   - Consider default values for missing dimensions

4. **Period Management**
   - Specify period views consistently
   - Use period operators on preload measures
   - Align period definitions across related cubes

5. **Dynamic Behavior**
   - Use dynamic mapping for user-configurable analysis
   - Validate dynamic values at runtime
   - Provide fallback mappings

6. **Dimension Mapping**
   - Map all required dimensions explicitly
   - Use measure conditions for selective mapping
   - Document dimension relationships

## Common Patterns

### Budget Comparison Pattern
```
BUDGET_VS_ACTUAL
    cell relation to FINANCIAL_CUBE
    related.Account = account
    related.Department = department
    related.Period = current.period
    budget.amount
    actual.amount
```

### Trend Analysis Pattern
```
SALES_TREND
    matrix relation to SALES_CUBE
    allow dimension reordering
    related.Product = product
    related.Month = analysis.months
    sales.amount
    sales.quantity
```

### Conditional Aggregation Pattern
```
ACTIVE_METRICS
    matrix relation to METRICS_CUBE
    related.Metric where (metric.value not empty)
    related.Period = reporting.period
    metric.value
```

## Troubleshooting

### Issue: Cube relation returns no data

**Possible Causes:**
- Dimension mappings don't match cube data
- Measure conditions are too restrictive
- Period view doesn't align with cube periods

**Solutions:**
- Verify dimension values exist in cube
- Check measure condition logic
- Review period operator usage
- Test with simpler mappings first

### Issue: Performance degradation

**Possible Causes:**
- Too many preload measures
- Inefficient dimension ordering
- Large matrix retrievals

**Solutions:**
- Enable `allow dimension reordering`
- Reduce preloaded measures
- Use cell relations for point queries
- Add appropriate indexes to cube

### Issue: Incorrect totals with blank dimensions

**Possible Causes:**
- Missing `and blank dimension` clause
- Dimension not properly aggregated in cube

**Solutions:**
- Add `and blank dimension` to mapping
- Verify cube aggregation rules
- Check dimension hierarchy definitions

### Issue: Dynamic mapping not working

**Possible Causes:**
- Related value returns invalid dimension
- Dynamic value not available at runtime
- Type mismatch in mapping

**Solutions:**
- Validate dynamic value source
- Provide default mappings
- Check value types match dimension types
- Add error handling

## Related Concepts

- [Dimensions](dimensions.md) - Cube dimension definitions
- [Measures](measures.md) - Cube measure definitions
- [Cube Links](cube-links.md) - Linking multiple cubes
- [Measure-Based Dimensions](measure-based-dimensions.md) - Dimensions derived from measures
- [Dates and Timestamps](dates-timestamps.md) - Period operators
- [Conditions](conditions.md) - Boolean expressions for filtering

## Notes

- Cube relations are read-only; they retrieve data but don't modify cubes
- Dimension mappings must resolve to valid dimension values
- Preload measures improve performance but increase memory usage
- Matrix relations can return large result sets; use appropriate filtering
- Period operators must be compatible with cube time dimensions
- Measure conditions are evaluated after dimension mapping
