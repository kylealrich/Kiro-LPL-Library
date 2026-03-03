# Business Class Definition

Business Class files have an extension of `.busclass`

## Business Class Structure

```
<BusinessClass> is a BusinessClass
    [deprecated]
    [owned by <ModuleName>]
    [<ClassicPrefix>]
    [<SqlName>]
    [<RpgName>]
    [<ClassicName>]
    [<DefaultLabel>]
    [framework type is ( ConfigParam
                       | LPLConfiguration
                       | ProcessFlow
                       | ReplicationSet
                       | Security)]
    [untranslatable]
    [subject is <Subject>]
    [stored in [base] environment]
    [stored in product line]
    [store using <ClassName>]
    [contains environment data]
    [disable data area copy]
    [preserve target data]
    [representative text is <Message>]
    [representative image is (<RelatedValue> | <Icon>)]
    [display as (portrait | photo | full) image]
    [missing image is (<ImageName> | random background)]
    [foreground text is <Message>]
    [(restrict | enable) actions on lists]
    [default Alpha filter operator is (contains | starts with | equals)]
    
    Suppress Warnings
    Ontology
    Patterns
    DataSource Mapping
    Persistent Fields
    Transient Fields
    Dimensions
    Measures
    Cube Links
    Context Fields
    Field Groups
    Audit Index Fields
    Local Fields
    Derived Fields
    Conditions
    Text Search Fields
    Cube Relations
    Columnar Relations
    Relations
    Form Invokes
    Matrix Forms
    Sets
    Field Rules
    SubType <RelatedCondition> Field Rules
    Commit Rules
    Audit Entry Rules
    Create Rules
    Create Exit Rules
    Update Action Rules
    Delete Rules
    Attach Rules
    Parent Attach Rules
    Dynamic Creation Rules
    Rule Blocks
    StateCycles
    Actions
```

## Business Class Attributes

### Deprecated

```
[deprecated]
```

Marks the business class as deprecated, indicating it should not be used in new development.

### Ownership

```
[owned by <ModuleName>]
```

If not defined, this defaults to the module it is under.

### Naming Conventions

| Attribute | Description | Required For |
|-----------|-------------|--------------|
| `<ClassicPrefix>` | Classic prefix for the business class | Use in Cobol programs |
| `<SqlName>` | SQL name for database mapping | Database operations |
| `<RpgName>` | RPG name for legacy systems | RPG integration |
| `<ClassicName>` | Classic name for legacy compatibility | Legacy system integration |
| `<DefaultLabel>` | Default display label | UI display |

### Framework Type

```
[framework type is ( ConfigParam
                   | LPLConfiguration
                   | ProcessFlow
                   | ReplicationSet
                   | Security)]
```

Specifies the framework type for special-purpose business classes.

| Framework Type | Purpose |
|----------------|---------|
| `ConfigParam` | Configuration parameter business class |
| `LPLConfiguration` | LPL configuration business class |
| `ProcessFlow` | Process flow business class |
| `ReplicationSet` | Replication set business class |
| `Security` | Security-related business class |

### Translation

```
[untranslatable]
```

Prevents any text or data from being translated.

### Subject

```
[subject is <Subject>]
```

Default subject for all actions on this business class.

## Storage Options

### Stored in Environment

```
[stored in [base] environment]
```

Store data in the environment database. IJF framework only.

### Stored in Product Line

```
[stored in product line]
```

Store data in the environment product line database (vs standard data area DB).

### Store Using

```
[store using <ClassName>]
```

ClassName that implements persistence. IJF framework only.

### Contains Environment Data

```
[contains environment data]
```

Environment data is not copied in daexport. IJF framework only.

### Disable Data Area Copy

```
[disable data area copy]
```

Prevent data from being copied in daexport. IJF framework only.

### Preserve Target Data

```
[preserve target data]
```

Default is to delete target data. This preserves it instead.

## Display and Representation

### Representative Text

```
[representative text is <Message>]
```

Text that is the human readable form of the key to this business class. This replaces Display Fields.

**Example:**
```
representative text is "<Company> - <CompanyName>"
```

### Representative Image

```
[representative image is (<RelatedValue> | <Icon>)]
```

Image that represents an instance of this business class.

### Display Image Options

```
[display as (portrait | photo | full) image]
```

Default is `display as portrait`.

| Option | Description |
|--------|-------------|
| `portrait` | Display as portrait-oriented image |
| `photo` | Display as photo |
| `full` | Display as full-size image |

### Missing Image

```
[missing image is (<ImageName> | random background)]
```

Specifies what to display when an image is missing.

### Foreground Text

```
[foreground text is <Message>]
```

Text to display in the foreground of the image or representation.

## List and Filter Options

### Actions on Lists

```
[(restrict | enable) actions on lists]
```

Default is `enable`. Controls whether actions can be performed on list views.

### Default Alpha Filter Operator

```
[default Alpha filter operator is (contains | starts with | equals)]
```

Default is `contains`. Sets the default filter behavior for Alpha fields.

| Operator | Behavior |
|----------|----------|
| `contains` | Filter matches if field contains the search value |
| `starts with` | Filter matches if field starts with the search value |
| `equals` | Filter matches if field equals the search value exactly |

## Business Class Sections

The following sections define the structure and behavior of the business class:

### Suppress Warnings
Suppresses specific compiler warnings.

### Ontology
Defines the ontological structure (stereotype, business class, context).

### Patterns
Implements design patterns (Versioning, Specialization, Hierarchy, etc.).

### DataSource Mapping
Maps the business class to external data sources.

### Persistent Fields
Fields that are stored in the database.

### Transient Fields
Fields that exist only in memory during processing.

### Dimensions
Dimensional fields for analytical purposes.

### Measures
Measure fields for analytical purposes.

### Cube Links
Links to analytical cubes.

### Context Fields
Non-ontological context variables.

### Field Groups
Logical groupings of fields.

### Audit Index Fields
Fields used for audit indexing.

### Local Fields
Fields local to the business class (not persisted).

### Derived Fields
Fields whose values are calculated from other fields.

### Conditions
Named conditions for business logic.

### Text Search Fields
Fields enabled for full-text search.

### Cube Relations
Relations to cube structures.

### Columnar Relations
Relations to columnar data structures.

### Relations
Relationships to other business classes.

### Form Invokes
Form invocation definitions.

### Matrix Forms
Matrix form definitions.

### Sets
Named sets of records.

### Field Rules
Rules that execute on field changes.

### SubType Field Rules
Rules specific to subtypes based on conditions.

### Commit Rules
Rules that execute on commit.

### Audit Entry Rules
Rules for audit log entries.

### Create Rules
Rules that execute on record creation.

### Create Exit Rules
Rules that execute after record creation.

### Update Action Rules
Rules that execute on update actions.

### Delete Rules
Rules that execute on record deletion.

### Attach Rules
Rules for attaching related records.

### Parent Attach Rules
Rules for parent-child attachment.

### Dynamic Creation Rules
Rules for dynamic record creation.

### Rule Blocks
Reusable blocks of rules.

### StateCycles
State machine definitions.

### Actions
Action definitions for the business class.

## Examples

### Simple Business Class

```
COMPANY is a BusinessClass
    owned by GL
    sql name is COMPANY
    classic name is "COMPANY"
    default label is "Company"
    
    representative text is "<Company> - <CompanyName>"
    
    Persistent Fields
        COMPANY is Company
        COMPANYNAME is Alpha size 50
            text searchable
```

### Business Class with Framework Type

```
SECURITYCLASS is a BusinessClass
    owned by Security
    framework type is Security
    sql name is SECCLASS
    default label is "Security Class"
    
    Persistent Fields
        SECURITYCLASS is SecurityClass
        DESCRIPTION is Alpha size 100
```

### Business Class with Storage Options

```
SYSTEMCONFIG is a BusinessClass
    owned by System
    stored in environment
    contains environment data
    disable data area copy
    
    Persistent Fields
        CONFIGKEY is Alpha size 50
        CONFIGVALUE is Alpha size 255
```

### Business Class with Representative Image

```
EMPLOYEE is a BusinessClass
    owned by HR
    representative text is "<Employee> - <EmployeeName>"
    representative image is EmployeePhoto
    display as portrait image
    missing image is DefaultEmployeeIcon
    
    Persistent Fields
        EMPLOYEE is Employee
        EMPLOYEENAME is Alpha size 50
        EMPLOYEEPHOTO is BinaryDocument
```

### Business Class with List Options

```
PRODUCT is a BusinessClass
    owned by Inventory
    enable actions on lists
    default Alpha filter operator is starts with
    
    representative text is "<Product> - <ProductDescription>"
    
    Persistent Fields
        PRODUCT is Product
        PRODUCTDESCRIPTION is Alpha size 100
            text searchable
```

### Example: PurchaseOrder - Enterprise Business Class

From the PurchaseOrder business class, demonstrating a complex enterprise-level business class:

```
PurchaseOrder is a BusinessClass
    owned by po
    prefix is PCR
    classic name is PURCHORDER

    Ontology
        symbolic key is PurchaseOrder
            classic set name is PCRSET0

    Patterns
        implements ContextualParent
        implements StaticJava
        implements BODId
        disable AuditIndex
        implements AnalyticCube
            disable continuous update
            dynamically calculate totals
            write to base level only
        implements ForceUIRefreshOnStale
        implements Archivable

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
        ShipToLocation is an InventoryLocation
            classic name is LOCATION
        TotalOrderAmount is an InternationalAmount 
            classic name is TOT-ORDER-AMT
            disable Auditing
            protected
        Released is Boolean
            classic name is RELEASED-FL
            protected
        Closed is AlphaUpper size 1
            classic name is CLOSED-FL
            protected
            States
                Yes         value is "Y"
                No          value is "N"
                Historical  value is "H"
```

This example demonstrates:
- Use of classic prefix for legacy system integration
- Symbolic key definition in Ontology section
- Multiple pattern implementations for enterprise features
- Protected fields that cannot be directly modified
- State fields with defined values
- Auditing control with `disable Auditing`
- Complex field types (InternationalAmount, VendorLocation)
- Classic name mappings for COBOL compatibility
- PII (Personally Identifiable Information) handling
    Persistent Fields
        PRODUCT is Product
        PRODUCTDESCRIPTION is Alpha size 100
            text searchable
```

### Deprecated Business Class

```
OLDCUSTOMER is a BusinessClass
    deprecated
    owned by AR
    sql name is OLDCUST
    
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNAME is Alpha size 50
```

## Best Practices

### Naming

- Use descriptive business class names
- Follow consistent naming conventions
- Provide SQL names for database mapping
- Provide classic names for legacy compatibility

### Ownership

- Assign business classes to appropriate modules
- Keep related business classes in the same module
- Document cross-module dependencies

### Framework Types

- Use framework types only for special-purpose classes
- Document the purpose of framework-typed classes
- Follow framework-specific guidelines

### Storage

- Use environment storage for configuration data
- Use product line storage for shared data
- Disable data area copy for environment-specific data
- Document storage decisions

### Representative Text

- Provide meaningful representative text
- Include key identifying information
- Keep representative text concise
- Test representative text with various data

### Images

- Provide appropriate representative images
- Handle missing images gracefully
- Choose appropriate display modes
- Consider performance with large images

### List Behavior

- Enable actions on lists by default
- Restrict actions only when necessary
- Choose appropriate filter operators for user experience
- Test filter behavior with various data

### Translation

- Mark untranslatable only when necessary
- Document why classes are untranslatable
- Consider multi-language requirements

### Deprecation

- Mark deprecated classes clearly
- Provide migration path documentation
- Plan for eventual removal
- Communicate deprecation to users

### Structure

- Organize sections logically
- Keep related definitions together
- Document complex structures
- Follow consistent patterns across business classes


### Example: Requisition - Complex Enterprise Business Class

From the Requisition business class, demonstrating a comprehensive enterprise-level business class with extensive features:

```
Requisition is a BusinessClass
	owned by rq
	prefix is RQH
	classic name is REQHEADER

	Ontology
		symbolic key is Requisition
			classic set name is RQHSET1
			classic name is REQ-NUMBER

	Patterns
		implements StaticJava
		disable AuditIndex
		implements AnalyticCube
			disable continuous update
			dynamically calculate totals
			write to base level only
		implements BODId
		enable explicit context override

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
		Vendor
		PurchaseFromLocation			is a VendorLocation
			classic name is PURCH-FR-LOC
		Buyer
			classic name is BUYER-CODE
		VendorName
		Dropship						is Boolean
			classic name is DROPSHIP-FL
			default label is "ReceivingOptions"
		DropshipAddress					is a PostalAddressV2 
			holds pii
			classic name for DropshipAddress.DeliveryAddress.AddressLine1 is SH-ADDR1
			classic name for DropshipAddress.DeliveryAddress.AddressLine2 is SH-ADDR2
			classic name for DropshipAddress.Municipality is SH-CITY-ADDR5
			classic name for DropshipAddress.StateProvince is SH-STATE-PROV
			classic name for DropshipAddress.PostalCode is SH-POST-CODE
			classic name for DropshipAddress.Country is SH-COUNTRY-CD
		OperatorID						is an Operator 
			holds pii
		BaseCurrencyCode				is a Currency
			classic name is BASE-CURR-CODE
			default label is "Currency"
		CreatedBy                       is an Operator 
			holds pii
		LastUpdateDate                  is TimeStamp
			classic name is LAST-UPDT-DATE
		LastUpdateBy                    is an Operator 
			holds pii
		IDMDocumentPID					is an IDMPID
			protected
			restricted
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
```

This example demonstrates:
- **Classic Integration:** Prefix (RQH) and classic names for COBOL compatibility
- **Ontology:** Symbolic key with classic set name for legacy system integration
- **Multiple Patterns:** StaticJava, AnalyticCube with options, BODId, explicit context override
- **Complex Field Types:** Composite types (PostalAddressV2), custom types (RQHeaderDefaultCodeBlock)
- **PII Handling:** Fields marked with `holds pii` for data privacy compliance
- **State Management:** Status field with multiple defined states for workflow
- **Protected Fields:** Fields that cannot be directly modified by users
- **Restricted Fields:** Fields with access restrictions
- **Classic Name Mapping:** Individual field mappings and composite field component mappings
- **Custom Labels:** Override default labels for better UX
- **Auditing Control:** Selective auditing with `disable Auditing` on specific fields
- **Boolean Flags:** Simple boolean fields for feature toggles
- **Timestamp Tracking:** Creation and update tracking with timestamps and operators
- **Currency Support:** Multi-currency fields with proper typing
- **Document Management:** Integration with IDM (Infor Document Management)

The Requisition business class is a comprehensive example of an enterprise-level business class that handles:
- Complex approval workflows
- Multi-company transactions
- Budget management and commitments
- Supplier integration
- State-based lifecycle management
- Extensive audit trails
- Integration with external systems (BOD, IDM)
- PII compliance
- Legacy system compatibility
