# Measures

Measures are the numeric values that are analyzed in an AnalyticCube. They represent the facts or metrics that are aggregated and analyzed across dimensions.

## Purpose

Measures are used to:
- Define the numeric values to be analyzed
- Specify aggregation behavior
- Support calculations and derived metrics
- Enable multi-dimensional analysis
- Provide the "facts" in a fact table

## Syntax

```
Measures
    <FullFieldName>
        [measure name is <Literal>]
        [calculate dynamically]
        [<DimensionField> value is <RelatedValue>]…
        [cube rule is <Message>]
        [Dimension Based Measures]
            <FieldName>…
            [<DefaultLabel>]
            Dimension Values
                <DimensionField> value is <RelatedValue>…
```

## Field Requirements

```
<FullFieldName>
```

Must be a Persistent or Derived field that is numeric.

## Measure Attributes

### Measure Name

```
[measure name is <Literal>]
```

Default is business class prefix plus field name. Default max is 50 characters, otherwise specify a name.

### Calculate Dynamically

```
[calculate dynamically]
```

Valid only when measure is a ComputeField. Indicates that the measure should be calculated on-the-fly rather than pre-calculated.

### Dimension Field Values

```
[<DimensionField> value is <RelatedValue>]…
```

- DimensionField must be a Measure Based Dimension
- When using a Measure Based Dimension, measure names can be duplicated
- Each measure must specify values for all Measure Based Dimensions

### Cube Rule

```
[cube rule is <Message>]
```

FullFieldName must be a NativeField. Defines a rule for cube calculation.

## Dimension Based Measures

```
[Dimension Based Measures]
    <FieldName>…
    [<DefaultLabel>]
    Dimension Values
        <DimensionField> value is <RelatedValue>…
```

Allows defining multiple related measures that share dimension values.

## Examples

### Simple Measures

```
SALESFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Measures
        SALESAMOUNT
            measure name is "SalesAmount"
        
        QUANTITY
            measure name is "Quantity"
        
        COST
            measure name is "Cost"
```

### Measures with Measure Based Dimensions

```
Measure Based Dimensions
    MEASURETYPE
        dimension name is "MeasureType"
        label is "Measure Type"

Measures
    ACTUALAMOUNT
        measure name is "Amount"
        MEASURETYPE value is MeasureType.Actual
    
    BUDGETAMOUNT
        measure name is "Amount"
        MEASURETYPE value is MeasureType.Budget
    
    VARIANCEAMOUNT
        measure name is "Amount"
        MEASURETYPE value is MeasureType.Variance
```

### Dynamically Calculated Measure

```
Derived Fields
    PROFITMARGIN is Decimal size 5.2
        ProfitMargin = (Revenue - Cost) / Revenue * 100

Measures
    PROFITMARGIN
        measure name is "ProfitMargin"
        calculate dynamically
```

### Measures with Cube Rules

```
Measures
    REVENUE
        measure name is "Revenue"
        cube rule is "sum(Revenue)"
    
    AVERAGEPRICE
        measure name is "AveragePrice"
        cube rule is "avg(UnitPrice)"
```

### Dimension Based Measures

```
Measure Based Dimensions
    TIMEFRAME
        dimension name is "TimeFrame"
        label is "Time Frame"

Measures
    Dimension Based Measures
        SALESAMOUNT
        QUANTITY
        COST
        
        Dimension Values
            TIMEFRAME value is TimeFrame.MonthToDate
    
    Dimension Based Measures
        SALESAMOUNT
        QUANTITY
        COST
        
        Dimension Values
            TIMEFRAME value is TimeFrame.YearToDate
```

### Multiple Measure Based Dimensions

```
Measure Based Dimensions
    MEASURETYPE
        dimension name is "MeasureType"
        label is "Measure Type"
    
    SCENARIO
        dimension name is "Scenario"
        label is "Scenario"

Measures
    ACTUALREVENUE
        measure name is "Revenue"
        MEASURETYPE value is MeasureType.Actual
        SCENARIO value is Scenario.Actual
    
    BUDGETREVENUE
        measure name is "Revenue"
        MEASURETYPE value is MeasureType.Budget
        SCENARIO value is Scenario.Budget
    
    FORECASTREVENUE
        measure name is "Revenue"
        MEASURETYPE value is MeasureType.Forecast
        SCENARIO value is Scenario.Forecast
```

### Measures with Default Labels

```
Measures
    Dimension Based Measures
        SALESAMOUNT
            default label is "Sales Amount"
        QUANTITY
            default label is "Quantity"
        
        Dimension Values
            TIMEFRAME value is TimeFrame.Current
```

## Common Measure Patterns

### Financial Measures

```
Measures
    REVENUE
        measure name is "Revenue"
    
    COST
        measure name is "Cost"
    
    GROSSPROFIT
        measure name is "GrossProfit"
        calculate dynamically
    
    NETINCOME
        measure name is "NetIncome"
        calculate dynamically
```

### Sales Measures

```
Measures
    SALESAMOUNT
        measure name is "SalesAmount"
    
    QUANTITY
        measure name is "Quantity"
    
    UNITPRICE
        measure name is "UnitPrice"
        calculate dynamically
    
    DISCOUNT
        measure name is "Discount"
```

### Inventory Measures

```
Measures
    BEGINNINGBALANCE
        measure name is "BeginningBalance"
    
    RECEIPTS
        measure name is "Receipts"
    
    ISSUES
        measure name is "Issues"
    
    ENDINGBALANCE
        measure name is "EndingBalance"
        calculate dynamically
```

### KPI Measures

```
Measures
    CUSTOMERSATISFACTION
        measure name is "CustomerSatisfaction"
    
    EMPLOYEETURNOVER
        measure name is "EmployeeTurnover"
    
    INVENTORYTURNOVER
        measure name is "InventoryTurnover"
        calculate dynamically
    
    PROFITMARGIN
        measure name is "ProfitMargin"
        calculate dynamically
```

### Variance Analysis Measures

```
Measure Based Dimensions
    VARIANCETYPE
        dimension name is "VarianceType"
        label is "Variance Type"

Measures
    ACTUALVALUE
        measure name is "Value"
        VARIANCETYPE value is VarianceType.Actual
    
    BUDGETVALUE
        measure name is "Value"
        VARIANCETYPE value is VarianceType.Budget
    
    VARIANCE
        measure name is "Value"
        VARIANCETYPE value is VarianceType.Variance
        calculate dynamically
    
    VARIANCEPERCENT
        measure name is "Value"
        VARIANCETYPE value is VarianceType.VariancePercent
        calculate dynamically
```

### Time-Based Measures

```
Measure Based Dimensions
    TIMEPERIOD
        dimension name is "TimePeriod"
        label is "Time Period"

Measures
    MONTHLYSALES
        measure name is "Sales"
        TIMEPERIOD value is TimePeriod.Monthly
    
    QUARTERLYSALES
        measure name is "Sales"
        TIMEPERIOD value is TimePeriod.Quarterly
    
    YEARLYSALES
        measure name is "Sales"
        TIMEPERIOD value is TimePeriod.Yearly
```

### Scenario Measures

```
Measure Based Dimensions
    SCENARIO
        dimension name is "Scenario"
        label is "Scenario"

Measures
    BASECASE
        measure name is "Forecast"
        SCENARIO value is Scenario.BaseCase
    
    BESTCASE
        measure name is "Forecast"
        SCENARIO value is Scenario.BestCase
    
    WORSTCASE
        measure name is "Forecast"
        SCENARIO value is Scenario.WorstCase
```

## Best Practices

### Measure Design

- Choose measures that provide meaningful business insights
- Use descriptive measure names
- Keep measure names within 50 character limit
- Document measure definitions and calculations
- Consider aggregation behavior

### Calculated Measures

- Use `calculate dynamically` for derived measures
- Document calculation formulas
- Test calculation accuracy
- Consider performance implications
- Validate calculation results

### Measure Based Dimensions

- Ensure all measures specify values for all Measure Based Dimensions
- Use consistent dimension values across related measures
- Document measure-dimension relationships
- Test measure aggregation with dimensions
- Validate dimension value assignments

### Naming

- Use clear, business-friendly measure names
- Follow consistent naming conventions
- Avoid technical jargon when possible
- Provide meaningful default labels
- Document naming standards

### Performance

- Pre-calculate measures when possible
- Use dynamic calculation selectively
- Consider cube refresh performance
- Test query performance with realistic data
- Monitor aggregation times

### Cube Rules

- Define appropriate aggregation rules
- Document cube rule logic
- Test aggregation behavior
- Consider edge cases
- Validate aggregation results

### Documentation

- Document measure definitions
- Explain calculation logic
- Document business rules
- Provide usage examples
- Keep documentation current

## Aggregation Behavior

### Default Aggregation

By default, measures are aggregated using SUM across dimensions.

### Custom Aggregation

Use cube rules to specify custom aggregation:
- `sum(FieldName)` - Sum aggregation
- `avg(FieldName)` - Average aggregation
- `min(FieldName)` - Minimum value
- `max(FieldName)` - Maximum value
- `count(FieldName)` - Count of values

### Calculated Measures

Calculated measures can derive their values from other measures:
```
PROFITMARGIN = (REVENUE - COST) / REVENUE * 100
```

## Measure Types

### Additive Measures

Can be summed across all dimensions:
```
Measures
    SALESAMOUNT
        measure name is "SalesAmount"
    
    QUANTITY
        measure name is "Quantity"
```

### Semi-Additive Measures

Can be summed across some dimensions but not others (e.g., balances):
```
Measures
    ACCOUNTBALANCE
        measure name is "AccountBalance"
        cube rule is "sum(AccountBalance) for all dimensions except Time"
```

### Non-Additive Measures

Cannot be summed (e.g., ratios, percentages):
```
Measures
    PROFITMARGIN
        measure name is "ProfitMargin"
        calculate dynamically
        cube rule is "avg(ProfitMargin)"
```

## Dimension Based Measures Pattern

### Grouping Related Measures

```
Measure Based Dimensions
    TIMEFRAME
        dimension name is "TimeFrame"

Measures
    Dimension Based Measures
        SALESAMOUNT
            default label is "Sales Amount"
        COST
            default label is "Cost"
        PROFIT
            default label is "Profit"
        
        Dimension Values
            TIMEFRAME value is TimeFrame.MonthToDate
    
    Dimension Based Measures
        SALESAMOUNT
        COST
        PROFIT
        
        Dimension Values
            TIMEFRAME value is TimeFrame.YearToDate
```

This pattern allows you to define multiple related measures that share the same dimension values, reducing repetition.

## Related Concepts

### Dimensions

Measures are analyzed across dimensions. Together they form the analytical structure of the cube.

### Measure Based Dimensions

Special dimensions whose values are defined per measure rather than from data.

### AnalyticCube Pattern

Measures are used within business classes that implement the AnalyticCube pattern.

### Derived Fields

Calculated measures often reference derived fields for their calculations.

### Cube Rules

Define how measures are aggregated across dimensions.

## Troubleshooting

### Common Issues

1. **Measure name too long**: Specify explicit measure name under 50 characters
2. **Missing dimension values**: Ensure all measures specify values for all Measure Based Dimensions
3. **Calculation errors**: Verify derived field formulas and cube rules
4. **Performance issues**: Consider pre-calculation vs dynamic calculation
5. **Aggregation errors**: Check cube rule definitions

### Debugging Tips

- Verify all measures are numeric fields
- Check measure-dimension value assignments
- Test calculation formulas
- Monitor cube refresh performance
- Review aggregation results
- Validate measure values at different dimension levels
- Test with realistic data volumes

## Measure Validation

### Checklist

- [ ] All measures are numeric fields
- [ ] Measure names are under 50 characters
- [ ] All Measure Based Dimension values are specified
- [ ] Calculated measures have correct formulas
- [ ] Cube rules are appropriate for measure type
- [ ] Default labels are meaningful
- [ ] Performance is acceptable
- [ ] Aggregation behavior is correct
- [ ] Documentation is complete

## Summary

Measures are the core numeric values in an AnalyticCube that are:
- Aggregated across dimensions
- Calculated dynamically or pre-calculated
- Associated with Measure Based Dimensions
- Subject to cube rules for aggregation
- Essential for analytical reporting and analysis

Proper measure design is critical for effective analytical cubes and business intelligence reporting.
