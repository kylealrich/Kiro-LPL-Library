# Local Fields

Local Fields are temporary fields that exist only during processing and are not persisted to the database. They are similar to transient fields but can also be defined within specific scopes like field definitions.

## Purpose

Local Fields are used for:
- Temporary calculations during processing
- Working variables in complex logic
- Intermediate values in rules
- Loop counters and iterators
- Temporary storage during actions

## Syntax

```
Local Fields
    <FieldName> [( <DataDefinition>
                  | is a <BusinessClass> view)]
        [<DefaultLabel>]
        [value is <RelatedField>]
        [<States>]
        [holds pii]
        [precision is <RelatedValue>]
        [round to precision]
        [as of <RelatedValue>]
        [disable surrogates]
        [[exact] version is (<RelatedValue> | latest)]
        [data area is <RelatedValue>]
        [is (condition | related (link | value)) for <RelatedValue>]
        [(dimensions [with attributes] | measures) only]
        [<TextVariables>]
        [document template for <BusinessClass>]
        [store as BusinessObjectReference]
        [do not save in checkpoint]
```

## Field Attributes

### Data Definition

```
[( <DataDefinition>
 | is a <BusinessClass> view)]
```

Defines the field type:
- Standard data definition (Alpha, Decimal, Date, etc.)
- Business class view for complex types

### Default Label

```
[<DefaultLabel>]
```

Provides a default display label for the field.

### Value

```
[value is <RelatedField>]
```

Initializes the field with a value from another field.

### States

```
[<States>]
```

Defines states for the field.

### Privacy

```
[holds pii]
```

Marks the field as containing Personally Identifiable Information.

### Precision

```
[precision is <RelatedValue>]
[round to precision]
```

Sets numeric precision and rounding behavior.

### As Of Date

```
[as of <RelatedValue>]
```

Retrieves versioned data as of a specific date.

### Surrogates

```
[disable surrogates]
```

Disables surrogate key handling.

### Versioning

```
[[exact] version is (<RelatedValue> | latest)]
```

Specifies version to retrieve for versioned key fields.

### Data Area

```
[data area is <RelatedValue>]
```

Valid only when in a 'stored in environment' busclass.

### Analytical Attributes

```
[is (condition | related (link | value)) for <RelatedValue>]
[(dimensions [with attributes] | measures) only]
```

RelatedValue must be a BusinessClass name. Used in analytical contexts.

### Text Variables

```
[<TextVariables>]
```

Allows text variable substitution.

### Document Template

```
[document template for <BusinessClass>]
```

Defines the field as a document template.

### Business Object Reference

```
[store as BusinessObjectReference]
```

Valid on a KeyField only.

### Checkpoint

```
[do not save in checkpoint]
```

Prevents the field from being saved in checkpoints during long-running processes.

## Examples

### Simple Local Fields

```
CUSTOMER is a BusinessClass
    Local Fields
        COUNTER is Integer
            default label is "Counter"
        
        TEMPAMOUNT is Decimal size 15.2
            precision is 2
            round to precision
            default label is "Temporary Amount"
        
        WORKINGFLAG is Boolean
            default label is "Working Flag"
```

### Local Fields with Initial Values

```
ORDER is a BusinessClass
    Persistent Fields
        SUBTOTAL is Decimal size 15.2
        TAXRATE is Decimal size 5.2
    
    Local Fields
        CALCULATEDTAX is Decimal size 15.2
            value is Subtotal * TaxRate / 100
            precision is 2
            round to precision
        
        WORKINGTOTAL is Decimal size 15.2
            value is Subtotal
            precision is 2
```

### Local Fields for Loop Processing

```
INVOICE is a BusinessClass
    Local Fields
        LINEINDEX is Integer
            default label is "Line Index"
        
        LINECOUNT is Integer
            default label is "Line Count"
        
        CURRENTLINE is InvoiceLine view
            default label is "Current Line"
```

### Local Fields with States

```
TRANSACTION is a BusinessClass
    Local Fields
        PROCESSSTATUS is Alpha size 20
            default label is "Process Status"
            
            States
                PENDING value is "Pending"
                PROCESSING value is "Processing"
                COMPLETED value is "Completed"
                ERROR value is "Error"
```

### Local Fields for Calculations

```
SALESORDER is a BusinessClass
    Local Fields
        RUNNINGSUBTOTAL is Decimal size 15.2
            precision is 2
            round to precision
        
        RUNNINGTAX is Decimal size 15.2
            precision is 2
            round to precision
        
        RUNNINGTOTAL is Decimal size 15.2
            precision is 2
            round to precision
        
        DISCOUNTAPPLIED is Boolean
```

### Local KeyField

```
EMPLOYEE is a BusinessClass
    Local Fields
        SELECTEDMANAGER is Employee
            default label is "Selected Manager"
        
        TEMPDEPARTMENT is Department
            default label is "Temporary Department"
```

### Local Fields with Versioning

```
CONTRACT is a BusinessClass
    Local Fields
        PRIORVERSION is Contract
            version is latest
            default label is "Prior Version"
        
        EFFECTIVECONTRACT is Contract
            as of EffectiveDate
            default label is "Effective Contract"
```

### Local Fields for Checkpoint Processing

```
BATCHPROCESS is a BusinessClass
    Local Fields
        BATCHCOUNTER is Integer
            default label is "Batch Counter"
        
        CURRENTRECORD is Alpha size 100
            default label is "Current Record"
            do not save in checkpoint
        
        TEMPBUFFER is Alpha size 1000
            do not save in checkpoint
```

### Local Fields with Business Class View

```
REPORT is a BusinessClass
    Local Fields
        CURRENTCUSTOMER is a CUSTOMER view
            default label is "Current Customer"
        
        SELECTEDPRODUCT is a PRODUCT view
            default label is "Selected Product"
```

### Local Fields for Analytical Processing

```
ANALYTICFACT is a BusinessClass
    Patterns
        implements AnalyticCube
    
    Local Fields
        DIMENSIONVALUE is Alpha size 50
            is related value for DIMENSION
            dimensions only
        
        MEASURECALCULATION is Decimal size 15.2
            is condition for MEASURE
            measures only
```

## Use Cases

### Loop Processing

```
Local Fields
    INDEX is Integer
    MAXINDEX is Integer
    CURRENTITEM is Item view
```

### Accumulation

```
Local Fields
    RUNNINGTOTAL is Decimal size 15.2
        precision is 2
        round to precision
    
    ITEMCOUNT is Integer
```

### Temporary Storage

```
Local Fields
    TEMPVALUE is Alpha size 100
    WORKINGBUFFER is Alpha size 1000
        do not save in checkpoint
```

### Status Tracking

```
Local Fields
    PROCESSSTATUS is Alpha size 20
        States
            STARTED value is "Started"
            INPROGRESS value is "InProgress"
            COMPLETED value is "Completed"
```

### Complex Calculations

```
Local Fields
    INTERMEDIATE1 is Decimal size 15.2
    INTERMEDIATE2 is Decimal size 15.2
    FINALRESULT is Decimal size 15.2
        precision is 4
        round to precision
```

## Best Practices

### Naming

- Use descriptive names that indicate temporary nature
- Consider prefixes like "Temp", "Working", "Current"
- Use clear, meaningful names
- Follow consistent naming conventions
- Avoid conflicts with persistent field names

### Scope

- Define local fields at appropriate scope
- Keep local fields close to where they're used
- Minimize the number of local fields
- Clean up unused local fields
- Document complex local field usage

### Performance

- Local fields have minimal overhead
- Use for temporary calculations
- Avoid unnecessary local fields
- Consider memory implications for large local fields
- Use `do not save in checkpoint` for large temporary data

### Checkpoint Processing

- Use `do not save in checkpoint` for:
  - Large temporary buffers
  - Frequently changing values
  - Data that can be recalculated
  - Intermediate calculation results
- Save in checkpoint for:
  - Loop counters
  - Accumulated values
  - State information
  - Data needed for recovery

### Initialization

- Initialize local fields with appropriate values
- Use `value is` for default values
- Consider null/empty handling
- Test initialization logic
- Document initialization requirements

## Differences from Other Field Types

| Aspect | Local Fields | Transient Fields | Persistent Fields |
|--------|--------------|------------------|-------------------|
| **Storage** | Not stored | Not stored | Stored in database |
| **Scope** | Business class or field | Business class | Business class |
| **Lifetime** | Processing only | Session/transaction | Permanent |
| **Checkpoint** | Optional | Not applicable | Always saved |
| **Auditing** | Not audited | Not audited | Can be audited |

## Checkpoint Behavior

### Default Behavior

By default, local fields are saved in checkpoints during long-running processes to support recovery.

### Do Not Save in Checkpoint

Use `do not save in checkpoint` for:
- Large temporary data
- Frequently changing values
- Data that can be recalculated
- Intermediate results

### Example

```
Local Fields
    BATCHCOUNTER is Integer
        // Saved in checkpoint for recovery
    
    TEMPBUFFER is Alpha size 10000
        do not save in checkpoint
        // Not saved - too large and can be recalculated
```

## Common Patterns

### Loop Counter Pattern

```
Local Fields
    INDEX is Integer
    MAXCOUNT is Integer
    CURRENTITEM is Item view
```

### Accumulator Pattern

```
Local Fields
    RUNNINGTOTAL is Decimal size 15.2
        value is 0
        precision is 2
    
    ITEMCOUNT is Integer
        value is 0
```

### State Machine Pattern

```
Local Fields
    CURRENTSTATE is Alpha size 20
        States
            INITIAL value is "Initial"
            PROCESSING value is "Processing"
            COMPLETE value is "Complete"
            ERROR value is "Error"
```

### Working Variables Pattern

```
Local Fields
    TEMP1 is Decimal size 15.2
    TEMP2 is Decimal size 15.2
    RESULT is Decimal size 15.2
        precision is 2
        round to precision
```

## Related Concepts

### Transient Fields

Similar to local fields but always at business class scope and user-enterable.

### Derived Fields

Calculated fields that are automatically maintained.

### Context Fields

Non-ontological context variables evaluated on use.

### Persistent Fields

Fields that are stored in the database.

## Limitations

- Not persisted to database
- Not audited
- Not effective-dated
- Not translatable
- Not encrypted
- Not text searchable
- Lost when processing completes

## Troubleshooting

### Common Issues

1. **Field not accessible**: Check scope of local field definition
2. **Value not initialized**: Add `value is` clause
3. **Checkpoint too large**: Use `do not save in checkpoint`
4. **Performance issues**: Reduce number of local fields
5. **Memory issues**: Use `do not save in checkpoint` for large fields

### Debugging Tips

- Verify local field definitions
- Check initialization values
- Test checkpoint behavior
- Monitor memory usage
- Review field scope
- Validate field references

## Summary

Local Fields provide:
- Temporary storage during processing
- Working variables for calculations
- Loop counters and iterators
- State tracking during execution
- Checkpoint control for long-running processes

Proper use of Local Fields enhances code clarity and supports complex business logic while maintaining good performance and memory management.
