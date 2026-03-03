# Measure Based Dimensions

A Measure Based Dimension is not an existing field value but rather takes on a value based on each measure. Each measure must specify the value for this dimension field.

## Purpose

Measure Based Dimensions are used to:
- Create dimensions that vary by measure
- Support scenarios where different measures have different dimensional characteristics
- Enable measure-specific categorization
- Provide flexibility in analytical modeling

## Key Concept

Unlike regular dimensions that are based on actual field values in the data, Measure Based Dimensions derive their values from the measures themselves. Each measure explicitly defines what value this dimension should have for that measure.

## Syntax

```
Measure Based Dimensions
    <FieldName> [<DataDefinition>]
        [dimension name is <Literal>]
        [label is <Message>]
        [caption is (representative text | <Message>)]
        [default total node is <RelatedField>]
        [Instance Selection]
            where <Condition>
        [Attributes]
            [child.]<FullFieldName>…
            [is aggregatable]
        [Hierarchies]
            <HierarchyName>
                [base level (hidden | included)]
                [description is <Message>]
                Attributes
                    [child.]<FullFieldName>…
```

## Field Requirements

```
<FieldName> [<DataDefinition>]
```

Must be:
- A KeyField, or
- A StateField

## Dimension Attributes

### Dimension Name

```
[dimension name is <Literal>]
```

Default is business class prefix plus field name. If the default exceeds the name limit of 50 characters, you must specify a name.

### Label

```
[label is <Message>]
```

If not blank, this will be used as the cube dimension name caption.

### Caption

```
[caption is (representative text | <Message>)]
```

Default text to display in cube. Defaults to representative text.

### Default Total Node

```
[default total node is <RelatedField>]
```

If a default total node is not specified, an "All" node, which aggregates all the dimension values, is generated and used when no dimension value is specified.

## Instance Selection

```
[Instance Selection]
    where <Condition>
```

Valid when RelatedField is a KeyField or a one-to-one related field. The Condition is defined in the context of the KeyField / OTO BusinessClass.

## Attributes

```
[Attributes]
    [child.]<FullFieldName>…
    [is aggregatable]
```

- FullFieldName is a field in the KeyField's BusinessClass
- If the KeyField is a ChildHierarchy, then fields in the child set BusinessClass can also be referenced using the 'child' data link
- `is aggregatable` generates a rollup hierarchy over this attribute in the cube

## Hierarchies

```
[Hierarchies]
    <HierarchyName>
        [base level (hidden | included)]
        [description is <Message>]
        Attributes
            [child.]<FullFieldName>…
```

Generates a rollup hierarchy using the specified attributes.

### Hierarchy Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `base level (hidden \| included)` | Controls base level visibility | included |
| `description is <Message>` | Hierarchy description | HierarchyName |
| `Attributes` | Must be declared attributes | |

## Examples

### Simple Measure Based Dimension

```
SALESFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Measure Based Dimensions
        MEASURETYPE
            dimension name is "MeasureType"
            label is "Measure Type"
            caption is representative text
    
    Measures
        ACTUALAMOUNT is Decimal size 15.2
            MEASURETYPE is MeasureType.Actual
        
        BUDGETAMOUNT is Decimal size 15.2
            MEASURETYPE is MeasureType.Budget
        
        VARIANCEAMOUNT is Decimal size 15.2
            MEASURETYPE is MeasureType.Variance
```

### Measure Based Dimension with Attributes

```
Measure Based Dimensions
    MEASURECATEGORY
        dimension name is "MeasureCategory"
        label is "Measure Category"
        
        Attributes
            CategoryType
            CategoryGroup
            is aggregatable

Measures
    REVENUE is Decimal size 15.2
        MEASURECATEGORY is MeasureCategory.Revenue
    
    COST is Decimal size 15.2
        MEASURECATEGORY is MeasureCategory.Cost
    
    PROFIT is Decimal size 15.2
        MEASURECATEGORY is MeasureCategory.Profit
```

### Measure Based Dimension with Hierarchy

```
Measure Based Dimensions
    FINANCIALMETRIC
        dimension name is "FinancialMetric"
        label is "Financial Metric"
        
        Attributes
            MetricType
            MetricCategory
            MetricLevel
        
        Hierarchies
            MetricHierarchy
                base level included
                description is "Financial Metric Hierarchy"
                Attributes
                    MetricType
                    MetricCategory
                    MetricLevel

Measures
    GROSSREVENUE is Decimal size 15.2
        FINANCIALMETRIC is FinancialMetric.GrossRevenue
    
    NETREVENUE is Decimal size 15.2
        FINANCIALMETRIC is FinancialMetric.NetRevenue
    
    OPERATINGEXPENSE is Decimal size 15.2
        FINANCIALMETRIC is FinancialMetric.OperatingExpense
    
    NETINCOME is Decimal size 15.2
        FINANCIALMETRIC is FinancialMetric.NetIncome
```

### Measure Based Dimension with Instance Selection

```
Measure Based Dimensions
    KPITYPE
        dimension name is "KPIType"
        label is "KPI Type"
        
        Instance Selection
            where KPIType.Active == true
        
        Attributes
            KPICategory
            KPIFrequency

Measures
    SALESVOLUME is Decimal size 15.2
        KPITYPE is KPIType.SalesVolume
    
    CUSTOMERCOUNT is Integer
        KPITYPE is KPIType.CustomerCount
    
    AVERAGEORDERVALUE is Decimal size 15.2
        KPITYPE is KPIType.AverageOrderValue
```

### Measure Based Dimension with Default Total Node

```
Measure Based Dimensions
    METRICGROUP
        dimension name is "MetricGroup"
        label is "Metric Group"
        default total node is AllMetrics
        
        Attributes
            GroupType
            GroupCategory

Measures
    QUANTITY is Decimal size 15.2
        METRICGROUP is MetricGroup.Quantity
    
    AMOUNT is Decimal size 15.2
        METRICGROUP is MetricGroup.Amount
    
    PERCENTAGE is Decimal size 5.2
        METRICGROUP is MetricGroup.Percentage
```

### Multiple Measure Based Dimensions

```
Measure Based Dimensions
    MEASURETYPE
        dimension name is "MeasureType"
        label is "Measure Type"
    
    TIMEFRAME
        dimension name is "TimeFrame"
        label is "Time Frame"
    
    SCENARIO
        dimension name is "Scenario"
        label is "Scenario"

Measures
    ACTUALYTD is Decimal size 15.2
        MEASURETYPE is MeasureType.Actual
        TIMEFRAME is TimeFrame.YearToDate
        SCENARIO is Scenario.Actual
    
    BUDGETYTD is Decimal size 15.2
        MEASURETYPE is MeasureType.Budget
        TIMEFRAME is TimeFrame.YearToDate
        SCENARIO is Scenario.Budget
    
    ACTUALMTD is Decimal size 15.2
        MEASURETYPE is MeasureType.Actual
        TIMEFRAME is TimeFrame.MonthToDate
        SCENARIO is Scenario.Actual
```

### Measure Based Dimension with Child Hierarchy

```
Measure Based Dimensions
    PERFORMANCEMETRIC
        dimension name is "PerformanceMetric"
        label is "Performance Metric"
        
        Attributes
            child.MetricName
            child.MetricLevel
            child.MetricUnit
        
        Hierarchies
            PerformanceHierarchy
                description is "Performance Metric Hierarchy"
                Attributes
                    child.MetricLevel
                    child.MetricUnit

Measures
    EFFICIENCY is Decimal size 5.2
        PERFORMANCEMETRIC is PerformanceMetric.Efficiency
    
    PRODUCTIVITY is Decimal size 5.2
        PERFORMANCEMETRIC is PerformanceMetric.Productivity
    
    QUALITY is Decimal size 5.2
        PERFORMANCEMETRIC is PerformanceMetric.Quality
```

## Use Cases

### Financial Reporting

```
Measure Based Dimensions
    ACCOUNTTYPE
        dimension name is "AccountType"
        label is "Account Type"
        
        Attributes
            AccountCategory
            FinancialStatement

Measures
    ASSETS is Decimal size 15.2
        ACCOUNTTYPE is AccountType.Asset
    
    LIABILITIES is Decimal size 15.2
        ACCOUNTTYPE is AccountType.Liability
    
    EQUITY is Decimal size 15.2
        ACCOUNTTYPE is AccountType.Equity
    
    REVENUE is Decimal size 15.2
        ACCOUNTTYPE is AccountType.Revenue
    
    EXPENSE is Decimal size 15.2
        ACCOUNTTYPE is AccountType.Expense
```

### KPI Dashboard

```
Measure Based Dimensions
    KPICATEGORY
        dimension name is "KPICategory"
        label is "KPI Category"
        
        Attributes
            CategoryName
            CategoryPriority

Measures
    CUSTOMERSATISFACTION is Decimal size 5.2
        KPICATEGORY is KPICategory.CustomerService
    
    EMPLOYEETURNOVER is Decimal size 5.2
        KPICATEGORY is KPICategory.HumanResources
    
    INVENTORYTURNOVER is Decimal size 5.2
        KPICATEGORY is KPICategory.Operations
    
    PROFITMARGIN is Decimal size 5.2
        KPICATEGORY is KPICategory.Financial
```

### Scenario Analysis

```
Measure Based Dimensions
    SCENARIO
        dimension name is "Scenario"
        label is "Scenario"
        
        Attributes
            ScenarioType
            ScenarioStatus

Measures
    BASECASE is Decimal size 15.2
        SCENARIO is Scenario.BaseCase
    
    BESTCASE is Decimal size 15.2
        SCENARIO is Scenario.BestCase
    
    WORSTCASE is Decimal size 15.2
        SCENARIO is Scenario.WorstCase
```

### Variance Analysis

```
Measure Based Dimensions
    VARIANCETYPE
        dimension name is "VarianceType"
        label is "Variance Type"
        
        Attributes
            VarianceCategory
            IsFavorable

Measures
    ACTUALVALUE is Decimal size 15.2
        VARIANCETYPE is VarianceType.Actual
    
    BUDGETVALUE is Decimal size 15.2
        VARIANCETYPE is VarianceType.Budget
    
    VARIANCE is Decimal size 15.2
        VARIANCETYPE is VarianceType.Variance
    
    VARIANCEPERCENT is Decimal size 5.2
        VARIANCETYPE is VarianceType.VariancePercent
```

## Best Practices

### Design

- Use Measure Based Dimensions when measures have different categorical characteristics
- Keep dimension values consistent across related measures
- Document the meaning of each dimension value
- Use meaningful dimension and value names
- Consider the analytical requirements

### Naming

- Use clear, descriptive dimension names
- Keep names within 50 character limit
- Use consistent naming conventions
- Provide meaningful labels and captions
- Document naming standards

### Attributes

- Define attributes that provide useful context
- Use `is aggregatable` for attributes that should roll up
- Keep attribute lists manageable
- Test attribute-based filtering
- Document attribute meanings

### Hierarchies

- Design hierarchies that match reporting needs
- Keep hierarchy levels consistent
- Use meaningful attribute names
- Test hierarchy aggregation
- Document hierarchy relationships

### Measures

- Ensure all measures specify values for all Measure Based Dimensions
- Use consistent dimension values across related measures
- Document measure-dimension relationships
- Test measure aggregation with dimensions
- Validate dimension value assignments

### Performance

- Limit the number of Measure Based Dimensions
- Keep dimension cardinality reasonable
- Use instance selection to filter dimension members
- Test query performance
- Monitor cube refresh times

### Documentation

- Document the purpose of each Measure Based Dimension
- Explain dimension value meanings
- Document measure-dimension mappings
- Provide usage examples
- Keep documentation current

## Common Patterns

### Actual vs Budget Pattern

```
Measure Based Dimensions
    MEASURETYPE
        dimension name is "MeasureType"
        label is "Measure Type"

Measures
    ACTUAL is Decimal size 15.2
        MEASURETYPE is MeasureType.Actual
    
    BUDGET is Decimal size 15.2
        MEASURETYPE is MeasureType.Budget
    
    VARIANCE is Decimal size 15.2
        MEASURETYPE is MeasureType.Variance
```

### Time Period Pattern

```
Measure Based Dimensions
    TIMEPERIOD
        dimension name is "TimePeriod"
        label is "Time Period"

Measures
    MTD is Decimal size 15.2
        TIMEPERIOD is TimePeriod.MonthToDate
    
    QTD is Decimal size 15.2
        TIMEPERIOD is TimePeriod.QuarterToDate
    
    YTD is Decimal size 15.2
        TIMEPERIOD is TimePeriod.YearToDate
```

### Unit of Measure Pattern

```
Measure Based Dimensions
    UNITOFMEASURE
        dimension name is "UnitOfMeasure"
        label is "Unit of Measure"

Measures
    QUANTITY is Decimal size 15.2
        UNITOFMEASURE is UnitOfMeasure.Units
    
    AMOUNT is Decimal size 15.2
        UNITOFMEASURE is UnitOfMeasure.Currency
    
    PERCENTAGE is Decimal size 5.2
        UNITOFMEASURE is UnitOfMeasure.Percent
```

## Differences from Regular Dimensions

| Aspect | Regular Dimension | Measure Based Dimension |
|--------|------------------|------------------------|
| **Source** | Actual field values in data | Defined per measure |
| **Variability** | Same for all measures | Can vary by measure |
| **Definition** | Based on data | Based on measure definition |
| **Use Case** | Standard dimensional analysis | Measure-specific categorization |

## Related Concepts

### Dimensions

Regular dimensions are based on actual field values, while Measure Based Dimensions are defined per measure.

### Measures

Each measure must specify a value for every Measure Based Dimension.

### AnalyticCube Pattern

Measure Based Dimensions are used within business classes that implement the AnalyticCube pattern.

### Hierarchies

Both regular and Measure Based Dimensions can have hierarchies for aggregation and drill-down.

## Troubleshooting

### Common Issues

1. **Missing dimension value**: Ensure all measures specify values for all Measure Based Dimensions
2. **Inconsistent values**: Use consistent dimension values across related measures
3. **Dimension name too long**: Specify explicit dimension name under 50 characters
4. **Hierarchy errors**: Check attribute definitions and hierarchy structure
5. **Performance issues**: Limit number of Measure Based Dimensions

### Debugging Tips

- Verify all measures have dimension values assigned
- Check dimension value consistency
- Test hierarchy aggregation
- Monitor cube refresh performance
- Review dimension member counts
- Validate measure-dimension mappings
