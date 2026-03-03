# Persistent Fields

Persistent Fields are fields that are stored in the database as part of the business class.

## Syntax

```
Persistent Fields
    <FieldName> [<DataDefinition>]
        [<States>]
        [<SqlName> | <SqlPrefix>]
        [<ClassicName>]
        [<ClassicNameForField>]
        [<DefaultLabel>]
        [encrypt]
        [automate context]
        [enable alternate document location]
        [document is <RelatedField>]
        [existence is <RelatedField>]
        [allow images]
        [text searchable]
        [scannable]
        [holds pii]
        [translatable]
        [disable Auditing [when in background]]
        [disable EffectiveDated]
        [enable EffectiveDated]
        [restricted]
        [protected]
        [precision is <RelatedValue>]
        [round to precision]
        [primitive type is <RelatedValue>]
        [primitive size is <RelatedValue>]
        [primitive decimal size is <RelatedValue>]
        [create value is blank if no entry]
        [disable surrogates]
        [context of <FullFieldName>]…
        [(delete cascades | delete ignored)]
        [as of <RelatedValue>]
        [within <RelatedValue>]
        [<Message>]
        [[exact] version is (<RelatedValue> | latest)]
        [data area is <RelatedValue>]
        [is (condition | related (link | value)) for <RelatedValue>]
        [(dimensions [with attributes] | measures) only]
        [<TextVariables>]
        [document template for <BusinessClass>]
        [store as BusinessObjectReference]
        [<SqlPrefix>]
        [Valid Business Classes]
        [snapshot of]
        [from <BusinessClass>]
```

## Field Attributes

### Security and Privacy

| Attribute | Description | Valid For |
|-----------|-------------|-----------|
| `encrypt` | Causes the field to be encrypted in the database | Alpha fields only |
| `holds pii` | Marks field as containing Personally Identifiable Information | All fields |
| `restricted` | Cannot be used as field on UI | All fields |
| `protected` | Application-controlled field; cannot be updated from UI, webservices, or spreadsheet | All fields |

### Context and Automation

| Attribute | Description |
|-----------|-------------|
| `automate context` | If the field is a KeyField, ensures all context variables are placed on this business class |
| `context of <FullFieldName>` | Disambiguates which field should be used as the Context field for this KeyField |
| `disable surrogates` | Disables surrogate key handling |

### Document Fields

| Attribute | Description | Valid For |
|-----------|-------------|-----------|
| `enable alternate document location` | Enables alternate storage location | BinaryDocument, BinaryObject, CSVText, JSONObject, RichText, Text, TextDocument, XMLDocument |
| `document is <RelatedField>` | References the document field | Document fields |
| `allow images` | Allows images in the field | RichText fields only |
| `document template for <BusinessClass>` | Defines this field as a document template | BinaryDocument fields |

### Existence Control

```
[existence is <RelatedField>]
```

Must be a Boolean type field that controls whether this field exists.

### Search and Scan

| Attribute | Description |
|-----------|-------------|
| `text searchable` | Field will be search text indexed by the database |
| `scannable` | Field can be scanned (e.g., barcode scanning) |

### Translation

```
[translatable]
```

Allows for data translation for this field.

### Auditing and Effective Dating

| Attribute | Description |
|-----------|-------------|
| `disable Auditing [when in background]` | Field will not be audited (optionally only when in background) |
| `disable EffectiveDated` | Field will not participate in EffectiveDated pattern |
| `enable EffectiveDated` | Field will participate in EffectiveDated pattern (valid on BinaryDocument and BinaryObject only) |

### Precision and Type

| Attribute | Description |
|-----------|-------------|
| `precision is <RelatedValue>` | Sets numeric precision |
| `round to precision` | Rounds to specified precision |
| `primitive type is <RelatedValue>` | Must be of type 'PrimitiveType'; cannot be Text or BinaryDocument/Image |
| `primitive size is <RelatedValue>` | Sets primitive type size |
| `primitive decimal size is <RelatedValue>` | Sets decimal places |

### Audit History

```
[create value is blank if no entry]
```

If a field is added after a record is created, typically the first value in the audit log applies as the value from the point of create. With this pattern, the value from create will be considered blank.

## KeyField Attributes

### Delete Rules

```
[(delete cascades | delete ignored)]
```

`<FieldName>` must be a KeyField. The default delete rule for a KeyField is delete restricted. Use of a KeyField will cause an OTM back relation to be automatically generated.

| Rule | Description |
|------|-------------|
| `delete cascades` | Child records are deleted when parent is deleted |
| `delete ignored` | Deletion of parent ignores child records |
| `delete restricted` (default) | Deletion is prevented if child records exist |

### As Of Date

```
[as of <RelatedValue>]
```

- `<FieldName>` must be a KeyField
- `<RelatedValue>` must be a TimeStamp or a Date
- This directive will retrieve the business class that this keyfield points to 'as of' the specified date and time
- An implication of this is that the one-to-many relation from the keyfield's business class back to this business class will be 'delete ignored'
- If `<RelatedValue>` is a Date, then the last entry on that date will be retrieved

### Within Constraint

```
[within <RelatedValue>]
[<Message>]
```

Valid on a KeyField only. `<RelatedValue>` must be a field reference via an OTM relation that defines a set of KeyFields of this KeyField's type. `<Message>` is an optional error message.

### Versioning

```
[[exact] version is (<RelatedValue> | latest)]
```

This directive specifies the particular version of a versioned key field to retrieve:
- If the version field is of type Date, TimeStamp or Time, this will default to the current date, current timestamp or current time
- Otherwise it will default to the latest version
- If there is no exact match on the version, it will return the closest version prior to the version specified
- If `exact version is` is specified, then there must be an exact match on the version
- If `latest` version is specified, then it will always find the last version even if that is a future version

### Data Area

```
[data area is <RelatedValue>]
```

Valid only when in a 'stored in environment' busclass.

### Analytical Fields

```
[is (condition | related (link | value)) for <RelatedValue>]
[(dimensions [with attributes] | measures) only]
```

RelatedValue must be a BusinessClass name.

### Business Object Reference

```
[store as BusinessObjectReference]
[<SqlPrefix>]
```

Valid on a KeyField only. This is required when trying to assign this value from an asynchronous operation. LawsonClassicTransactions are an example of an asynchronous operation. When creating a new record via an asynchronous operation, only the BusinessObjectReference of the record is immediately available and thus only that aspect of the keyfield can actually be stored. A BusinessObjectReference is internally a GroupField and may have sql naming conflicts. The `sql prefix` statement can be used to overcome any naming conflict.

### Valid Business Classes

```
[Valid Business Classes]
    <BusinessClass>…
```

Valid on a BusinessObjectReference only. Specifies which business classes are valid for this reference.

## Text Variables

```
TextVariables ::= Text Variables [<Parens>locale of <RelatedValue><Parens>]
                  <TextVariable> [value is <RelatedValue>]…
```

Text variables can be defined for any Alpha type field. This allows the user who is entering data in this field to put in variable substitution syntax that references these defined variable names.

### Text Variable Syntax

The syntax to use a text variable is `{TextVariable}`. Notice the curly braces special syntax that surrounds the TextVariable name.

### Text Variable Behavior

- TextVariables can be one-to-many valued
- If the variable starts at the beginning of a line and ends with a new line character, then it will be copied down – each OTM being a new line
- If not, then it will act as though it is in paragraph mode

### Optional Values

A value does not have to be ascribed to a TextVariable definition. This will mean that no substitution ever takes place. This is useful when defining text fields that are used as templates for other text fields. The only issue is that the designer needs to make sure the variable name match on both the template and the target.

### Context Field Variables

If a text variable uses a Context Field in its RelatedLink, then the value of the Context Field must be specified as such: `{TextVariable:<ContextFieldValue>}`

### Direct LPL References

RelatedValues can be directly used for variable replacement by prefixing the related value with `lpl:`, making the syntax `{lpl:<RelatedValue>}`. This allows for UserFields and other dynamic LPL constructs to be used at runtime without having to have a TextVariable.

## Document Templates

```
[document template for <BusinessClass>]
```

This field must be of the type 'BinaryDocument'. A document template is a 'rtf' file with standard Word Doc replacement variables which must be of LPL syntax. When the 'document' TypeOperator is used on this field, the 'rtf' is sent through a variable replacement transformation resulting in another 'rtf' file that can be assigned to a BinaryDocument field. This allows for Word Documents to be generated within LPL.

## Snapshot Fields

```
<FieldName> is a snapshot of <RelatedField>
    [when <Condition>]
```

Snapshot sets and keeps this field the same as RelatedField only when this business object is being updated. This can optionally be done based on a Condition.

## From Business Class

```
(<FieldName> | <FieldGroupName>) from <BusinessClass>
    (audit fields | <ActionAttribute>…)
```

Valid only when Auditing and EffectiveDated are disabled. Allows fields to be pulled from another business class's audit information.

## Aggregate Operator

```
AggregateOp ::= ( aggregate
                | instance count)
```

The aggregate operator can be used only in a business class that is an aggregate of an aggregated `<BusinessClass>`:
- If the operator `instance count` is used, `.<FullFieldName>` cannot be specified
- If the operator `aggregate` is used, `.<FullFieldName>` must be specified and must be a field in the aggregated `<BusinessClass>` that is capable of being aggregated
- It must be some form of numeric field
- It can be a group field with non-aggregatable fields in it if there is at least one aggregatable field

## Examples

### Simple Persistent Fields

```
CUSTOMER is a BusinessClass
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNAME is Alpha size 50
            text searchable
            default label is "Customer Name"
        
        CREDITLIMIT is Decimal size 15.2
            precision is 2
            round to precision
```

### Encrypted Field

```
Persistent Fields
    SSN is Alpha size 11
        encrypt
        holds pii
        protected
        default label is "Social Security Number"
```

### Document Field with Alternate Location

```
Persistent Fields
    ATTACHMENT is BinaryDocument
        enable alternate document location
        document is ATTACHMENTDATA
        holds pii
```

### KeyField with Delete Rule

```
Persistent Fields
    COMPANY is Company
        delete restricted
        automate context
    
    CUSTOMER is Customer
        delete cascades
```

### Versioned KeyField

```
Persistent Fields
    CONTRACT is Contract
        version is latest
        delete restricted
```

### As Of Date KeyField

```
Persistent Fields
    PRICELIST is PriceList
        as of OrderDate
        "Price list as of order date"
```

### Text Field with Variables

```
Persistent Fields
    EMAILTEMPLATE is Text
        Text Variables
            CUSTOMERNAME value is Customer.Name
            ORDERNUMBER value is Order.OrderNumber
            ORDERDATE value is Order.OrderDate
```

### Document Template

```
Persistent Fields
    INVOICETEMPLATE is BinaryDocument
        document template for INVOICE
        default label is "Invoice Template"
```

### Snapshot Field

```
Persistent Fields
    SNAPSHOTPRICE is Decimal size 15.2
        is a snapshot of Product.Price
        when OrderStatus == OrderStatus.Confirmed
```

### Translatable Field

```
Persistent Fields
    PRODUCTDESCRIPTION is Alpha size 255
        translatable
        text searchable
        default label is "Product Description"
```

### Protected Field

```
Persistent Fields
    SYSTEMCALCULATION is Decimal size 15.2
        protected
        disable Auditing
        default label is "System Calculation"
```

### Field with Existence Control

```
Persistent Fields
    HASALTERNATEADDRESS is Boolean
        default label is "Has Alternate Address"
    
    ALTERNATEADDRESS is Address group
        existence is HasAlternateAddress
```

### Example: PurchaseOrder - Complex Persistent Fields

From the PurchaseOrder business class, demonstrating enterprise-level persistent field patterns:

```
Persistent Fields
    PORelease
        protected
    POCode
    PurchaseOrderRevision
        classic name is PO-REVISION
        protected
    Vendor
    PurchaseFromLocation is a VendorLocation
        classic name is PURCH-FR-LOC
    Buyer
        classic name is BUYER-CODE
    PurchaseOrderDate is Date
        classic name is PO-DATE
    Currency
        classic name is CURRENCY-CODE
    
    // PII Fields with Group Field Classic Names
    DropshipName is a VendorName 
        holds pii
        classic name is SH-NAME
    DropshipAddress is a PostalAddressV2    
        holds pii
        classic name for DropshipAddress.DeliveryAddress.AddressLine1 is SH-ADDR1
        classic name for DropshipAddress.DeliveryAddress.AddressLine2 is SH-ADDR2
        classic name for DropshipAddress.Municipality is SH-CITY-ADDR5
        classic name for DropshipAddress.StateProvince is SH-STATE-PROV
        classic name for DropshipAddress.PostalCode is SH-POST-CODE
        classic name for DropshipAddress.Country is SH-COUNTRY-CD
    
    DropshipPhoneNumber is a TelephoneNumber 
        holds pii
        classic name for DropshipPhoneNumber.InternationalPrefix is SH-PHONE-PREF
        classic name for DropshipPhoneNumber.SubscriberNumber is SH-PHONE
        classic name for DropshipPhoneNumber.Extension is SH-PHONE-EXT
    
    // Protected Financial Fields with Auditing Disabled
    TotalProductAmount is an InternationalAmount
        classic name is TOT-PRD-AMT
        disable Auditing
        protected
    TotalOrderAmount is an InternationalAmount 
        classic name is TOT-ORDER-AMT
        disable Auditing
        protected
    OpenPurchaseAmount is an InternationalAmount
        classic name is OPEN-PO-AMT
        disable Auditing
    
    // State Field with Multiple Values
    Closed is AlphaUpper size 1
        classic name is CLOSED-FL
        protected
        States
            Yes         value is "Y"
            No          value is "N"
            Historical  value is "H"
    
    // Lifecycle State Field
    PurchaseOrderLifeCycleState is Numeric size 1
        States
            Unreleased          value is 0
            Released            value is 1
            Canceled            value is 2
            Closed              value is 3
            Historical          value is 4
            ReleaseInProgress   value is 5
            CreateInProgress    value is 6
            Deleted             value is 7
            CancelInProgress    value is 8
            DeleteInProgress    value is 9
    
    // Restricted and Protected IDM Fields
    IDMDocumentPID is an DocumentPID
        restricted
        protected
        disable Auditing
    IDMDocumentSequence is Numeric size 3
        restricted
        protected
        disable Auditing
    
    // Audit Fields with PII
    CreatedBy is an Operator 
        holds pii
    LastUpdateDate is TimeStamp
        classic name is LAST-UPDT-DATE
    LastUpdateBy is an Operator 
        holds pii
```

This example demonstrates:
- Protected fields that cannot be modified via UI
- PII marking for compliance
- Classic name mappings for group field components
- Auditing control with `disable Auditing`
- State fields with multiple defined values
- Numeric state fields for lifecycle management
- Restricted and protected fields for system use
- Complex group fields (PostalAddressV2, TelephoneNumber)
- Financial fields with InternationalAmount type
- Combination of protected, restricted, and auditing attributes

### Business Object Reference

```
Persistent Fields
    RELATEDOBJECT is BusinessObjectReference
        store as BusinessObjectReference
        sql prefix is "REL"
        
        Valid Business Classes
            Order
            Invoice
            Payment
```

### Aggregate Fields

```
SALESAGGREGATE is a BusinessClass
    Persistent Fields
        TOTALORDERS is Integer
            aggregate instance count from Order
        
        TOTALSALES is Decimal size 15.2
            aggregate Order.OrderAmount
```

## Best Practices

### Security

- Use `encrypt` for sensitive data
- Mark `holds pii` for compliance
- Use `protected` for system-calculated fields
- Use `restricted` to hide fields from UI

### Performance

- Use `text searchable` selectively
- Disable auditing for frequently-updated fields when appropriate
- Use alternate document locations for large binary data
- Consider indexing implications

### Data Integrity

- Set appropriate delete rules for KeyFields
- Use `within` constraints for validation
- Use `existence` control for optional field groups
- Validate precision and rounding requirements

### Translation

- Mark translatable fields appropriately
- Test with multiple locales
- Consider storage implications
- Document translation requirements

### Document Management

- Use alternate document locations for large files
- Implement document templates for generated documents
- Handle missing documents gracefully
- Consider document versioning

### Text Variables

- Define clear, meaningful variable names
- Document available variables
- Test variable substitution
- Handle missing values appropriately

### Versioning

- Use `latest` for current data access
- Use `exact version` for historical accuracy
- Document versioning strategy
- Test version retrieval

### Snapshots

- Use snapshots to capture point-in-time values
- Define clear snapshot conditions
- Document snapshot timing
- Test snapshot behavior

### Naming

- Use descriptive field names
- Provide SQL and classic names for compatibility
- Use consistent naming conventions
- Document field purposes


### Example: Requisition - Complex Enterprise Persistent Fields

From the Requisition business class, demonstrating various persistent field patterns:

```
Persistent Fields
	Requester
	DefaultDistributionAccount      is a RQHeaderDefaultCodeBlock
	DefaultGLEncumbranceReference   is AlphaUpper 60
	RequestingLocation
		classic name is REQ-LOCATION
	RequestedDeliveryDate			is Date
		classic name is REQ-DEL-DATE
	CreationDate					is TimeStamp
	ReferenceNumber
		classic name is REFERENCE-NO
	RequisitionDescription			is Alpha size 30
		classic name is REQ-DESC
	RequisitionImport				is like RequisitionImport
	InterfaceInProcess				is Boolean
	OriginatingInterfaceRun			is a RequisitionInterfaceResult
		delete ignored
	FromCompanyLocation
	DeliverTo
	Vendor
	PurchaseFromLocation			is a VendorLocation
		classic name is PURCH-FR-LOC
	Buyer
		classic name is BUYER-CODE
	VendorName
	PrintRequisition				is Boolean
		classic name is PRINT-REQ-FL
	LastRequisitionLine				is a LineNumber
		classic name is LAST-LINE-NBR
		disable Auditing
	LastDeliverySequence			is a RequisitionSequence
		classic name is LAST-DEL-SEQ
		disable Auditing
	ReturnSequence					is a RequisitionSequence
		classic name is RETURN-SEQ
	Status							is Numeric size 1
		protected
		States
			Unreleased			value is 0
			Released			value is 1
			CreateInProgress	value is 2
			InProcess			value is 5
			ReleaseInProgress	value is 7
			Rejected			value is 8
			Processed			value is 9
	PurchaseOrderUserField1
		classic name is PO-USER-FLD-1
	PurchaseOrderUserField3			is a UserField2
		classic name is PO-USER-FLD-3
	PurchaseOrderUserField5			is a UserFld2
		classic name is PO-USER-FLD-5
	UserDate1						is Date
	UserDate2						is Date
	LastApprovalDate				is Date
		classic name is LAST-APRV-DT
	Approved						is Boolean
		classic name is APPROVED-FL
	AttainedLevel					is an ApprovalLevel
		classic name is ATTAIN-LEVEL
	ApprovalLevel
		classic name is APRV-LEVEL
	NumberOfLines
		protected
		classic name is NBR-LINES
		disable Auditing
	NumberOfClosedLines
		protected
		classic name is CLOSED-LINES
		disable Auditing
	Closed							is Boolean
		protected
		classic name is CLOSED-FL
	InventoryItemsTotal				is an InternationalAmount
		protected
		classic name is I-TOTAL
		disable Auditing
	NonstockItemsTotal				is an InternationalAmount
		protected
		classic name is N-TOTAL
		disable Auditing
	SpecialAndServiceItemsSTotal	is an InternationalAmount
		protected
		classic name is X-S-TOTAL
		default label is "SpecialAndServiceItemsTotal"
		disable Auditing
	Printed							is Boolean
		classic name is PRINTED-FL
	AllocationPriority
		classic name is ALLOC-PRI
	QuoteRequired					is Boolean
		classic name is QUOTE-FL
	ReturnNote						is a Note
	ReturnDate						is Date
	Asset
	AssetTemplate
	PurchaseTaxable					is Boolean
		classic name is PURCH-TAX-FL
	PurchaseTaxCode					is a TaxCode
		classic name is PURCH-TAX-CD
	OperatorID						is an Operator 
		holds pii
	Dropship						is Boolean
		classic name is DROPSHIP-FL
		default label is "ReceivingOptions"
	DropshipAddress					is a PostalAddressV2 
		holds pii
		classic name for DropshipAddress.DeliveryAddress.AddressLine1 is SH-ADDR1
		classic name for DropshipAddress.DeliveryAddress.AddressLine2 is SH-ADDR2
		classic name for DropshipAddress.DeliveryAddress.AddressLine3 is SH-ADDR3
		classic name for DropshipAddress.DeliveryAddress.AddressLine4 is SH-ADDR4
		classic name for DropshipAddress.Municipality is SH-CITY-ADDR5
		classic name for DropshipAddress.StateProvince is SH-STATE-PROV
		classic name for DropshipAddress.PostalCode is SH-POST-CODE
		classic name for DropshipAddress.County is SH-COUNTY
		classic name for DropshipAddress.Country is SH-COUNTRY-CD
	DropshipPhoneNumber				is an TelephoneNumber
		classic name for DropshipPhoneNumber.InternationalPrefix is SH-PHONE-PREF
		classic name for DropshipPhoneNumber.SubscriberNumber is SH-PHONE
		classic name for DropshipPhoneNumber.Extension is SH-PHONE-EXT
	DropshipContact					is an AddressContact 
		holds pii
		classic name is SH-CONTACT
	DropshipName					is a Name 
		holds pii
		classic name is SH-NAME
	ShippingNotes					is Text
	CapitalTotal                    is Decimal size 15.4
	TechnicalTotal                  is Decimal size 15.4
		classic name is TECH-TOTAL
	CapitalTechnicalTotal           is Decimal size 15.4
		classic name is CAP-TECH-TOTAL
	RequisitionRecordSource         is Numeric size 2
		classic name is RECORD-SOURCE
		States
			Rq10			value is 1
				default label is "RQ10"
			ERequisitions	value is 2
				default label is "E-Requisitions"
			Rq500			value is 3
				default label is "RQ500"
			Ic83			value is 4
				default label is "IC83"
			Ic142			value is 5
				default label is "IC142"
	ProcessFlowApprovalLevel
		classic name is PFLOW-APRV-LVL
	RequisitionApprovalType
		classic name is APPROVAL-TYPE
	BaseCurrencyCode				is a Currency
		classic name is BASE-CURR-CODE
		default label is "Currency"
	TransactionCurrencyCode			is a Currency
		classic name is TRAN-CURR-CODE
	PurchaseOrderBillToName			is a Name 
		holds pii
		classic name is POB-NAME
	PurchaseOrderBillToAddress		is a PostalAddressV2 
		holds pii
		classic name for PurchaseOrderBillToAddress.DeliveryAddress.AddressLine1 is POB-ADDR1
		classic name for PurchaseOrderBillToAddress.DeliveryAddress.AddressLine2 is POB-ADDR2
		classic name for PurchaseOrderBillToAddress.DeliveryAddress.AddressLine3 is POB-ADDR3
		classic name for PurchaseOrderBillToAddress.DeliveryAddress.AddressLine4 is POB-ADDR4
		classic name for PurchaseOrderBillToAddress.Municipality is POB-CITY-ADDR5
		classic name for PurchaseOrderBillToAddress.StateProvince is POB-STATE-PROV
		classic name for PurchaseOrderBillToAddress.PostalCode is POB-POSTAL-CD
		classic name for PurchaseOrderBillToAddress.County is POB-COUNTY
		classic name for PurchaseOrderBillToAddress.Country is POB-COUNTRY-CD
	PurchaseOrderBillToContact      is an AddressContact
		classic name is POB-CONTACT
	RequisitionSource               is a RqSource
		classic name is RQ-SOURCE
	OneSourceDocumentToOnePO        is Numeric size 1
		classic name is ONE-SRC-ONE-PO
		States
			NotApplicable value is 0
			No            value is 1
			Yes           value is 2
	ProcurementCardNumber
		classic name is PCARD-NBR
	SourcingEventRequired           is Boolean
		classic name is SS-EVENT-REQD
	PurchaseOrderBillToPhoneNumber  is an TelephoneNumber
		classic name for PurchaseOrderBillToPhoneNumber.InternationalPrefix is POB-PHONE-PREF
		classic name for PurchaseOrderBillToPhoneNumber.SubscriberNumber is POB-PHONE
		classic name for PurchaseOrderBillToPhoneNumber.Extension is POB-PHONE-EXT
	PurchaseOrderBillToEmailAddress is an EmailAddressMulti 
		holds pii
		sql name is POrderBillToEmailAddress
		classic name is POB-EMAIL-ADDR
	LocationRule                    is a WarehouseLocationRule
	ReleasedDate                    is TimeStamp
	ReleasingOperatorID             is an Operator 
		holds pii
		classic name is REL-OPER-ID
	ApprovedRejectedDate            is TimeStamp
		classic name is APR-REJ-DATE
	ApprovingRejectingOperatorID    is an Operator 
		holds pii
		classic name is APR-REJ-OPR-ID
	PurchaseOrderUserField7         is a UserFld7
		classic name is PO-USER-FLD-7
	CreatedBy                       is an Operator 
		holds pii
	LastUpdateDate                  is TimeStamp
		classic name is LAST-UPDT-DATE
	LastUpdateBy                    is an Operator 
		holds pii
	DefaultProcedureInformation 
		restricted
		classic name for DefaultProcedureInformation.DefaultProcedure is DFLT-PROC
		classic name for DefaultProcedureInformation.DefaultProcedureModifier is DFLT-PROC-MOD-
		classic name for DefaultProcedureInformation.DefaultProcedureDate is DFLT-PROC-DATE
		classic name for DefaultProcedureInformation.DefaultPractitioner is DFLT-PHYS-NAME
		classic name for DefaultProcedureInformation.DefaultNurseCoordinator is DFLT-NRS-COORD
		classic name for DefaultProcedureInformation.DefaultSalesRepresentative is DFLT-SALES-REP
		classic name for DefaultProcedureInformation.DefaultSupplierOrderNumber is DFLT-ORD-NBR
		classic name for DefaultProcedureInformation.DefaultRequester is DFLT-REQUESTOR
		classic name for DefaultProcedureInformation.DefaultUserDefined1 is DFLT-USERDEF1
		classic name for DefaultProcedureInformation.DefaultUserDefined2 is DFLT-USERDEF2
	PatientProcedure	
		restricted
		context of FromCompanyLocation.FromCompany
		delete ignored
	DefaultBillCode                 is a ConsignCode
		classic name is DFLT-CN-CODE
	GlobalDocumentType
		classic name is GLBL-DOC-TYPE
	ProcurementTemplate
	BudgetEditProcessing 			is Numeric size 1
		States
			NotInProcessing value is 0
			InProcess  		value is 1
			Failure    		value is 2
			Success    		value is 3
	AOCTotal						is an InternationalAmount
		protected
		disable Auditing
	HeaderAddOnChargeTotal			is an InternationalAmount
		protected
		disable Auditing
	LineAddOnChargeTotal			is an InternationalAmount
		protected
		disable Auditing
	ExtendedAmountTotal				is an InternationalAmount
		protected
		disable Auditing
	CurrencyTable
	RequisitionReleaseMessages 		is Numeric size 1
		States
			NoMessage value is blank
			RequisitionAmountLessThanVendorMinimumOrderAmount value is 1
				default label is "CannotRelease,RequisitionAmountLessThanVendorMinimumOrderAmount"
			RequisitionAmountLessThanPurchFromLocMinimumOrderAmount value is 2
				default label is "CannotRelease,RequisitionAmountLessThanPurchaseFromLocationMinimumOrderAmount"
	POCode
	CommodityCode                   is a CommCodes
	DefaultCommentType						is AlphaUpper size 1
		States
			PrintOnInternalDocuments	value is "I"
			PrintOnPurchaseOrder		value is "P"
			PrintOnPurchaseOrderTrailer	value is "T"
			PrintOnReceivingDocument	value is "R"
			PrintOnPickList				value is "L"
			PrintOnDeliveryTicket		value is "D"
			PrintOnRequisition			value is "Q"
			DisplayOnlyOnInternalDocuments value is "S"
			DisplayOnly					value is "O"
			InvoiceComments				value is "N"
	EAMWorkOrder
	EAMWorkOrderActivity
	TrialNotice                     is a RecallNotice
	AribaPOType
	QuoteNumber                              is Alpha size 30
	Confirming                               is Boolean
	IDMDocumentPID					is an IDMPID
		protected
		restricted
	IDMJob
		protected
		restricted
	ExternalRequisition				is an ExternalReqNbr  
	RequisitionStatusValue			is Numeric size 1
		States
			Closed	   				value is 1
			Unreleased 				value is 2
			Released   				value is 3
			InProcess  				value is 4
			Processed  				value is 5
			Rejected   				value is 6
			ReleaseInProgress		value is 7
			CreateInProgress		value is 8
	Printer 						is an IDMPrinter
	SupplierCreated                 is Numeric size 1
		States
			NotApplicable               value is 0
			Created                     value is 1
			Submitted                   value is 2
			Approved                    value is 3
			Rejected                    value is 4
	ForCapitatedProcedure           is Boolean
	DeliveryMethod
```

This example demonstrates:
- **Classic Name Mapping:** Extensive use of classic names for COBOL compatibility
- **Composite Fields:** Complex types like PostalAddressV2, TelephoneNumber with component-level classic names
- **PII Fields:** Multiple fields marked with `holds pii` for data privacy compliance
- **Protected Fields:** System-calculated fields that cannot be directly modified
- **Restricted Fields:** Fields with access restrictions for security
- **State Fields:** Multiple fields with defined states for workflow management
- **Disable Auditing:** Selective auditing control on calculated totals
- **Delete Rules:** `delete ignored` for related records
- **Context Fields:** `context of` for disambiguating key field context
- **Custom Labels:** Override default labels for better user experience
- **SQL Name Override:** Custom SQL names for database compatibility
- **Boolean Flags:** Simple boolean fields for feature toggles
- **Timestamp Fields:** Tracking creation and modification times
- **Currency Fields:** Multi-currency support with proper typing
- **Decimal Precision:** Explicit size specifications for financial fields
- **Text Fields:** Various text field types (Alpha, Text, Note)
- **Reference Fields:** KeyFields referencing other business classes
- **User-Defined Fields:** Flexible user field definitions
