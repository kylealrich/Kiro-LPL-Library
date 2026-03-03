# Field Definition

## Field Files

Field files have an extension of `.field` or `.keyfield`

## Field Structure

```
<FieldName> is a (Field | KeyField)
    [owned by <ModuleName>]
    [(<SqlName> | <SqlPrefix>)]
    [<ClassicName>]
    [<DefaultLabel>]
    [text searchable]
    [scannable]
    [holds pii]
    [default filter operator is (contains | starts with | equals)]
    [extends <FieldName>]
    [member of <extended FieldName> peer group]
    [(delete cascades | delete ignored)]
    [name is <Literal>]
    
    RepresentationOntologyPatterns
    Display Fields
    Surrogate For
    Context Fields
    Transient Fields
    Local Fields
    Derived Fields
    Conditions
    Cube Relations
    Columnar Relations
    Relations
    Rule Blocks
    Field Rules
```

## Field Properties

### Ownership

```
owned by <ModuleName>
```

- If not defined, the field is global and put in the 'field' package
- Owned fields belong to a specific module

### SQL Naming

```
<SqlName> | <SqlPrefix>
```

- **SqlPrefix**: For Group Field only
- **SqlName**: Not valid on a Group Field
- Every duplicate group field type instantiation in a Business Class or GroupField requires a `prefix is` predicate
- This lets us add the prefix to all the group field names

**Note:** Currently, the kludge for the prefix on a GroupField instantiation in a Business Class is `sql prefix is XXX`

### Classic Naming

```
<ClassicName>
```

Defines the classic name for the field.

### Default Label

```
<DefaultLabel>
```

Defines the default display label for the field.

### Field Attributes

#### Text Searchable
```
text searchable
```
Marks the field as searchable in text searches.

#### Scannable
```
scannable
```
Marks the field as scannable (e.g., for barcode scanning).

#### Holds PII
```
holds pii
```
Indicates the field contains Personally Identifiable Information.

#### Default Filter Operator
```
default filter operator is (contains | starts with | equals)
```

Valid only on a SimpleField (not a GroupField or ArrayField) and alpha field types.

Options:
- **contains**: Filter matches if field contains the search value
- **starts with**: Filter matches if field starts with the search value
- **equals**: Filter matches if field equals the search value exactly

## Field Extension

### Extends

```
extends <FieldName>
```

Allows a field to extend another field. If a field extends another field, it cannot specify a new representation.

Both KeyFields and Fields can be extended:

#### Extended KeyField
Typically specifies an additional business class that contains the particular instances of the new KeyField, although this is not required.

**Example:** Company might be extended as a new field called APCompany with a business class of ApCompany. If the business class for Company is Glsystem, each instance of an ApCompany must also be an instance of a Glsystem, whereas each instance of a Glsystem might not be an instance of an ApCompany.

**Example without new business class:** PersonnelCode has a Context variable called PCodeType and it might be extended as a new field called LocationCode with PCodeType set to PCodeType.Location (one of its states).

#### Extended Field
Typically sets some context variable of the base Field.

**Example:** CurrencyValue has a context variable called IsRate and might be extended as a new field called CurrencyAmount with IsRate set to false.

### Peer Group

```
member of <extended FieldName> peer group
```

- `extended FieldName` must be a super key field of this key field – that is, this key field must extend it
- A 'peer group' of KeyFields is a set of KeyFields that are all surrogates of each other where the surrogate values are all identical

**Example:** If ICCompany extends GLCompany and POCompany extends GLCompany, and if ICCompany and POCompany are members of the GLCompany peer group, then ICCompany acts as a surrogate for POCompany, where the POCompany value is equal to the ICCompany value.

## Delete Behavior

```
(delete cascades | delete ignored)
```

Valid on a KeyField with a business class only. Defaults to delete restricted.

### Delete Options

| Option | Description |
|--------|-------------|
| **delete cascades** | When the parent record is deleted, child records are also deleted |
| **delete ignored** | Deletion of parent record ignores child records |
| **delete restricted** (default) | Deletion of parent record is prevented if child records exist |

## Field Name

```
name is <Literal>
```

Specifies an alternative name for the field.

## Field Structure Sections

The following sections in the Field structure have the same syntax as their counterparts in the Business Class structure. Refer to the Business Class Definition section for their syntax.

### Representation Ontology Patterns
Defines how the field is represented and its ontological relationships.

### Display Fields
Defines how the field is displayed in the user interface.

### Surrogate For
Defines surrogate relationships with other fields.

### Context Fields
Defines context variables for the field.

### Transient Fields
Valid on group or array fields only. Defines fields that are not persisted.

### Local Fields
Valid on group or array fields only. Defines fields that are local to the field definition.

### Derived Fields
Valid on group or array fields only. Defines fields whose values are calculated.

### Conditions
Valid on group or array fields only. Defines conditional logic for the field.

### Cube Relations
Valid on group or array fields only. Defines relationships to cube structures.

### Columnar Relations
Valid on group or array fields only. Defines columnar relationships.

### Relations
Valid on group or array fields only. Defines relationships to other entities.

### Rule Blocks
Defines reusable blocks of rules.

### Field Rules
Defines validation and business rules for the field.

## Field Types

### Simple Field
A basic field with a primitive type (Alpha, Numeric, Date, etc.)

### Group Field
A field that contains other fields as a group. Requires SQL prefix for duplicate instantiations.

### Array Field
A field that contains multiple occurrences of a value or group.

### Key Field
A field that serves as a key or identifier, potentially with an associated business class.

## Field Extension Examples

### KeyField Extension with Business Class

```
COMPANY is a KeyField
    owned by GL
    sql name is COMPANY
    classic name is "COMPANY"
    default label is "Company"
    is Alpha size 4
    
APCOMPANY is a KeyField
    extends COMPANY
    owned by AP
    // Inherits representation from COMPANY
    // Adds ApCompany business class context
```

### KeyField Extension with Context

```
PERSONNELCODE is a KeyField
    owned by HR
    is Alpha size 10
    
    Context Fields
        PCODETYPE is a PersonnelCodeType
        
LOCATIONCODE is a KeyField
    extends PERSONNELCODE
    owned by HR
    // Sets PCODETYPE to Location state
```

### Field Extension with Context Variable

```
CURRENCYVALUE is a Field
    is Decimal size 15.2
    
    Context Fields
        ISRATE is Boolean
        
CURRENCYAMOUNT is a Field
    extends CURRENCYVALUE
    // Sets ISRATE to false
```

## Best Practices

### Field Naming
- Use descriptive, uppercase names for fields
- Follow consistent naming conventions across modules
- Use prefixes to indicate field type or module ownership

### Field Ownership
- Assign fields to appropriate modules
- Use global fields sparingly for truly cross-module concepts
- Document ownership decisions

### Field Extension
- Extend fields to create specialized versions
- Use peer groups for surrogate key relationships
- Document context variable settings in extensions

### SQL and Classic Names
- Provide SQL names for database mapping
- Provide classic names for legacy system compatibility
- Keep names concise but meaningful

### Field Attributes
- Mark PII fields appropriately for compliance
- Use text searchable for fields that need full-text search
- Set appropriate default filter operators for user experience

### Delete Behavior
- Carefully consider cascade vs. restrict behavior
- Document delete behavior for key fields
- Test delete scenarios thoroughly
