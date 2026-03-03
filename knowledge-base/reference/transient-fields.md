# Transient Fields

Transient Fields are mutable but not persistent. They exist only in memory during processing and are not stored in the database.

## Purpose

Transient fields are used for:
- Temporary calculations
- UI input fields that don't need persistence
- Intermediate values in business logic
- Working variables during processing
- Fields that derive their values from other fields

## Syntax

```
Transient Fields
    <FieldName> [<DataDefinition>]
        [<States>]
        [<DefaultLabel>]
        [scannable]
        [holds pii]
        [precision is <RelatedValue>]
        [round to precision]
        [primitive type is <RelatedValue>]
        [primitive size is <RelatedValue>]
        [primitive decimal size is <RelatedValue>]
        [as of <RelatedValue>]
        [within <RelatedValue>]
        [<Message>]
        [disable surrogates]
        [[exact] version is (<RelatedValue> | latest)]
        [data area is <RelatedValue>]
        [is (condition | related (link | value)) for <RelatedValue>]
        [(dimensions [with attributes] | measures) only]
        [<TextVariables>]
        [document template for <BusinessClass>]
        [store as BusinessObjectReference]
        [derive value from <RelatedValue>]
```

## Field Attributes

### Basic Attributes

| Attribute | Description |
|-----------|-------------|
| `<FieldName>` | Name of the transient field |
| `<DataDefinition>` | Data type definition |
| `<States>` | State definitions |
| `<DefaultLabel>` | Default display label |

### Privacy and Security

| Attribute | Description |
|-----------|-------------|
| `scannable` | Field can be scanned (e.g., barcode scanning) |
| `holds pii` | Marks field as containing Personally Identifiable Information |

### Precision and Type

| Attribute | Description |
|-----------|-------------|
| `precision is <RelatedValue>` | Sets numeric precision |
| `round to precision` | Rounds to specified precision |
| `primitive type is <RelatedValue>` | Must be of type 'PrimitiveType'; cannot be Text or BinaryDocument/Image |
| `primitive size is <RelatedValue>` | Sets primitive type size |
| `primitive decimal size is <RelatedValue>` | Sets decimal places |

### KeyField Attributes

| Attribute | Description |
|-----------|-------------|
| `as of <RelatedValue>` | Retrieves versioned data as of specified date/time |
| `within <RelatedValue>` | Constrains values within a set |
| `<Message>` | Optional error message |
| `disable surrogates` | Disables surrogate key handling |
| `[exact] version is (<RelatedValue> \| latest)` | Specifies version to retrieve |
| `data area is <RelatedValue>` | Valid only when in a 'stored in environment' busclass |
| `store as BusinessObjectReference` | Valid on a KeyField only |

### Analytical Attributes

```
[is (condition | related (link | value)) for <RelatedValue>]
[(dimensions [with attributes] | measures) only]
```

RelatedValue must be a BusinessClass name.

### Text Variables

```
[<TextVariables>]
```

Allows text variable substitution in the field.

### Document Template

```
[document template for <BusinessClass>]
```

Defines this field as a document template.

### Derive Value

```
[derive value from <RelatedValue>]
```

Specifies the value when field is not entered. This provides a default value for the transient field.

## Differences from Persistent Fields

| Aspect | Persistent Fields | Transient Fields |
|--------|------------------|------------------|
| **Storage** | Stored in database | Not stored |
| **Lifetime** | Permanent | Session/transaction only |
| **Auditing** | Can be audited | Not audited |
| **Effective Dating** | Can participate | Does not participate |
| **Translation** | Can be translatable | Not translatable |
| **Encryption** | Can be encrypted | Not encrypted |
| **Text Searchable** | Can be indexed | Not indexed |

## Examples

### Simple Transient Field

```
CUSTOMER is a BusinessClass
    Transient Fields
        SEARCHTEXT is Alpha size 100
            default label is "Search"
```

### Transient Field with Derived Value

```
Transient Fields
    DISCOUNTRATE is Decimal size 5.2
        derive value from Customer.DefaultDiscount
        precision is 2
        round to precision
        default label is "Discount Rate"
```

### Transient Field for Calculations

```
Transient Fields
    SUBTOTAL is Decimal size 15.2
        precision is 2
        round to precision
        default label is "Subtotal"
    
    TAXAMOUNT is Decimal size 15.2
        precision is 2
        round to precision
        default label is "Tax Amount"
    
    GRANDTOTAL is Decimal size 15.2
        precision is 2
        round to precision
        default label is "Grand Total"
```

### Transient KeyField

```
Transient Fields
    SELECTEDPRODUCT is Product
        default label is "Selected Product"
    
    SELECTEDCUSTOMER is Customer
        default label is "Selected Customer"
```

### Transient Field with States

```
Transient Fields
    SEARCHTYPE is Alpha size 20
        default label is "Search Type"
        
        States
            BYNAME value is "Name"
            BYCODE value is "Code"
            BYDESCRIPTION value is "Description"
```

### Transient Field for UI Input

```
Transient Fields
    FROMDATE is Date
        default label is "From Date"
        derive value from current date - 30 days
    
    TODATE is Date
        default label is "To Date"
        derive value from current date
```

### Example: PurchaseOrder - Complex Transient Fields

From the PurchaseOrder business class, demonstrating enterprise transient field patterns:

```
Transient Fields
    StandardCommentCode
    
    TransientTemplateDisplaySequence is Numeric size 1
        States
            Line    value is 1
            Item    value is 2
    
    TransientTemplateDefaultAccountingUnit is AlphaUpper up to 20
    TransientContactCode is a VendorContact
    TransientFromMassPOCreateAndRelease is Boolean
    
    TransientCancelOriginationProgram is Numeric size 2
        States
            PurchaseOrder               value is 0
            Receiving                   value is 1
            VendorReturns               value is 2
            Invoicing                   value is 3
            PurchaseOrderInterface      value is 4
            Contract                    value is 5
            ReceivingAdjustment         value is 6
    
    TransientDropShipToInventoryLocationAddress is Boolean
    
    TransientReceivingOptions is Numeric size 1
        default label is "DropshipReceivingOptions"
        derive value from Dropship
        States
            ReceivingRequired       value is 0
            ReceivingNotAllowed     value is 1
    
    TransientMessage is Alpha size 75
    
    TransientReleaseOriginationProgram is Numeric size 2
        States
            Receiving                   value is 1
            Cancel                      value is 2
            OnlineAuthorization         value is 3
    
    // Healthcare-specific transient fields with PII
    TransientVisitNumber is a PatientVisit
        derive value from DerivedTranVisitNumber
    
    TransientPatientID is a Patient
        derive value from DerivedTranPatientID
    
    TransientCaseNumber is a CaseNumber
        derive value from DerivedTranCaseNumber
    
    TransientPatientLastName is a LastName
        derive value from DerivedTranPatientLastName
    
    TransientPatientFirstName is a FirstName    
        holds pii
        derive value from DerivedTranPatientFirstName
    
    TransientPatientMiddleName is a MiddleName
        derive value from DerivedTranPatientMiddleName
    
    TransientBirthdate is Date
        derive value from DerivedTranBirthdate
    
    TransientGender is a Gender
        derive value from DerivedTranGender
    
    TransientSocialSecurityNumber is a TaxID 
        holds pii
        derive value from DerivedTranSocialSecurityNumber
    
    // Derived from relation
    TransientGLCalendarPeriod is a GeneralLedgerCalendarPeriod    
        derive value from GeneralLedgerCalendarPeriodXrefRel.GeneralLedgerCalendarPeriod
    
    TransientMassIssue is Boolean
    
    TransientCurrencyMultDiv is Numeric size 1
        derive value from DerivedCurrencyMultDiv
        States        
            Multiply value is 0
            Divide   value is 1
    
    // Financial transient fields for old values
    TransientOldPOAOCAmount is an InternationalAmount 
    TransientOldPOPrtAmount is an InternationalAmount 
    TransientOldPOTaxAmount is an InternationalAmount 
    TransientOldPOVGrdAmount is an InternationalAmount 
    TransientOldOVGrdAmount is an InternationalAmount 
    TransientOldTotOrdAmount is an InternationalAmount 
    
    FromInterfaceUpdate is Boolean
    PurchaseOrderUpdateFromMatchUnmatchFlag is Boolean
    TransientCopyAllLines is Boolean
    TransientFromSourcing is Boolean
```

This example demonstrates:
- **State fields**: Multiple transient fields with defined states for workflow tracking
- **Derived values**: Fields that derive from persistent fields or derived fields
- **PII marking**: Healthcare fields marked with `holds pii` for compliance
- **Origination tracking**: Fields tracking where operations originated from
- **Financial snapshots**: Transient fields storing old values for comparison
- **Boolean flags**: Simple flags for process control
- **Complex types**: KeyFields and group fields as transient fields
- **Relation-derived values**: Values derived from related business classes
- **Industry-specific fields**: Healthcare patient information fields
- **Naming conventions**: Consistent "Transient" prefix for clarity
- **Integration flags**: Fields for interface and batch processing control
    TODATE is Date
        default label is "To Date"
        derive value from current date
```

### Transient Field with Text Variables

```
Transient Fields
    MESSAGETEXT is Alpha size 500
        default label is "Message"
        
        Text Variables
            CUSTOMERNAME value is Customer.Name
            ORDERDATE value is Order.OrderDate
```

### Transient Field for Resequencing

```
Transient Fields
    NEWSEQUENCE is Integer
        default label is "New Sequence"
        derive value from -1
```

### Transient Field with Precision

```
Transient Fields
    CALCULATEDPRICE is Decimal size 15.4
        precision is 4
        round to precision
        default label is "Calculated Price"
```

### Transient Boolean Field

```
Transient Fields
    INCLUDEHISTORICAL is Boolean
        derive value from false
        default label is "Include Historical"
    
    SHOWDETAILS is Boolean
        derive value from true
        default label is "Show Details"
```

### Transient Field for Filtering

```
Transient Fields
    STATUSFILTER is Alpha size 20
        default label is "Status Filter"
        derive value from "Active"
        
        States
            ALL value is "All"
            ACTIVE value is "Active"
            INACTIVE value is "Inactive"
            PENDING value is "Pending"
```

### Transient Field with Versioning

```
Transient Fields
    PRICELISTVERSION is Integer
        version is latest
        default label is "Price List Version"
```

### Transient Field for Aggregation

```
Transient Fields
    SELECTEDCOUNT is Integer
        default label is "Selected Count"
        derive value from 0
    
    SELECTEDTOTAL is Decimal size 15.2
        precision is 2
        round to precision
        default label is "Selected Total"
        derive value from 0
```

## Common Use Cases

### Search and Filter Fields

```
Transient Fields
    SEARCHCRITERIA is Alpha size 100
        default label is "Search Criteria"
    
    DATERANGEFROM is Date
        default label is "From Date"
        derive value from current date - 90 days
    
    DATERANGETO is Date
        default label is "To Date"
        derive value from current date
    
    STATUSFILTER is Alpha size 20
        default label is "Status"
        derive value from "All"
```

### Calculation Working Fields

```
Transient Fields
    WORKINGSUBTOTAL is Decimal size 15.2
        precision is 2
    
    WORKINGDISCOUNT is Decimal size 15.2
        precision is 2
    
    WORKINGTAX is Decimal size 15.2
        precision is 2
```

### UI State Management

```
Transient Fields
    CURRENTTAB is Integer
        derive value from 1
        default label is "Current Tab"
    
    EXPANDEDPANEL is Boolean
        derive value from false
        default label is "Expanded"
    
    SELECTEDROW is Integer
        derive value from 0
        default label is "Selected Row"
```

### Temporary Selections

```
Transient Fields
    SELECTEDITEMS is Alpha size 1000
        default label is "Selected Items"
    
    SELECTIONCOUNT is Integer
        derive value from 0
        default label is "Selection Count"
```

### Wizard/Multi-Step Process Fields

```
Transient Fields
    CURRENTSTEP is Integer
        derive value from 1
        default label is "Current Step"
    
    COMPLETEDSTEPS is Alpha size 100
        default label is "Completed Steps"
    
    CANPROCEED is Boolean
        derive value from false
        default label is "Can Proceed"
```

## Best Practices

### When to Use Transient Fields

- For temporary calculations that don't need persistence
- For UI input fields that control behavior but don't need storage
- For working variables during complex processing
- For search and filter criteria
- For wizard or multi-step process state
- For derived values that can be recalculated

### When NOT to Use Transient Fields

- For data that needs to be stored permanently
- For data that needs to be audited
- For data that needs to be effective-dated
- For data that needs to be translated
- For data that needs to be searched in the database

### Naming Conventions

- Use descriptive names that indicate temporary nature
- Consider prefixes like "Working", "Temp", "Selected", "Current"
- Use clear, meaningful names for UI fields
- Follow consistent naming patterns

### Default Values

- Use `derive value from` to provide sensible defaults
- Set defaults that minimize user input
- Consider user experience when setting defaults
- Test default values thoroughly

### Performance

- Transient fields have no database overhead
- Use transient fields for frequently-calculated values
- Avoid unnecessary transient fields
- Consider memory implications for large transient fields

### UI Design

- Use transient fields for search criteria
- Use transient fields for filter options
- Use transient fields for temporary selections
- Provide clear labels for user-facing transient fields

### Calculations

- Use transient fields for intermediate calculations
- Document calculation logic
- Test calculation accuracy
- Consider precision requirements

### State Management

- Use transient fields to track UI state
- Use transient fields for wizard progress
- Use transient fields for temporary flags
- Reset transient fields appropriately

## Transient vs Derived vs Local Fields

| Field Type | Storage | Calculation | Scope |
|------------|---------|-------------|-------|
| **Transient** | Not stored | Can be derived or entered | Business class |
| **Derived** | Not stored | Always calculated | Business class |
| **Local** | Not stored | Can be derived or calculated | Business class or field |
| **Persistent** | Stored | Can have defaults | Business class |

### Choosing the Right Field Type

- **Use Transient** when you need a mutable field that isn't persisted
- **Use Derived** when the value is always calculated from other fields
- **Use Local** when you need a temporary field within a specific scope
- **Use Persistent** when the value needs to be stored in the database

## Related Concepts

### Derived Fields

Derived fields are similar to transient fields but their values are always calculated. Transient fields can be entered by users or derived.

### Local Fields

Local fields are scoped to a specific context (business class or field definition) and are not persistent.

### Context Fields

Context fields are non-ontological context variables that are evaluated each time they're used.

### Field Rules

Field rules can populate transient fields based on business logic and other field values.

## Limitations

- Cannot be audited
- Cannot participate in effective dating
- Cannot be translated
- Cannot be encrypted
- Cannot be text searchable
- Not stored in database
- Lost when session/transaction ends
- Cannot be used in database queries directly
