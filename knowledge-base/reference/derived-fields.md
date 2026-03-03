# Derived Fields

Derived Fields are calculated fields whose values are automatically computed from other fields or expressions. They are not stored in the database but are calculated on-the-fly.

## Purpose

Derived Fields are used for:
- Calculated values based on other fields
- Conditional logic
- String concatenation
- Aggregations
- Measure calculations
- Dynamic labels and messages
- Complex business logic

## Syntax

```
Derived Fields
    <FieldName> is a[n] <DerivedFieldType>
        [type (<DataDefinition> | is a[n] <BusinessClass> view | is MessageField)]
        [precision is <RelatedValue>]
        [is (condition | related (link | value)) for <RelatedValue>]
        [<SqlName>]
        [<ClassicName>]
        [<DefaultLabel>]
        [restricted]
        [holds pii]
        (<DerivedFieldExpression> | <optionally blank for NativeField and InstanceCount>)
```

**Note:** Type is not required for all derived fields. On some derived fields, such as Compute, the type defaults from the fields in the compute statement.

## Derived Field Types

```
DerivedFieldType ::= ( aggregation of <FullFieldName>
                     | ConditionalField
                     | ComputeField
                     | MeasureField
                     | InstanceCount
                     | StringField
                     | MessageField
                     | LabelField
                     | DerivedField)
```

## Derived Field Expressions

### Aggregation Of

```
aggregation of <FullFieldName>
    when <Condition>
```

Useful for cube measures - returns FullFieldName value or 0 on instance depending on condition.

### Conditional Field

```
ConditionalField
    if <Condition>
        (<RelatedValue> | <ConditionalFieldControl>)…
    [else]
        (<RelatedValue> | <ConditionalFieldControl>)…
```

### Compute Field

```
ComputeField
    <Parens><RelatedValue> [<MathOperator> <RelatedValue>]<Parens>…
```

### Measure Field

```
MeasureField
    <Parens><MeasureValue> [<MathOperator> <MeasureValue>]<Parens>…
```

Where:
```
MeasureValue ::= ((sum | avg | min | max) <RelatedField> | instance count)
```

### Instance Count

```
InstanceCount
    [where <Condition>]
```

Useful for cube measures - returns 1 or 0 on instance depending on condition.

### String Field

```
StringField
    <RelatedValue> <RelatedValue> <RelatedValue>…
```

### Message Field

```
MessageField
    <Message>
```

Language translatable string that is treated as a Message (lowercase).

### Label Field

```
LabelField
    <Message>
```

Language translatable string that is treated as a Label (upper and lowercase).

### Derived Field

```
DerivedField
    (<Rule> | return [<RelatedValue>])…
```

## Math Operators

```
MathOperator ::= ( +   // Addition; also concatenates Alpha fields
                 | -   // Subtraction; also removes strings from Alpha fields
                 | *   // Multiplication
                 | /   // Division
                 | ^   // Exponentiation
                 | %)  // Modulo
```

**Special Alpha Operations:**
- `+` operator concatenates Alpha type fields
- `-` operator removes strings from Alpha type fields

## Examples

### Compute Field

```
INVOICE is a BusinessClass
    Persistent Fields
        SUBTOTAL is Decimal size 15.2
        TAXRATE is Decimal size 5.2
    
    Derived Fields
        TAXAMOUNT is a ComputeField
            type is Decimal size 15.2
            precision is 2
            default label is "Tax Amount"
            TaxAmount = Subtotal * TaxRate / 100
        
        TOTAL is a ComputeField
            type is Decimal size 15.2
            precision is 2
            default label is "Total"
            Total = Subtotal + TaxAmount
```

### Conditional Field

```
EMPLOYEE is a BusinessClass
    Persistent Fields
        SALARY is Decimal size 15.2
        EMPLOYEETYPE is Alpha size 20
    
    Derived Fields
        BONUSELIGIBLE is a ConditionalField
            type is Boolean
            default label is "Bonus Eligible"
            if EmployeeType == "FullTime" and Salary > 50000
                true
            else
                false
```

### String Field

```
CUSTOMER is a BusinessClass
    Persistent Fields
        FIRSTNAME is Alpha size 50
        LASTNAME is Alpha size 50
        TITLE is Alpha size 10
    
    Derived Fields
        FULLNAME is a StringField
            type is Alpha size 120
            default label is "Full Name"
            Title " " FirstName " " LastName
```

### Message Field

```
ORDER is a BusinessClass
    Persistent Fields
        ORDERSTATUS is Alpha size 20
    
    Derived Fields
        STATUSMESSAGE is a MessageField
            default label is "Status Message"
            "Order status is <OrderStatus>"
```

### Label Field

```
PRODUCT is a BusinessClass
    Persistent Fields
        PRODUCTCODE is Alpha size 20
        PRODUCTNAME is Alpha size 100
    
    Derived Fields
        PRODUCTLABEL is a LabelField
            default label is "Product Label"
            "<ProductCode> - <ProductName>"
```

### Aggregation Of

```
SALESFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Persistent Fields
        SALESAMOUNT is Decimal size 15.2
        ISACTIVE is Boolean
    
    Derived Fields
        ACTIVESALES is an aggregation of SALESAMOUNT
            when IsActive == true
```

### Instance Count

```
ANALYTICFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Persistent Fields
        AMOUNT is Decimal size 15.2
    
    Derived Fields
        LARGETRANSACTIONS is an InstanceCount
            where Amount > 10000
```

### Measure Field

```
SALESFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Persistent Fields
        QUANTITY is Decimal size 15.2
        UNITPRICE is Decimal size 15.2
    
    Derived Fields
        AVERAGEPRICE is a MeasureField
            type is Decimal size 15.2
            precision is 2
            avg UnitPrice
        
        TOTALQUANTITY is a MeasureField
            type is Decimal size 15.2
            sum Quantity
```

### Complex Conditional Field

```
EMPLOYEE is a BusinessClass
    Persistent Fields
        YEARSOFSERVICE is Integer
        PERFORMANCERATING is Alpha size 20
        SALARY is Decimal size 15.2
    
    Derived Fields
        BONUSAMOUNT is a ConditionalField
            type is Decimal size 15.2
            precision is 2
            default label is "Bonus Amount"
            if YearsOfService >= 10
                if PerformanceRating == "Excellent"
                    Salary * 0.15
                else if PerformanceRating == "Good"
                    Salary * 0.10
                else
                    Salary * 0.05
            else if YearsOfService >= 5
                if PerformanceRating == "Excellent"
                    Salary * 0.10
                else if PerformanceRating == "Good"
                    Salary * 0.07
                else
                    Salary * 0.03
            else
                if PerformanceRating == "Excellent"
                    Salary * 0.05
                else
                    0
```

### Derived Field with Rules

```
ORDER is a BusinessClass
    Persistent Fields
        SUBTOTAL is Decimal size 15.2
        DISCOUNTPERCENT is Decimal size 5.2
    
    Derived Fields
        DISCOUNTAMOUNT is a DerivedField
            type is Decimal size 15.2
            precision is 2
            default label is "Discount Amount"
            
            if DiscountPercent > 0
                return Subtotal * DiscountPercent / 100
            else
                return 0
```

### String Concatenation with Math Operators

```
PERSON is a BusinessClass
    Persistent Fields
        FIRSTNAME is Alpha size 50
        MIDDLENAME is Alpha size 50
        LASTNAME is Alpha size 50
    
    Derived Fields
        FULLNAME is a ComputeField
            type is Alpha size 150
            default label is "Full Name"
            FullName = FirstName + " " + MiddleName + " " + LastName
        
        FORMALLASTNAME is a ComputeField
            type is Alpha size 100
            default label is "Formal Last Name"
            FormalLastName = LastName + ", " + FirstName
```

### String Removal with Math Operators

```
PRODUCT is a BusinessClass
    Persistent Fields
        PRODUCTCODE is Alpha size 20
        PREFIX is Alpha size 5
    
    Derived Fields
        CODEWITHOUTP is a ComputeField
            type is Alpha size 20
            default label is "Code Without Prefix"
            CodeWithoutPrefix = ProductCode - Prefix
```

### Multiple Math Operations

```
CALCULATION is a BusinessClass
    Persistent Fields
        VALUE1 is Decimal size 15.2
        VALUE2 is Decimal size 15.2
        VALUE3 is Decimal size 15.2
    
    Derived Fields
        RESULT is a ComputeField
            type is Decimal size 15.2
            precision is 4
            default label is "Result"
            Result = (Value1 + Value2) * Value3 / 100
        
        POWER is a ComputeField
            type is Decimal size 15.2
            precision is 2
            default label is "Power"
            Power = Value1 ^ 2
        
        MODULO is a ComputeField
            type is Integer
            default label is "Modulo"
            Modulo = Value1 % Value2
```

### Business Class View

```
SALESORDER is a BusinessClass
    Persistent Fields
        CUSTOMER is Customer
    
    Derived Fields
        CUSTOMERVIEW is a DerivedField
            type is a CUSTOMER view
            default label is "Customer View"
            return Customer
```

## Use Cases

### Financial Calculations

```
Derived Fields
    GROSSPROFIT is a ComputeField
        type is Decimal size 15.2
        precision is 2
        GrossProfit = Revenue - Cost
    
    PROFITMARGIN is a ComputeField
        type is Decimal size 5.2
        precision is 2
        ProfitMargin = (Revenue - Cost) / Revenue * 100
```

### Status Determination

```
Derived Fields
    ORDERSTATUS is a ConditionalField
        type is Alpha size 20
        if ShipDate != blank
            "Shipped"
        else if PaymentReceived == true
            "Paid"
        else if OrderDate != blank
            "Pending"
        else
            "Draft"
```

### Name Formatting

```
Derived Fields
    DISPLAYNAME is a StringField
        type is Alpha size 150
        LastName ", " FirstName " " MiddleInitial
```

### Eligibility Checks

```
Derived Fields
    ELIGIBLE is a ConditionalField
        type is Boolean
        if Age >= 18 and Active == true and Balance > 0
            true
        else
            false
```

### Cube Measures

```
Derived Fields
    ACTIVECOUNT is an InstanceCount
        where Status == "Active"
    
    AVERAGEAMOUNT is a MeasureField
        type is Decimal size 15.2
        avg Amount
```

## Best Practices

### Performance

- Keep derived field calculations simple
- Avoid complex nested conditionals when possible
- Consider caching frequently-accessed derived fields
- Test performance with realistic data volumes
- Monitor calculation overhead

### Naming

- Use descriptive names that indicate calculated nature
- Follow consistent naming conventions
- Avoid conflicts with persistent field names
- Use clear, meaningful names
- Document complex calculations

### Type Specification

- Specify type explicitly for clarity
- Set appropriate precision for numeric fields
- Consider field size for string concatenations
- Test with boundary values
- Validate type compatibility

### Conditional Logic

- Keep conditional logic readable
- Use consistent indentation
- Test all branches
- Handle edge cases
- Document complex conditions

### String Operations

- Be careful with string concatenation lengths
- Handle null/blank values appropriately
- Test string removal operations
- Consider localization for messages
- Validate string operations

### Maintenance

- Document complex derived fields
- Keep calculations maintainable
- Review derived fields periodically
- Test after changes to source fields
- Consider impact on dependent fields

## Common Patterns

### Calculation Pattern

```
Derived Fields
    RESULT is a ComputeField
        type is Decimal size 15.2
        precision is 2
        Result = (Field1 + Field2) * Field3
```

### Conditional Pattern

```
Derived Fields
    STATUS is a ConditionalField
        type is Alpha size 20
        if Condition1
            "Value1"
        else if Condition2
            "Value2"
        else
            "Default"
```

### String Concatenation Pattern

```
Derived Fields
    COMBINED is a StringField
        type is Alpha size 200
        Field1 " - " Field2 " - " Field3
```

### Aggregation Pattern

```
Derived Fields
    FILTERED is an aggregation of AMOUNT
        when Status == "Active"
```

## Related Concepts

### Persistent Fields

Derived fields calculate values from persistent fields.

### Transient Fields

Both are not stored, but transient fields can be user-entered.

### Local Fields

Similar to derived fields but with different scope and usage.

### Compute Fields

A specific type of derived field for mathematical calculations.

### Conditional Fields

A specific type of derived field for conditional logic.

## Limitations

- Not stored in database
- Calculated on access
- Cannot be directly updated
- Performance overhead for complex calculations
- Not audited directly
- Not effective-dated directly

## Troubleshooting

### Common Issues

1. **Type mismatch**: Ensure compatible types in calculations
2. **Null values**: Handle null/blank values appropriately
3. **Performance**: Simplify complex calculations
4. **Circular references**: Avoid fields referencing each other
5. **String length**: Ensure concatenations fit in field size

### Debugging Tips

- Test derived fields with various data
- Check type compatibility
- Verify conditional logic branches
- Test with null/blank values
- Monitor calculation performance
- Review error messages carefully
- Test edge cases

## Summary

Derived Fields provide:
- Automatic calculation of values
- Conditional logic
- String manipulation
- Aggregations for analytics
- Dynamic messages and labels
- Complex business logic

Proper use of Derived Fields enhances functionality while maintaining data integrity and reducing redundancy.
