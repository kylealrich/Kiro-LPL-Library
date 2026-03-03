# Business Class Ontology

The Ontology section defines how a business class is keyed and its relationships to other business classes.

## Symbolic Key

```
symbolic key is <KeyField>
    [name is <FieldName>]
    [classic set name is <Literal>]
    [sql set name is <Literal>]
    [not indexed]
    [<SqlName>]
    [<ClassicName>]
    [<ClassicNameForField>]
    [<SqlNameForField>]
    [protected]
    [name for <context FieldName> is <FieldName>]
    [type for <context FieldName> is a[n] <KeyField>]
```

### Multiple Symbolic Keys

A business class can have multiple symbolic keys; these are synonyms for accessing the same business class.

### Symbolic Key Attributes

| Attribute | Description |
|-----------|-------------|
| `name is <FieldName>` | Overrides the name of the symbolic KeyField on this class |
| `classic set name is <Literal>` | Sets the classic name for the primary set that is generated |
| `sql set name is <Literal>` | Sets the SQL name for the primary set that is generated |
| `not indexed` | Do not create a physical index for the symbolic key |
| `<SqlName>` | SQL name for the field |
| `<ClassicName>` | Classic name for the field |
| `<ClassicNameForField>` | Classic name for a specific field |
| `<SqlNameForField>` | SQL name for a specific field |
| `protected` | Application-controlled field; cannot be updated from UI, webservices, or spreadsheet |

### Name for Context Field

```
[name for <context FieldName> is <FieldName>]
```

`<ContextField>` must be a context field within `<KeyField>`. The context fields within a KeyField automatically get put on the business class. The default name is the highest level type name of the context field.

**Example:** If APCompany is the context field and APCompany extends Company, the name is Company. This phrase allows the field name to be explicitly defined.

### Type for Context Field

```
[type for <context FieldName> is a[n] <KeyField>]
```

This overrides the type of the Context Field. The new type must be a subtype of the original type (e.g., ADCompany versus Company).

### Symbolic Keying

This 'symbolically keys' the business class. A business class can be keyed both symbolically and relatively. A business class that is not symbolically keyed is considered to be 'relatively' keyed.

When a KeyField is defined to be the symbolic key to a business class, the Ontology Context variables are replicated on the business class. When a KeyField is simply used on a business class, the Ontology Context variables must be resolved within the context of the business class or a compile error occurs. The exceptions to this are those Ontology Context variables that are designated as optional.

## Part Of

```
part of <BusinessClass>
    [connect via UniqueID [only]]
    [delete ( cascades | restricted | ignored)]
```

Defines that this business class is part of another business class.

### Connect via UniqueID

```
[connect via UniqueID [only]]
```

BusinessClass must NOT implement the LazyUniqueID pattern to use `connect via UniqueID`.

### Delete Behavior

| Option | Description | Default |
|--------|-------------|---------|
| `cascades` | Child records are deleted when parent is deleted | Yes |
| `restricted` | Deletion is prevented if child records exist | No |
| `ignored` | Deletion of parent ignores child records | No |

## Relative Key

```
relative key is <FieldName> [<DataDefinition>]
    [<States>]
    [<SqlName>]
    [classic set name is <Literal>]
    [sql set name is <Literal>]
    [not indexed]
    [<ClassicName>]
    [<ClassicNameForField>]
    [<SqlNameForField>]
    [<DefaultLabel>]
    [protected]
    [(delete cascades | delete ignored)]
    [[exact] version is (<RelatedValue> | latest)]
    [data area is <RelatedValue>]
    [name for <context FieldName> is <FieldName>]
    [type for <context FieldName> is a[n] <KeyField>]
    [text searchable]
```

Only one relative key is allowed. This keys the business class relative to another business class.

### Relative Key Attributes

| Attribute | Description |
|-----------|-------------|
| `<FieldName>` | Name of the relative key field |
| `<DataDefinition>` | Data type definition |
| `<States>` | State definitions |
| `<SqlName>` | SQL name |
| `classic set name is <Literal>` | Classic name for the primary set |
| `sql set name is <Literal>` | SQL name for the primary set |
| `not indexed` | Do not create a physical index |
| `<ClassicName>` | Classic name |
| `<ClassicNameForField>` | Classic name for field |
| `<SqlNameForField>` | SQL name for field |
| `<DefaultLabel>` | Default label |
| `protected` | Application-controlled field |
| `delete cascades` | Cascade deletes |
| `delete ignored` | Ignore deletes |
| `text searchable` | Enable text search |

### Versioning

```
[[exact] version is (<RelatedValue> | latest)]
```

This directive specifies the particular version of a versioned key field to retrieve.

- If the version field on the key field is of type Date, TimeStamp, or Time, this will default to the current date, current timestamp, or current time
- Otherwise it will default to the latest version
- If there is no exact match on the version, it will return the closest version before the version specified
- If `exact version is` is specified, there must be an exact match on the version
- If `latest` version is specified, it will find the last version even if that is a future version

### Data Area

```
[data area is <RelatedValue>]
```

Valid only when in a 'stored in environment' busclass.

### Name for Context Field

```
[name for <context FieldName> is <FieldName>]
```

`<ContextField>` must be a field in the context field chain of `<BusinessClass>`. The context field chain of `<BusinessClass>` automatically gets put on this business class. The default name is the highest level type name of the context field.

**Example:** If APCompany is the context field and APCompany extends Company, the name is Company. This phrase allows the field name to be explicitly defined.

### Type for Context Field

```
[type for <context FieldName> is a[n] <KeyField>]
```

This overrides the type of the Context Field. See symbolic key definition for full description.

### Relative Key Behavior

The 'relative key' construct will automatically:
- Put the `<BusinessClass>` SymbolicKey field, including its Ontological Context fields, on this business class (unless `connect via UniqueID only` is specified)
- Put a one-to-one or one-to-many relation on `<BusinessClass>` pointing back to this business class
- Name that relation `<this business class>SetRel`
- If `<BusinessClass>` is itself relatively keyed, put a one-to-one required relation on this business class
- Name that relation `<BusinessClass>Rel`

### Accessing Related Data

**The 'parts' of `<BusinessClass>` can be accessed from `<BusinessClass>` by specifying:**
```
<this business class> set
```
in a `<RelatedLink>`

**The `<BusinessClass>` can be accessed from `<this business class>` by specifying:**
```
<BusinessClass>
```
in a `<RelatedLink>`

## Examples

### Simple Symbolic Key

```
COMPANY is a BusinessClass
    owned by GL
    
    Ontology
        symbolic key is COMPANY
    
    Persistent Fields
        COMPANY is Company
        COMPANYNAME is Alpha size 50
```

### Symbolic Key with Custom Name

```
APCOMPANY is a BusinessClass
    owned by AP
    
    Ontology
        symbolic key is APCOMPANY
            name is APCompany
            sql set name is "APCOSET"
            classic set name is "APCOSET"
    
    Persistent Fields
        APCOMPANY is ApCompany
        COMPANYNAME is Alpha size 50
```

### Multiple Symbolic Keys (Synonyms)

```
CUSTOMER is a BusinessClass
    owned by AR
    
    Ontology
        symbolic key is CUSTOMER
        symbolic key is CUSTOMERNUMBER
            name is CustomerNumber
    
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNUMBER is CustomerNumber
        CUSTOMERNAME is Alpha size 50
```

### Part Of Relationship

```
ORDERLINE is a BusinessClass
    owned by Order
    
    Ontology
        part of ORDER
            delete cascades
    
    Persistent Fields
        ORDER is Order
        LINENUM is Integer
        PRODUCT is Product
        QUANTITY is Decimal size 15.2
```

### Part Of with UniqueID

```
ATTACHMENT is a BusinessClass
    owned by Document
    
    Ontology
        part of DOCUMENT
            connect via UniqueID only
            delete cascades
    
    Persistent Fields
        ATTACHMENTID is UniqueID
        FILENAME is Alpha size 255
        FILEDATA is BinaryDocument
```

### Relative Key

```
EMPLOYEEADDRESS is a BusinessClass
    owned by HR
    
    Ontology
        relative key is ADDRESSTYPE is Alpha size 20
            delete cascades
    
    Persistent Fields
        EMPLOYEE is Employee
        ADDRESSTYPE is Alpha size 20
        STREET is Alpha size 50
        CITY is Alpha size 30
        STATE is Alpha size 2
        ZIP is Alpha size 10
```

### Relative Key with Versioning

```
CONTRACTTERM is a BusinessClass
    owned by Contract
    
    Ontology
        relative key is TERMNUM is Integer
            version is latest
            delete cascades
    
    Persistent Fields
        CONTRACT is Contract
        TERMNUM is Integer
        TERMTEXT is Text
        EFFECTIVEDATE is Date
```

### Symbolic Key with Context Field Overrides

```
APINVOICE is a BusinessClass
    owned by AP
    
    Ontology
        symbolic key is APINVOICE
            name for COMPANY is APCompany
            type for COMPANY is an APCOMPANY
    
    Persistent Fields
        APINVOICE is ApInvoice
        APCOMPANY is ApCompany
        INVOICEAMOUNT is Decimal size 15.2
```

### Protected Symbolic Key

```
SYSTEMCONFIG is a BusinessClass
    owned by System
    
    Ontology
        symbolic key is CONFIGKEY
            protected
            not indexed
    
    Persistent Fields
        CONFIGKEY is Alpha size 50
        CONFIGVALUE is Alpha size 255
```

### Relative Key with Text Search

```
PRODUCTDESCRIPTION is a BusinessClass
    owned by Inventory
    
    Ontology
        relative key is LANGUAGE is Alpha size 5
            text searchable
            delete cascades
    
    Persistent Fields
        PRODUCT is Product
        LANGUAGE is Alpha size 5
        DESCRIPTION is Alpha size 255
            text searchable
```

## Best Practices

### Symbolic Keys

- Use symbolic keys for independent business entities
- Provide meaningful key field names
- Consider using multiple symbolic keys for alternative identifiers
- Use `protected` for system-managed keys
- Provide SQL and classic names for legacy compatibility

### Part Of Relationships

- Use `part of` for true composition relationships
- Set appropriate delete behavior (usually cascades)
- Use `connect via UniqueID` for loose coupling
- Document the parent-child relationship
- Consider performance implications of cascading deletes

### Relative Keys

- Use relative keys for dependent entities
- Provide meaningful relative key field names
- Set appropriate delete behavior
- Use versioning when historical data is needed
- Consider indexing for performance

### Context Field Overrides

- Override context field names to avoid conflicts
- Override context field types for specialization
- Document why overrides are necessary
- Keep overrides consistent across related classes

### Indexing

- Create indexes by default for performance
- Use `not indexed` only when justified
- Document why indexes are disabled
- Monitor query performance

### Delete Behavior

- Use `delete cascades` for true composition
- Use `delete restricted` to prevent orphans
- Use `delete ignored` rarely and with caution
- Document delete behavior decisions
- Test delete scenarios thoroughly

### Versioning

- Use `latest` for current data access
- Use `exact version` for historical accuracy
- Document versioning strategy
- Consider query performance implications

## Common Patterns

### Master-Detail Pattern

```
// Master
ORDER is a BusinessClass
    Ontology
        symbolic key is ORDER
    
    Persistent Fields
        ORDER is Order
        ORDERDATE is Date

// Detail
ORDERLINE is a BusinessClass
    Ontology
        part of ORDER
            delete cascades
        relative key is LINENUM is Integer
    
    Persistent Fields
        ORDER is Order
        LINENUM is Integer
        PRODUCT is Product
```

### Specialization Pattern

```
// Base
EMPLOYEE is a BusinessClass
    Ontology
        symbolic key is EMPLOYEE
    
    Persistent Fields
        EMPLOYEE is Employee
        EMPLOYEENAME is Alpha size 50

// Specialized
MANAGER is a BusinessClass
    Ontology
        symbolic key is MANAGER
            type for EMPLOYEE is a MANAGER
    
    Persistent Fields
        MANAGER is Manager
        DEPARTMENTCOUNT is Integer
```

### Versioned Entity Pattern

```
PRICELISTITEM is a BusinessClass
    Ontology
        part of PRICELIST
            delete cascades
        relative key is PRODUCT is Product
            version is latest
    
    Persistent Fields
        PRICELIST is PriceList
        PRODUCT is Product
        PRICE is Decimal size 15.2
        EFFECTIVEDATE is Date
```

## Related Concepts

### Symbolic vs Relative Keying

- **Symbolic Key**: Independent entity with its own identity
- **Relative Key**: Dependent entity, keyed relative to another entity
- A business class can have both symbolic and relative keys

### Part Of vs Relative Key

- **Part Of**: Composition relationship, typically with cascade delete
- **Relative Key**: Defines how the entity is keyed within its parent
- Often used together for dependent entities

### Context Field Propagation

- Context fields from symbolic keys are automatically propagated
- Context fields from relative keys are also propagated
- Overrides allow customization of propagated fields
