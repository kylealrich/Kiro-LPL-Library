# Representation

## Field Representation

```
[<FieldRepresentation>]
```

A KeyField might not have a representation. This is useful when the KeyField is the symbolic key of a business class that implements the Specialization pattern. The field can still be put as a Persistent field in a business class to dynamically reference the appropriate related Specialization.

## Field Representation Types

```
FieldRepresentation ::= ( <SimpleField>
                        | <GroupField>
                        | <ArrayField>)
```

## Simple Field

```
SimpleField ::= type <DataDefinition>
                [precision is <RelatedValue>]
                [round to precision]
                [<States>]
```

### Simple Field Notes

`<States>` is not indented under type because indentation implies some form of ownership or detail declaration. In this case, `<States>` describes the Field's definition and not the Representation's definition.

### Simple Field Attributes

| Attribute | Description |
|-----------|-------------|
| `type <DataDefinition>` | Defines the data type of the field |
| `precision is <RelatedValue>` | Sets the precision for numeric fields |
| `round to precision` | Rounds values to the specified precision |
| `<States>` | Defines states for the field |

## Group Field

```
GroupField ::= Group Fields
               <FieldName> [<DataDefinition>]
                   [<States>]
                   [<SqlName>]
                   [<ClassicName>]
                   [<ClassicNameForField>]
                   [<SqlNameForField>]
                   [<DefaultLabel>]
                   [text searchable]
                   [scannable]
                   [encrypt]
                   [holds pii]
                   [enable alternate document location]
                   [document is <RelatedField>]
                   [existence is <RelatedField>]
                   [disable Auditing [when in background]]
                   [disable EffectiveDated]
                   [enable EffectiveDated]
                   [automate context]
                   [disable surrogates]
                   [allow images]
                   [(delete cascades | delete ignored)]
                   [translatable]
                   [restricted]
                   [protected]
                   [precision is <RelatedValue>]
                   [round to precision]
                   [primitive type is <RelatedValue>]
                   [primitive size is <RelatedValue>]
                   [primitive decimal size is <RelatedValue>]
                   [create value is blank if no entry]
                   [as of <RelatedValue>]
                   [within <RelatedValue>]
                   [<Message>]
                   [[exact] version is (<RelatedValue> | latest)]
                   [data area is <RelatedValue>]
                   [is (condition | related (link | value)) for <RelatedValue>]
                   [(dimensions [with attributes] | measures) only]
                   [<TextVariables>]
                   [document template [for <BusinessClass>]]
                   [store as BusinessObjectReference]
                   [<SqlPrefix>]
```

## Group Field Attributes

### Naming and Display

| Attribute | Description |
|-----------|-------------|
| `<SqlName>` | SQL name for the field |
| `<ClassicName>` | Classic name for the field |
| `<ClassicNameForField>` | Classic name for a specific field |
| `<SqlNameForField>` | SQL name for a specific field |
| `<DefaultLabel>` | Default display label |
| `<SqlPrefix>` | SQL prefix for group fields |

### Search and Scan

| Attribute | Description |
|-----------|-------------|
| `text searchable` | Enables text search on the field |
| `scannable` | Enables scanning (e.g., barcode) |

### Security and Privacy

| Attribute | Description |
|-----------|-------------|
| `encrypt` | Encrypts the field value |
| `holds pii` | Marks field as containing Personally Identifiable Information |
| `restricted` | Cannot be used as field on UI |
| `protected` | Application-controlled field; cannot be updated from UI, webservices, or spreadsheet |

### Document Fields

| Attribute | Valid For | Description |
|-----------|-----------|-------------|
| `enable alternate document location` | BinaryDocument, BinaryObject, CSVText, JSONObject, RichText, Text, TextDocument, XMLDocument | Enables alternate storage location |
| `document is <RelatedField>` | Document fields | References the document field |
| `allow images` | RichText | Allows images in rich text |
| `document template [for <BusinessClass>]` | Document fields | Specifies document template |

**Note:** If within a Group, just `document template` can be specified generically. This will require that wherever this group field is used, a specific document template for BusClass is specified.

### Field Behavior

| Attribute | Description |
|-----------|-------------|
| `existence is <RelatedField>` | Must be a Boolean type field that controls existence |
| `disable Auditing [when in background]` | Field will not be audited (optionally only when in background) |
| `disable EffectiveDated` | Field will not participate in EffectiveDated pattern |
| `enable EffectiveDated` | Field will participate in EffectiveDated pattern |
| `automate context` | Automates context handling |
| `disable surrogates` | Disables surrogate key handling |
| `delete cascades` | Cascades deletes to related records |
| `delete ignored` | Ignores deletes |

### Translation and Localization

| Attribute | Description |
|-----------|-------------|
| `translatable` | Allows for data translation for this field |

### Precision and Type

| Attribute | Description |
|-----------|-------------|
| `precision is <RelatedValue>` | Sets numeric precision |
| `round to precision` | Rounds to specified precision |
| `primitive type is <RelatedValue>` | Must be of type 'PrimitiveType'; cannot be Text or BinaryDocument/Image |
| `primitive size is <RelatedValue>` | Sets primitive type size |
| `primitive decimal size is <RelatedValue>` | Sets decimal places for primitive type |

### Audit and History

| Attribute | Description |
|-----------|-------------|
| `create value is blank if no entry` | If a field is added after a record is created, typically the first value in the audit log applies as the value from the point of create. With this pattern, the value from create will be considered blank |
| `as of <RelatedValue>` | Specifies as-of date for historical data |
| `within <RelatedValue>` | Specifies time range |

### Versioning and Data Area

| Attribute | Description |
|-----------|-------------|
| `[exact] version is (<RelatedValue> \| latest)` | Specifies version |
| `data area is <RelatedValue>` | Valid only when stored in an environment busclass |

### Relations and Dimensions

| Attribute | Description |
|-----------|-------------|
| `is (condition \| related (link \| value)) for <RelatedValue>` | Defines relationship type; RelatedValue must be a BusinessClass name |
| `(dimensions [with attributes] \| measures) only` | Restricts to dimensions or measures only |

### Storage

| Attribute | Description |
|-----------|-------------|
| `store as BusinessObjectReference` | Stores the field as a business object reference |

### Other

| Attribute | Description |
|-----------|-------------|
| `<States>` | Defines states for the field |
| `<Message>` | Associated message |
| `<TextVariables>` | Text variable definitions |

## Array Field

```
ArrayField ::= <FieldName> [<DataDefinition>]
               occurs (<Literal> | sizeofarray <ArrayField> | unlimited) times
```

ArrayField is valid on both keyfield and fields.

### Array Occurrence Options

| Option | Description |
|--------|-------------|
| `<Literal>` | Fixed number of occurrences (e.g., `occurs 10 times`) |
| `sizeofarray <ArrayField>` | Size matches another array field |
| `unlimited` | Unlimited occurrences (valid for local fields only) |

### Array Field Examples

#### Fixed Size Array
```
PHONENUMBERS is a Field
    type is Alpha size 20
    occurs 5 times
```

#### Dynamic Size Array
```
LINEDETAILS is a Field
    type is LineDetail group
    occurs sizeofarray LINEITEMS times
```

#### Unlimited Array (Local Fields)
```
TEMPVALUES is a Field
    type is Decimal size 15.2
    occurs unlimited times
```

## Field Representation Examples

### Simple Field Example

```
AMOUNT is a Field
    type is Decimal size 15.2
    precision is 2
    round to precision
```

### Group Field Example

```
ADDRESS is a Field
    Group Fields
        STREET is Alpha size 50
            text searchable
            default label is "Street Address"
            
        CITY is Alpha size 30
            text searchable
            default label is "City"
            
        STATE is Alpha size 2
            default label is "State"
            
        ZIPCODE is Alpha size 10
            scannable
            default label is "ZIP Code"
```

### Array Field Example

```
CONTACTINFO is a Field
    Group Fields
        CONTACTTYPE is Alpha size 20
        CONTACTVALUE is Alpha size 100
            text searchable
    occurs 10 times
```

### Document Field with Alternate Location

```
ATTACHMENT is a Field
    type is BinaryDocument
    enable alternate document location
    document is ATTACHMENTDATA
    holds pii
```

### Protected Field Example

```
SYSTEMCALCULATION is a Field
    type is Decimal size 15.2
    protected
    disable Auditing
```

### Translatable Field Example

```
DESCRIPTION is a Field
    type is Alpha size 255
    translatable
    text searchable
```

## Best Practices

### Simple Fields
- Use appropriate precision for numeric fields
- Define states for fields with limited valid values
- Round to precision when exact decimal values are needed

### Group Fields
- Group related fields together logically
- Use SQL prefixes to avoid naming conflicts
- Mark PII fields appropriately
- Use protected for system-calculated fields
- Enable auditing selectively for performance

### Array Fields
- Use fixed sizes when the maximum is known
- Use unlimited only for local/temporary fields
- Consider performance implications of large arrays
- Use sizeofarray to keep related arrays synchronized

### Document Fields
- Enable alternate document location for large documents
- Use appropriate document types (Binary, Text, XML, JSON)
- Consider encryption for sensitive documents
- Mark document fields that hold PII

### Security
- Use `encrypt` for sensitive data
- Mark `holds pii` for compliance
- Use `protected` to prevent unauthorized updates
- Use `restricted` to hide fields from UI

### Performance
- Disable auditing for frequently updated fields when appropriate
- Use `disable EffectiveDated` for fields that don't need history
- Consider storage implications of translatable fields
- Use alternate document locations for large binary data
