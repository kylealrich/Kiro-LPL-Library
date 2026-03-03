# Context Fields

These are 'non-ontological' context variables.

## Syntax

### In Field Definitions

```
Context Fields
    <ContextField>
    <ContextField>
    ...
```

### In Business Class Definitions

```
Context Fields
    <ContextField>…
    [<DefaultLabel>]
    [is (condition | related (link | value)) for <RelatedValue>]
    [(dimensions [with attributes] | measures) only]
```

**Note:** RelatedValue must be a BusinessClass name when using analytical attributes.

## Context Field Definition

```
ContextField ::= <FieldName> [<DataDefinition>]
                 [[exact] version is (<RelatedValue> | latest)]
                 [<ContextRule>…]
```

### Context Field Attributes

| Attribute | Description |
|-----------|-------------|
| `<FieldName>` | Name of the context field |
| `[<DataDefinition>]` | Optional data type definition |
| `version is <RelatedValue>` | Specific version to use |
| `version is latest` | Use the latest version |
| `exact version is <RelatedValue>` | Use exact version specified |
| `<ContextRule>` | Rules that govern the context field |

## Context Rules

```
ContextRule ::= <RelatedField> <AssignmentOp> <RelatedValue>
              | default to <RelatedValue>…
              | constraint <Condition><Message>
              | required[<Message>]
              | if <Parens><FieldName> <ContextOperator><Parens>
                    <ContextRule>
                [else
                    <ContextRule>]
```

### Assignment Rule

```
<RelatedField> <AssignmentOp> <RelatedValue>
```

Assigns a value to the context field.

### Default Rule

```
default to <RelatedValue>…
```

Multiple fields can be put on a default. If the first one is blank, the second one is used, and so on. Only the last one can be a literal value.

**Example:**
```
default to PrimaryAddress.City, SecondaryAddress.City, "Unknown"
```

### Constraint Rule

```
constraint <Condition><Message>
```

Defines a constraint that must be satisfied, with an associated error message.

### Required Rule

```
required[<Message>]
```

Marks the field as required. Defaults to 'Field is required' if no message is provided.

### Conditional Rule

```
if <Parens><FieldName> <ContextOperator><Parens>
    <ContextRule>
[else
    <ContextRule>]
```

Applies rules conditionally based on context operators.

## Context Operators

```
ContextOperator ::= ( in context
                    | entered)
```

| Operator | Description |
|----------|-------------|
| `in context` | Checks if the field is in the current context |
| `entered` | Checks if the field has been entered/provided |

## Examples

### Simple Context Field

```
Context Fields
    FISCALYEAR is Integer
        FiscalYear = CurrentFiscalYear
```

### Context Field with Default

```
Context Fields
    DEFAULTCURRENCY is Alpha size 3
        default to Company.Currency, "USD"
```

### Context Field with Version

```
Context Fields
    CONTRACTVERSION is Integer
        version is latest
        ContractVersion = Contract.LatestVersion
```

### Context Field with Constraint

```
Context Fields
    CREDITLIMIT is Decimal size 15.2
        CreditLimit = Customer.CreditLimit
        constraint CreditLimit > 0
            "Credit limit must be greater than zero"
```

### Required Context Field

```
Context Fields
    DEPARTMENT is Alpha size 10
        required "Department is required for this transaction"
```

### Conditional Context Field

```
Context Fields
    APPROVER is Alpha size 10
        if (Amount in context)
            if Amount > 10000
                Approver = Manager.Supervisor
            else
                Approver = Manager
```

### Context Field with Multiple Defaults

```
Context Fields
    SHIPPINGADDRESS is Address group
        default to Customer.ShippingAddress, 
                   Customer.BillingAddress, 
                   Company.DefaultAddress
```

### Context Field with Entered Check

```
Context Fields
    DISCOUNTRATE is Decimal size 5.2
        if (PromotionCode entered)
            DiscountRate = Promotion.DiscountRate
        else
            DiscountRate = Customer.StandardDiscount
```

### Versioned Context Field

```
Context Fields
    PRICELISTVERSION is Integer
        exact version is EffectiveDate
        PriceListVersion = PriceList.Version
```

## Complex Examples

### Multi-Level Conditional Context

```
Context Fields
    TAXRATE is Decimal size 5.4
        if (TaxExempt in context)
            if TaxExempt == true
                TaxRate = 0
            else
                if ShipToState in context
                    TaxRate = StateTaxRate.Rate
                else
                    TaxRate = Company.DefaultTaxRate
        else
            TaxRate = Company.DefaultTaxRate
```

### Context Field with Multiple Rules

```
Context Fields
    APPROVALTHRESHOLD is Decimal size 15.2
        required "Approval threshold must be specified"
        default to Department.ApprovalLimit, 
                   Company.DefaultApprovalLimit, 
                   1000.00
        constraint ApprovalThreshold > 0
            "Approval threshold must be positive"
        constraint ApprovalThreshold <= 1000000
            "Approval threshold cannot exceed 1,000,000"
```

### Context Field with Business Logic

```
Context Fields
    EFFECTIVEPRICE is Decimal size 15.2
        if (PromotionCode entered)
            if Promotion.DiscountType == "Percentage"
                EffectivePrice = BasePrice * (1 - Promotion.DiscountRate)
            else
                EffectivePrice = BasePrice - Promotion.DiscountAmount
        else
            if Customer.PriceLevel in context
                EffectivePrice = PriceList.Price
            else
                EffectivePrice = BasePrice
        
        constraint EffectivePrice > 0
            "Effective price must be greater than zero"
```

## Best Practices

### Context Field Design

- Use context fields for derived or calculated values
- Keep context field logic simple and maintainable
- Document complex context field calculations
- Consider performance implications of context field evaluation

### Naming Conventions

- Use descriptive names that indicate the field's purpose
- Follow consistent naming patterns across similar fields
- Consider prefixing with "Effective", "Calculated", or "Derived" when appropriate

### Default Values

- Order defaults from most specific to most general
- Always provide a final literal default when possible
- Document the fallback logic
- Test all default scenarios

### Constraints

- Provide clear, user-friendly error messages
- Validate business rules at the context field level
- Consider all edge cases
- Test constraint violations

### Required Fields

- Mark fields as required only when truly necessary
- Provide helpful error messages
- Consider conditional requirements based on context
- Document why fields are required

### Conditional Logic

- Keep conditional logic as simple as possible
- Use nested conditions sparingly
- Document complex conditional logic
- Test all branches of conditional logic

### Versioning

- Use `latest` for configuration-type relationships
- Use `exact version` when historical accuracy is critical
- Document versioning strategy
- Consider the impact on queries and performance

## Common Patterns

### Calculated Context Field

```
Context Fields
    EXTENDEDPRICE is Decimal size 15.2
        ExtendedPrice = Quantity * UnitPrice
```

### Lookup Context Field

```
Context Fields
    CUSTOMERNAME is Alpha size 50
        CustomerName = Customer.Name
        default to "Unknown Customer"
```

### Conditional Assignment Pattern

```
Context Fields
    SHIPPINGMETHOD is Alpha size 20
        if (ExpressShipping entered)
            if ExpressShipping == true
                ShippingMethod = "Express"
            else
                ShippingMethod = "Standard"
        else
            ShippingMethod = "Standard"
```

### Multi-Source Default Pattern

```
Context Fields
    BILLINGADDRESS is Address group
        default to Invoice.BillingAddress,
                   Customer.BillingAddress,
                   Customer.ShippingAddress,
                   Company.Address
```

### Validated Context Field Pattern

```
Context Fields
    ORDERQUANTITY is Integer
        required "Order quantity is required"
        constraint OrderQuantity > 0
            "Order quantity must be greater than zero"
        constraint OrderQuantity <= Product.MaxOrderQuantity
            "Order quantity exceeds maximum allowed"
```

## Execution Order

Context rules are not automatically arranged for execution as are Field Rules. The order they are in is the order in which they are executed.

**Important:** This means:
- Later context fields can reference earlier ones
- Earlier context fields cannot reference later ones
- Order matters when there are dependencies
- Plan the sequence carefully

### Example of Order Dependency

```
Context Fields
    // This must come first
    BASEPRICE is Decimal size 15.2
        BasePrice = Product.Price
    
    // This can reference BASEPRICE because it comes after
    DISCOUNTEDPRICE is Decimal size 15.2
        DiscountedPrice = BasePrice * (1 - DiscountRate)
    
    // This can reference both previous fields
    FINALPRICE is Decimal size 15.2
        FinalPrice = DiscountedPrice + TaxAmount
```

## Non-Ontological Nature

Context fields are 'non-ontological' context variables, which means:
- They are not required to be in the ontological context
- They are evaluated newly each time the field is used
- They provide additional calculated or derived values
- They don't affect the core business class structure

## Business Class Context Field Attributes

### Default Label

```
[<DefaultLabel>]
```

Provides a default display label for the context field.

### Analytical Attributes

```
[is (condition | related (link | value)) for <RelatedValue>]
```

Specifies the type of analytical attribute:
- **condition**: Context field is a condition for the specified business class
- **related link**: Context field is a related link for the specified business class
- **related value**: Context field is a related value for the specified business class

RelatedValue must be a BusinessClass name.

### Dimension and Measure Restrictions

```
[(dimensions [with attributes] | measures) only]
```

Restricts the context field usage:
- **dimensions [with attributes] only**: Context field is only valid for dimensions (optionally with attributes)
- **measures only**: Context field is only valid for measures

RelatedValue must be a BusinessClass name.

## Business Class Context Field Examples

### Context Field with Default Label

```
SALESFACT is a BusinessClass
    Context Fields
        FISCALYEAR is Integer
            default label is "Fiscal Year"
            FiscalYear = CurrentFiscalYear
```

### Context Field for Analytical Cube

```
SALESFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Context Fields
        PRODUCTCATEGORY
            is related link for PRODUCT
            dimensions only
        
        SALESREGION
            is related value for CUSTOMER
            dimensions only
```

### Context Field with Dimension Attributes

```
Context Fields
    PRODUCTATTRIBUTES
        is related link for PRODUCT
        dimensions with attributes only
```

### Context Field for Measures

```
Context Fields
    MEASURECALCULATION
        is condition for SALESFACT
        measures only
```

### Multiple Analytical Context Fields

```
ANALYTICFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Context Fields
        TIMEPERIOD
            default label is "Time Period"
            is related value for PERIOD
            dimensions only
        
        ACCOUNTTYPE
            default label is "Account Type"
            is condition for ACCOUNT
            dimensions only
        
        AGGREGATIONRULE
            default label is "Aggregation Rule"
            is condition for MEASURE
            measures only
```

## Related Concepts

### Ontological Context

Unlike ontological context fields (defined in the Context section of a KeyField), context fields are:
- Not part of the affordance structure
- Re-evaluated on each use
- Not automatically indexed
- More flexible but potentially less performant

### Derived Fields

Context fields are similar to derived fields but:
- Context fields are specific to the field definition
- Derived fields are part of the business class
- Context fields are evaluated in definition order
- Derived fields have automatic dependency resolution

### Field Rules

Context rules differ from field rules in that:
- Context rules execute in definition order
- Field rules have automatic dependency resolution
- Context rules are simpler and more direct
- Field rules are more powerful for complex logic

### Analytical Context

In AnalyticCube business classes, context fields can be restricted to:
- Dimensions only
- Dimensions with attributes
- Measures only

This provides fine-grained control over where context fields can be used in analytical scenarios.
