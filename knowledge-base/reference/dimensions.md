# Dimensions

Dimensions define the analytical axes for AnalyticCube business classes. They determine how data can be sliced, diced, and aggregated for analysis.

## Purpose

Dimensions are used in AnalyticCube business classes to:
- Define analytical perspectives (time, geography, product, customer, etc.)
- Enable hierarchical aggregation
- Support drill-down and roll-up operations
- Provide context for measures
- Enable multi-dimensional analysis

## Syntax

```
Dimensions
    <RelatedField>
        [dimension name is <Literal>]
        [label is <Message>]
        [suppress dimension when <SimpleCondition>]
        [caption is (representative text | <Message>)]
        [default total node is <RelatedField>]
        [top node is <RelatedField>]
        [disable hierarchy aggregation]
        [is a <PeriodType> period dimension [with year of <FullFieldName>]]
        [current year is <RelatedField>]
        [current period is <RelatedField>]
        [current date is <RelatedField>]
        [period set is <RelatedField>]
        [default label is <Message>]
        [current date period is <FullFieldName>]
        [year when <Condition>]
        [quarter when <Condition>]
        [month when <Condition>]
        [week when <Condition>]
        [day when <Condition>]
        [start date is <FullFieldName>]
        [end date is <FullFieldName>]
        [parent period is <FullFieldName>]
        [next period is <FullFieldName>]
        [previous period is <FullFieldName>]
        [beginning balance is <FullFieldName>]
        [ending balance is <FullFieldName>]
        [beginning ytd balance is <FullFieldName>]
        [ending ytd balance is <FullFieldName>]
        [valid for measure <FullFieldName>]…
        [(allow write to summary level | write to base level only)]
        [Instance Selection]
            where <Condition>
        [Attributes]
            [child.]<FullFieldName>…
        [Hierarchies]
            <HierarchyName>
                [base level (hidden | included)]
                [description is <Message>]
                Attributes
                    [child.]<FullFieldName>…
```

## Dimension Requirements

```
<RelatedField>
```

Must be:
- A KeyField, or
- A StateField, or
- Designated as a 'period'

Can be only a one-to-one related field.

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

### Suppress Dimension

```
[suppress dimension when <SimpleCondition>]
```

Conditionally suppresses the dimension based on a condition.

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

### Top Node

```
[top node is <RelatedField>]
```

Top or 'start' node when dimension is parent or child hierarchy.

### Disable Hierarchy Aggregation

```
[disable hierarchy aggregation]
```

By default, all hierarchy dimensions will aggregate based on the hierarchy. This disables that behavior.

### Write Level

```
[(allow write to summary level | write to base level only)]
```

Default is 'allow write to summary level' unless changed at the AnalyticCube level.

## Period Dimensions

### Period Type

```
PeriodType ::= ( monthly
               | weekly
               | daily
               | variable)
```

### Period Dimension Declaration

```
[is a <PeriodType> period dimension [with year of <FullFieldName>]]
```

- Daily periods only roll up into months based on a Gregorian calendar
- If the RelatedField is a date, then it must be a daily period dimension and does not need a year designation
- `with year…` is required on all non date-based periods

### Current Period Indicators

| Attribute | Description |
|-----------|-------------|
| `current year is <RelatedField>` | Indicates the current year |
| `current period is <RelatedField>` | Indicates the current period |
| `current date is <RelatedField>` | Valid on a date-based period only; if no current period is defined, the current period will be determined based on the 'as of' date |

### Period Set

```
[period set is <RelatedField>]
```

Valid on a date-based period only.

## Variable Period Type Specifications

The following specifications are required for PeriodOperators to work with the 'variable' PeriodType. Condition and FullFieldName below are in the context of the dimension business class.

### Period Label

```
[default label is <Message>]
```

### Current Date Period

```
[current date period is <FullFieldName>]
```

Period key field that corresponds to the current date.

### Period Type Conditions

| Attribute | Description |
|-----------|-------------|
| `year when <Condition>` | True when period is a year |
| `quarter when <Condition>` | True when period is a quarter |
| `month when <Condition>` | True when period is a month |
| `week when <Condition>` | True when period is a week |
| `day when <Condition>` | True when period is a day |

### Period Date Fields

| Attribute | Description |
|-----------|-------------|
| `start date is <FullFieldName>` | Date field that returns period start date |
| `end date is <FullFieldName>` | Date field that returns period end date |

### Period Navigation

| Attribute | Description |
|-----------|-------------|
| `parent period is <FullFieldName>` | Period key field for parent period |
| `next period is <FullFieldName>` | Period key field for next period |
| `previous period is <FullFieldName>` | Period key field for previous period |

### Period Balances

| Attribute | Description |
|-----------|-------------|
| `beginning balance is <FullFieldName>` | Period key field for ltd beginning balance |
| `ending balance is <FullFieldName>` | Period key field for ltd ending balance |
| `beginning ytd balance is <FullFieldName>` | Period key field for ytd beginning balance |
| `ending ytd balance is <FullFieldName>` | Period key field for ytd ending balance |

### Valid for Measure

```
[valid for measure <FullFieldName>]…
```

Specifies which measures are valid for this dimension.

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
```

FullFieldName is a field in the KeyField's BusinessClass. If the KeyField is a ChildHierarchy, then fields in the child set BusinessClass can also be referenced using the 'child' data link.

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

### Simple Dimension

```
SALESFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Dimensions
        PRODUCT
            dimension name is "Product"
            label is "Product"
            caption is representative text
```

### Dimension with Hierarchy

```
Dimensions
    ACCOUNT
        dimension name is "Account"
        label is "Account"
        
        Attributes
            AccountType
            AccountCategory
            AccountSubCategory
        
        Hierarchies
            AccountHierarchy
                base level included
                description is "Account Hierarchy"
                Attributes
                    AccountType
                    AccountCategory
                    AccountSubCategory
```

### Monthly Period Dimension

```
Dimensions
    PERIOD
        dimension name is "Period"
        label is "Period"
        is a monthly period dimension with year of FiscalYear
        current year is CurrentFiscalYear
        current period is CurrentPeriod
```

### Daily Period Dimension

```
Dimensions
    TRANSACTIONDATE
        dimension name is "TransactionDate"
        label is "Transaction Date"
        is a daily period dimension
        current date is current date
```

### Variable Period Dimension

```
Dimensions
    PERIOD
        dimension name is "Period"
        label is "Period"
        is a variable period dimension with year of Year
        
        default label is "Period"
        current date period is CurrentDatePeriod
        
        year when PeriodType == PeriodType.Year
        quarter when PeriodType == PeriodType.Quarter
        month when PeriodType == PeriodType.Month
        week when PeriodType == PeriodType.Week
        day when PeriodType == PeriodType.Day
        
        start date is StartDate
        end date is EndDate
        parent period is ParentPeriod
        next period is NextPeriod
        previous period is PreviousPeriod
```

### Example: PurchaseOrder - Enterprise Dimensions

From the PurchaseOrder business class, demonstrating complex dimension definitions:

```
Dimensions
    Buyer
        caption is "<Name>"
        Attributes
            BuyerGroup
            Name
    
    TransientGLCalendarPeriod
        dimension name is Calendar
        caption is "<AlternateCaption>"
        Attributes
            YTDCrossReference
            SamePeriodLastYear
            YTDCrossRefSPLY
            PeriodType
            PeriodTypeName
            NumberOfDays
    
    Closed
        dimension name is ClosedFlag
    
    Currency
        dimension name is CurrencyCode
    
    DerivedDropShipDimension
        dimension name is Dropship
    
    IssueMethod
    
    PurchaseOrderLifeCycleState
        dimension name is LifeCycle
    
    ShipToLocation
        dimension name is Location
        Attributes
            Company
            Name
            LocationType
            LocationTypeDescription
            PostalAddress
            GlobalLocationNumber
    
    PurchaseOrder
        dimension name is PONumber
        Attributes
            Company
            PurchaseOrder
            Closed
            Issued
            Released
            Revised
            TaxCode
    
    ProcessLevel
    
    Vendor
        caption is representative text
        Attributes
            VendorGroup
            Vendor
            VendorName
            VendorStatus
            VendorClass
            ContactName
            Operator
            Project
        Hierarchies
            VendorClass
                base level included
                Attributes
                    VendorGroup
                    VendorClass
```

This example demonstrates:
- **Custom captions**: Using field values (`<Name>`) or representative text
- **Renamed dimensions**: Using `dimension name is` to provide cleaner names
- **Attribute lists**: Multiple attributes for drill-down analysis
- **Hierarchies**: VendorClass hierarchy with base level included
- **Derived field dimensions**: DerivedDropShipDimension for calculated values
- **State field dimensions**: Closed, IssueMethod, PurchaseOrderLifeCycleState
- **Complex attributes**: ShipToLocation with multiple location attributes
- **Document dimensions**: PurchaseOrder dimension with document attributes
- **Period dimensions**: TransientGLCalendarPeriod with period-specific attributes
- **Vendor hierarchy**: Multi-level hierarchy from VendorGroup to VendorClass
- **Mixed attribute types**: Combining KeyFields, state fields, and regular fields

Key patterns shown:
- Use descriptive dimension names for clarity in reports
- Include relevant attributes for drill-down capabilities
- Define hierarchies for roll-up aggregation
- Use representative text for user-friendly captions
- Leverage derived fields for calculated dimensions
- Include document-level attributes for context
        quarter when PeriodType == PeriodType.Quarter
        month when PeriodType == PeriodType.Month
        week when PeriodType == PeriodType.Week
        day when PeriodType == PeriodType.Day
        
        start date is StartDate
        end date is EndDate
        
        parent period is ParentPeriod
        next period is NextPeriod
        previous period is PreviousPeriod
        
        beginning balance is BeginningBalance
        ending balance is EndingBalance
        beginning ytd balance is BeginningYTDBalance
        ending ytd balance is EndingYTDBalance
```

### Dimension with Instance Selection

```
Dimensions
    CUSTOMER
        dimension name is "Customer"
        label is "Customer"
        
        Instance Selection
            where Customer.Active == true
        
        Attributes
            CustomerType
            CustomerRegion
            CustomerSegment
```

### Dimension with Suppression

```
Dimensions
    SALESPERSON
        dimension name is "SalesPerson"
        label is "Sales Person"
        suppress dimension when SalesPerson == blank
        
        Attributes
            SalesRegion
            SalesTeam
```

### Dimension with Default Total Node

```
Dimensions
    GEOGRAPHY
        dimension name is "Geography"
        label is "Geography"
        default total node is AllGeographies
        
        Attributes
            Country
            Region
            City
```

### Dimension with Top Node

```
Dimensions
    ORGANIZATION
        dimension name is "Organization"
        label is "Organization"
        top node is CorporateLevel
        
        Attributes
            Division
            Department
            Team
```

### Child Hierarchy Dimension

```
Dimensions
    PRODUCTCATEGORY
        dimension name is "ProductCategory"
        label is "Product Category"
        
        Attributes
            child.CategoryName
            child.CategoryLevel
            child.CategoryType
        
        Hierarchies
            CategoryHierarchy
                base level included
                description is "Product Category Hierarchy"
                Attributes
                    child.CategoryLevel
                    child.CategoryType
```

### Dimension with Write Level Control

```
Dimensions
    BUDGET
        dimension name is "Budget"
        label is "Budget"
        write to base level only
        
        Attributes
            BudgetType
            BudgetYear
```

### Dimension with Valid Measures

```
Dimensions
    SCENARIO
        dimension name is "Scenario"
        label is "Scenario"
        valid for measure ActualAmount
        valid for measure BudgetAmount
        valid for measure VarianceAmount
```

### Multiple Hierarchies

```
Dimensions
    PRODUCT
        dimension name is "Product"
        label is "Product"
        
        Attributes
            ProductCategory
            ProductSubCategory
            ProductBrand
            ProductLine
        
        Hierarchies
            CategoryHierarchy
                description is "Product Category Hierarchy"
                Attributes
                    ProductCategory
                    ProductSubCategory
            
            BrandHierarchy
                description is "Product Brand Hierarchy"
                Attributes
                    ProductBrand
                    ProductLine
```

## Best Practices

### Dimension Design

- Choose dimensions that provide meaningful analytical perspectives
- Keep dimension hierarchies logical and intuitive
- Limit the number of dimensions to avoid complexity
- Use descriptive dimension names and labels
- Document dimension purposes and hierarchies

### Period Dimensions

- Use appropriate period types (monthly, weekly, daily, variable)
- Define current period indicators clearly
- Implement period navigation for time-based analysis
- Test period rollup behavior
- Document period hierarchy structure

### Hierarchies

- Design hierarchies that match business reporting needs
- Keep hierarchy levels consistent
- Use meaningful attribute names
- Test hierarchy aggregation
- Document hierarchy relationships

### Attributes

- Include attributes that provide useful context
- Keep attribute lists manageable
- Use consistent naming conventions
- Document attribute meanings
- Test attribute-based filtering

### Instance Selection

- Use instance selection to filter dimension members
- Keep conditions simple and efficient
- Test filter performance
- Document selection criteria
- Consider impact on cube size

### Performance

- Limit dimension cardinality when possible
- Use instance selection to reduce dimension size
- Consider indexing for large dimensions
- Test query performance with realistic data
- Monitor cube refresh times

### Write Levels

- Use "write to base level only" for detailed data entry
- Use "allow write to summary level" for planning scenarios
- Document write level decisions
- Test write behavior at different levels
- Consider data integrity implications

### Naming

- Use clear, business-friendly dimension names
- Keep names within 50 character limit
- Use consistent naming conventions
- Provide meaningful labels and captions
- Document naming standards

## Common Patterns

### Time Dimension Pattern

```
Dimensions
    FISCALPERIOD
        dimension name is "FiscalPeriod"
        label is "Fiscal Period"
        is a monthly period dimension with year of FiscalYear
        current year is CurrentFiscalYear
        current period is CurrentFiscalPeriod
        
        Attributes
            Year
            Quarter
            Month
        
        Hierarchies
            FiscalHierarchy
                description is "Fiscal Year Hierarchy"
                Attributes
                    Year
                    Quarter
                    Month
```

### Geography Dimension Pattern

```
Dimensions
    GEOGRAPHY
        dimension name is "Geography"
        label is "Geography"
        
        Attributes
            Country
            Region
            State
            City
        
        Hierarchies
            GeographyHierarchy
                description is "Geographic Hierarchy"
                Attributes
                    Country
                    Region
                    State
                    City
```

### Product Dimension Pattern

```
Dimensions
    PRODUCT
        dimension name is "Product"
        label is "Product"
        
        Instance Selection
            where Product.Active == true
        
        Attributes
            ProductFamily
            ProductCategory
            ProductSubCategory
            ProductBrand
        
        Hierarchies
            ProductHierarchy
                description is "Product Hierarchy"
                Attributes
                    ProductFamily
                    ProductCategory
                    ProductSubCategory
```

### Organization Dimension Pattern

```
Dimensions
    ORGANIZATION
        dimension name is "Organization"
        label is "Organization"
        top node is Corporate
        
        Attributes
            Division
            Department
            CostCenter
        
        Hierarchies
            OrgHierarchy
                description is "Organization Hierarchy"
                Attributes
                    Division
                    Department
                    CostCenter
```

## Related Concepts

### Measures

Dimensions provide context for measures. Together they form the analytical structure of the cube.

### AnalyticCube Pattern

Dimensions are used within business classes that implement the AnalyticCube pattern.

### Hierarchies

Hierarchies define parent-child relationships within dimensions for aggregation and drill-down.

### Attributes

Attributes provide additional properties of dimension members used in hierarchies and filtering.

## Troubleshooting

### Common Issues

1. **Dimension name too long**: Specify explicit dimension name under 50 characters
2. **Hierarchy aggregation errors**: Check attribute definitions and hierarchy structure
3. **Period rollup issues**: Verify period type and navigation fields
4. **Performance problems**: Use instance selection to reduce dimension size
5. **Write level conflicts**: Ensure write level settings are appropriate

### Debugging Tips

- Test dimensions with small data sets first
- Verify hierarchy relationships
- Check period navigation logic
- Monitor cube refresh performance
- Review dimension member counts
- Test drill-down and roll-up operations
