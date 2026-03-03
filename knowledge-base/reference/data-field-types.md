# Data and Field Types

## Field Definitions

```
DimensionField ::= <dimension RelatedField>
// this includes isAggregatable attributes

FieldSize ::= <numeric characters>

KeyField ::= <keyfield FullFieldName>

Literal ::= <any character except whitespace or angle brackets>

NbrDecimals ::= <numeric characters>

Number ::= [-]<numeric characters>[.<numeric characters>]

# ::= <numeric characters>

Percent ::= <Number>%

ReportText ::= (<alphanumeric characters> | ':')

Text ::= '"'<alphanumeric characters>'"'

ViewField ::= <view FullFieldName>
```

## Data Definition

```
DataDefinition ::= ( is a[n] <FieldName>
                   | is like <FieldName>
                     // must be a simple field; this syntax determines only type and size
                   | is a[n] <BusinessClass> group [in subject <Subject>]
                     // Subjects defined in Product Line Definition
                   | is a[n] <BusinessClass> compute [in subject <Subject>][measures only]
                   | is <TypeDataDefinition>)
                     // needs to have a standard set of 'system' fields: product line, module, and so on
```

## Primitive Types

```
PrimitiveType ::= ( Alpha
                  | AlphaRight
                  | AlphaUpper
                  | Anniversary // cannot translate
                  | BinaryDocument
                  | BinaryObject
                  | Boolean // cannot translate
                  | BusinessObjectReference [to <BusinessClass>] // cannot translate
                  | CSVText // cannot translate
                  | Date // cannot translate
                  | [Unsigned] Decimal // cannot translate
                  | DocumentTitle
                    // Alpha field with special characteristic: when in the context of a 
                    // BinaryDocument, it is populated when the BinaryDocument is populated. 
                    // When user selects a file on disk into a BinaryDocument field, we search 
                    // for the closest DocumentTitle and MimeType and fill them out.
                  | EmailAddressField [with multiple addresses]
                    // Deprecated: "with multiple addresses" due to size limitation 
                    // – use MultiEmailAddressField
                  | MultiEmailAddressField
                  | GroupField // cannot translate
                  | Integer // cannot translate
                  | Iteration of <BusinessClass> // cannot translate
                  | JSONObject // cannot translate
                  | MimeType // cannot translate
                  | Numeric // cannot translate
                  | Password // cannot translate
                  | [Unsigned] Percent // cannot translate
                  | Period // cannot translate
                  | RichText
                  | Signed // valid only in a Report Definition
                  | Text
                  | TextDocument // cannot translate
                  | Time // cannot translate
                  | TimeStamp // cannot translate
                  | UniqueID // cannot translate
                  | URI
                  | URL
                  | XMLDocument // cannot translate
                  | Year // cannot translate
                  )
```

## Type Data Definition

```
TypeDataDefinition ::= [us-ascii]<PrimitiveType> [[size (fixed | up to)] <FieldSize>[.<NbrDecimals>]]
```

### Size Modifiers

- **up to**: Indicates a variable size field (e.g., varchar)
  - On LOB types, restricts the size of the LOB
  - Useful in some databases to reduce the base record size

- **fixed**: The default size modifier
  - A warning is generated on any non "up to" type that is 30 spaces or more unless "fixed" is designated
  - Valid on `Alpha` and `AlphaUpper` only

### Field Size Notes

- `FieldSize` is the overall size of the field
- For Decimal fields: A size of `12.2` means 12 total digits, of which 2 are the number of digits after the decimal point

## Translation Support

The following types **cannot be translated**:
- Anniversary
- Boolean
- BusinessObjectReference
- CSVText
- Date
- Decimal (signed and unsigned)
- GroupField
- Integer
- Iteration
- JSONObject
- MimeType
- Numeric
- Password
- Percent (signed and unsigned)
- Period
- TextDocument
- Time
- TimeStamp
- UniqueID
- XMLDocument
- Year

## Special Field Types

### DocumentTitle
An Alpha field with special behavior:
- When in the context of a BinaryDocument, it is automatically populated when the BinaryDocument is populated
- When a user selects a file on disk into a BinaryDocument field, the system searches for the closest DocumentTitle and MimeType fields and fills them out

### EmailAddressField
- Basic syntax: `EmailAddressField`
- Deprecated syntax: `EmailAddressField with multiple addresses` (due to size limitation)
- Use `MultiEmailAddressField` instead for multiple addresses

### Signed Type
Valid only in a Report Definition
