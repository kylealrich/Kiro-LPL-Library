# Cube Links

Cube Links allow one AnalyticCube to link to another BusinessClass's cube, automatically extending the first cube's measures to include all the measures of the linked cube.

## Purpose

Cube Links are used to:
- Combine measures from multiple cubes
- Create unified analytical views
- Share dimensions across cubes
- Enable cross-cube analysis
- Reduce data duplication

## Syntax

```
Cube Links
    <CubeLinkName>
        link to <BusinessClass>
        [Dimension Mapping]
            related.<DimensionField> = (<DimensionField> | top node)…
```

## Requirements

```
link to <BusinessClass>
```

BusinessClass must have a cube defined (must implement AnalyticCube pattern).

## Dimension Mapping

```
[Dimension Mapping]
    related.<DimensionField> = (<DimensionField> | top node)…
```

Maps dimensions from the linked cube to dimensions in the current cube:
- `related.<DimensionField>` - Dimension in the linked cube
- `<DimensionField>` - Corresponding dimension in the current cube
- `top node` - Maps to the top node of the dimension hierarchy

## How Cube Links Work

When you create a Cube Link:
1. The current cube gains access to all measures from the linked cube
2. Dimensions are mapped between the cubes
3. Measures from the linked cube can be analyzed using the current cube's dimensions
4. Data is combined based on the dimension mappings

## Examples

### Simple Cube Link

```
SALESFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        PRODUCT
        CUSTOMER
        PERIOD
    
    Measures
        SALESAMOUNT
        QUANTITY
    
    Cube Links
        BudgetLink
            link to BUDGETFACT
            
            Dimension Mapping
                related.PRODUCT = PRODUCT
                related.CUSTOMER = CUSTOMER
                related.PERIOD = PERIOD
```

### Cube Link with Top Node Mapping

```
DETAILEDSALES is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        PRODUCT
        CUSTOMER
        SALESPERSON
        PERIOD
    
    Measures
        SALESAMOUNT
        COST
    
    Cube Links
        SummaryLink
            link to SUMMARYSALES
            
            Dimension Mapping
                related.PRODUCT = PRODUCT
                related.CUSTOMER = CUSTOMER
                related.PERIOD = PERIOD
                related.SALESPERSON = top node
```

### Multiple Cube Links

```
CONSOLIDATEDFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        ACCOUNT
        DEPARTMENT
        PERIOD
    
    Measures
        ACTUALAMOUNT
    
    Cube Links
        BudgetLink
            link to BUDGETFACT
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.DEPARTMENT = DEPARTMENT
                related.PERIOD = PERIOD
        
        ForecastLink
            link to FORECASTFACT
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.DEPARTMENT = DEPARTMENT
                related.PERIOD = PERIOD
```

### Cube Link with Partial Dimension Mapping

```
REGIONALSALES is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        PRODUCT
        REGION
        PERIOD
    
    Measures
        SALESAMOUNT
    
    Cube Links
        GlobalSalesLink
            link to GLOBALSALES
            
            Dimension Mapping
                related.PRODUCT = PRODUCT
                related.GEOGRAPHY = REGION
                related.PERIOD = PERIOD
```

### Cube Link for Variance Analysis

```
ACTUALFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        ACCOUNT
        COSTCENTER
        PERIOD
    
    Measures
        ACTUALAMOUNT
    
    Cube Links
        BudgetLink
            link to BUDGETFACT
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.COSTCENTER = COSTCENTER
                related.PERIOD = PERIOD
        
        PriorYearLink
            link to PRIORYEARFACT
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.COSTCENTER = COSTCENTER
                related.PERIOD = PERIOD
```

### Cube Link with Hierarchical Mapping

```
DETAILEDBUDGET is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        ACCOUNT
        DEPARTMENT
        PROJECT
        PERIOD
    
    Measures
        BUDGETAMOUNT
    
    Cube Links
        SummaryBudgetLink
            link to SUMMARYBUDGET
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.DEPARTMENT = DEPARTMENT
                related.PROJECT = top node
                related.PERIOD = PERIOD
```

## Use Cases

### Actual vs Budget Analysis

```
// Actual Cube
ACTUALFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        ACCOUNT
        DEPARTMENT
        PERIOD
    
    Measures
        ACTUALAMOUNT
    
    Cube Links
        BudgetLink
            link to BUDGETFACT
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.DEPARTMENT = DEPARTMENT
                related.PERIOD = PERIOD

// Budget Cube
BUDGETFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        ACCOUNT
        DEPARTMENT
        PERIOD
    
    Measures
        BUDGETAMOUNT
```

### Multi-Source Consolidation

```
CONSOLIDATEDFINANCIALS is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        ACCOUNT
        ENTITY
        PERIOD
    
    Measures
        CONSOLIDATEDAMOUNT
    
    Cube Links
        SubsidiaryALink
            link to SUBSIDIARYAFACT
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.ENTITY = ENTITY
                related.PERIOD = PERIOD
        
        SubsidiaryBLink
            link to SUBSIDIARYBFACT
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.ENTITY = ENTITY
                related.PERIOD = PERIOD
```

### Historical Comparison

```
CURRENTSALES is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        PRODUCT
        CUSTOMER
        PERIOD
    
    Measures
        SALESAMOUNT
    
    Cube Links
        PriorYearLink
            link to PRIORYEARSALES
            
            Dimension Mapping
                related.PRODUCT = PRODUCT
                related.CUSTOMER = CUSTOMER
                related.PERIOD = PERIOD
```

### Scenario Analysis

```
BASESCENARIO is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        ACCOUNT
        DEPARTMENT
        PERIOD
    
    Measures
        BASEAMOUNT
    
    Cube Links
        BestCaseLink
            link to BESTCASESCENARIO
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.DEPARTMENT = DEPARTMENT
                related.PERIOD = PERIOD
        
        WorstCaseLink
            link to WORSTCASESCENARIO
            
            Dimension Mapping
                related.ACCOUNT = ACCOUNT
                related.DEPARTMENT = DEPARTMENT
                related.PERIOD = PERIOD
```

## Best Practices

### Design

- Link cubes that share common dimensions
- Ensure dimension mappings are logical and meaningful
- Document the purpose of each cube link
- Consider the analytical requirements
- Plan for data consistency across linked cubes

### Dimension Mapping

- Map all relevant dimensions
- Use `top node` for dimensions that don't exist in the current cube
- Ensure dimension granularity is compatible
- Test dimension mapping behavior
- Document mapping decisions

### Performance

- Consider the performance impact of cube links
- Limit the number of cube links when possible
- Test query performance with linked cubes
- Monitor cube refresh times
- Optimize dimension mappings

### Data Consistency

- Ensure linked cubes use consistent dimension values
- Validate data across linked cubes
- Handle missing dimension values appropriately
- Test edge cases
- Document data consistency requirements

### Naming

- Use descriptive cube link names
- Follow consistent naming conventions
- Document the purpose of each link
- Use meaningful names that indicate the linked cube
- Keep names clear and concise

### Documentation

- Document each cube link's purpose
- Explain dimension mappings
- Document data sources
- Provide usage examples
- Keep documentation current

## Common Patterns

### Actual-Budget-Forecast Pattern

```
Cube Links
    BudgetLink
        link to BUDGETFACT
        
        Dimension Mapping
            related.ACCOUNT = ACCOUNT
            related.DEPARTMENT = DEPARTMENT
            related.PERIOD = PERIOD
    
    ForecastLink
        link to FORECASTFACT
        
        Dimension Mapping
            related.ACCOUNT = ACCOUNT
            related.DEPARTMENT = DEPARTMENT
            related.PERIOD = PERIOD
```

### Year-over-Year Pattern

```
Cube Links
    PriorYearLink
        link to PRIORYEARFACT
        
        Dimension Mapping
            related.PRODUCT = PRODUCT
            related.CUSTOMER = CUSTOMER
            related.PERIOD = PERIOD
    
    TwoYearsAgoLink
        link to TWOYEARSAGOFACT
        
        Dimension Mapping
            related.PRODUCT = PRODUCT
            related.CUSTOMER = CUSTOMER
            related.PERIOD = PERIOD
```

### Detail-Summary Pattern

```
Cube Links
    SummaryLink
        link to SUMMARYFACT
        
        Dimension Mapping
            related.PRODUCT = PRODUCT
            related.CUSTOMER = top node
            related.REGION = REGION
            related.PERIOD = PERIOD
```

### Multi-Entity Consolidation Pattern

```
Cube Links
    Entity1Link
        link to ENTITY1FACT
        
        Dimension Mapping
            related.ACCOUNT = ACCOUNT
            related.ENTITY = ENTITY
            related.PERIOD = PERIOD
    
    Entity2Link
        link to ENTITY2FACT
        
        Dimension Mapping
            related.ACCOUNT = ACCOUNT
            related.ENTITY = ENTITY
            related.PERIOD = PERIOD
```

## Dimension Mapping Strategies

### One-to-One Mapping

Direct mapping of dimensions with the same granularity:
```
Dimension Mapping
    related.PRODUCT = PRODUCT
    related.CUSTOMER = CUSTOMER
```

### Hierarchical Mapping

Mapping to different levels of a hierarchy:
```
Dimension Mapping
    related.PRODUCTDETAIL = PRODUCT
    related.CUSTOMERDETAIL = CUSTOMER
```

### Top Node Mapping

Mapping dimensions that don't exist in the current cube:
```
Dimension Mapping
    related.SALESPERSON = top node
    related.TERRITORY = top node
```

### Mixed Mapping

Combination of different mapping strategies:
```
Dimension Mapping
    related.PRODUCT = PRODUCT
    related.CUSTOMER = top node
    related.PERIOD = PERIOD
```

## Benefits of Cube Links

1. **Unified Analysis**: Analyze measures from multiple cubes together
2. **Reduced Duplication**: Avoid duplicating dimension definitions
3. **Flexibility**: Combine data from different sources
4. **Maintainability**: Changes to linked cubes are automatically reflected
5. **Performance**: Can be more efficient than joining data at query time

## Limitations

1. **Dimension Compatibility**: Dimensions must be compatible for meaningful analysis
2. **Performance Impact**: Multiple cube links can impact query performance
3. **Complexity**: Can make cube structure more complex
4. **Data Consistency**: Requires consistent data across linked cubes
5. **Maintenance**: Changes to linked cubes may require updates to mappings

## Related Concepts

### AnalyticCube Pattern

Cube Links are used within business classes that implement the AnalyticCube pattern.

### Dimensions

Dimension mappings connect dimensions between linked cubes.

### Measures

Measures from linked cubes become available in the current cube.

### Cube Relations

Similar to Cube Links but define different types of relationships between cubes.

## Troubleshooting

### Common Issues

1. **Dimension mapping errors**: Ensure all required dimensions are mapped
2. **Performance issues**: Limit the number of cube links
3. **Data inconsistency**: Validate data across linked cubes
4. **Missing measures**: Verify linked cube has measures defined
5. **Incompatible dimensions**: Ensure dimension granularity is compatible

### Debugging Tips

- Verify linked cube exists and has AnalyticCube pattern
- Check all dimension mappings are valid
- Test with small data sets first
- Monitor query performance
- Validate dimension value compatibility
- Review cube refresh logs
- Test measure availability from linked cubes

## Validation Checklist

- [ ] Linked cube implements AnalyticCube pattern
- [ ] All relevant dimensions are mapped
- [ ] Dimension mappings are logical
- [ ] Top node mappings are appropriate
- [ ] Performance is acceptable
- [ ] Data consistency is validated
- [ ] Documentation is complete
- [ ] Testing is thorough

## Summary

Cube Links provide a powerful mechanism for:
- Combining measures from multiple cubes
- Creating unified analytical views
- Sharing dimensions across cubes
- Enabling cross-cube analysis
- Building flexible analytical solutions

Proper use of Cube Links can significantly enhance the analytical capabilities of AnalyticCube business classes while maintaining data consistency and performance.
