# Patterns

Design patterns that can be implemented on fields and business classes.

## Disable Static Translations

```
[disable StaticTranslations]
```

All `<Message>` definitions will not participate in Translation processes.

### Effects

- There will be no `<Field>Bundle.properties` file for the BL or the UI in the resource language packs
- Conversion of `<LiteralMessage>` to resource string will still take place

## Access Initializer Pattern

```
[implements AccessInitializer]
initializer is <FieldName>
```

Valid for Group and Array fields only. FieldName must be a DerivedField within this Field.

### Purpose

Provides initialization logic for field access.

## Mutually Exclusive Pattern

```
[implements MutuallyExclusive]
field determiner is <RelatedField>
<StateName> field is <FieldName>…
```

Valid in a GroupField only.

### Behavior

This pattern causes the GroupField to behave as though it is only a single field that has a dynamic type. The single field within the Group Field is determined by the defined 'field determiner' `<RelatedField>` which must be a state field.

### Requirements

- `<StateName>` must be a defined state on `<RelatedField>`
- All states on RelatedField must be specified

### Note

For some unknown reason, MutuallyExclusive fields get a default delimiter set (see CompactFormat).

### Example

```
PAYMENTMETHOD is a Field
    implements MutuallyExclusive
    field determiner is PaymentType
    
    Group Fields
        PAYMENTTYPE is PaymentTypeState
        
        Check field is CHECKNUMBER
        CHECKNUMBER is Alpha size 20
        
        CreditCard field is CARDNUMBER
        CARDNUMBER is Alpha size 16
        
        Cash field is CASHAMOUNT
        CASHAMOUNT is Decimal size 15.2
```

## Field Range Pattern

```
[implements FieldRange]
from is <FieldName>
    [exclusive]
to is <FieldName>
    [exclusive]
```

This pattern is valid in a GroupField only.

### Behavior

- A from value of blank means low-value
- A to value of blank means high-value
- To must always be greater than from
- Inclusive is the default

### Operators

This designation allows the following operators to be used with this field:
- `within` operator
- `overlaps` operator

### Example

```
DATERANGE is a Field
    implements FieldRange
    
    Group Fields
        from is STARTDATE
        STARTDATE is Date
        
        to is ENDDATE
        ENDDATE is Date
```

## Compact Format Pattern

```
[implements CompactFormat]
[delimiter is <Literal>]
[format fields based on primary form]
```

This pattern allows a GroupField or an ArrayField to be entered as a single string, each field being delimited by the designated delimiter.

### Implications

- The delimiter is not allowed as a character within any of the member or occurring fields
- The default delimiter is a double-hat `^^`
- The format (order, visibility, and so on) of the fields can be based on the primary form

### Example

```
ADDRESS is a Field
    implements CompactFormat
    delimiter is "|"
    
    Group Fields
        STREET is Alpha size 50
        CITY is Alpha size 30
        STATE is Alpha size 2
        ZIP is Alpha size 10

// Can be entered as: "123 Main St|Springfield|IL|62701"
```

## User Defined States Pattern

```
[implements UserDefinedStates]
```

This pattern treats this key field as though it has a list of States – only the state values are not hard-coded but rather defined within the instances of the business class.

### Key Implications

1. The number of instances is expected to be small
2. The field should be treated just as a hard-coded field that has states on it is in the UI
3. It should use a drop-down select to allow the user to choose a value rather than a larger select window

### Example

```
CUSTOMSTATUS is a KeyField
    implements UserDefinedStates
    business class is CustomStatus
    type is Alpha size 20
```

## Array Hierarchy Pattern

```
[implements ArrayHierarchy]
```

This pattern causes an Array KeyField to be treated as a Hierarchy of Keys.

### Requirements

- Valid only on an Array KeyField
- Currently all Array KeyFields must implement this pattern

### Delete Behavior

When a node in an Array Hierarchy is deleted, all sub-nodes will be deleted as well – it is an implicit delete cascades.

### Example

```
ACCOUNTSEGMENT is a KeyField
    implements ArrayHierarchy
    type is Alpha size 10
    occurs 5 times
```

## Parent Hierarchy Pattern

```
[implements ParentHierarchy]
[parent field name is <FieldName>]
[<ClassicName>]
[descendants are <RelatedLink>]
```

A ParentHierarchy is a Hierarchy managed by having a 'parent field' (which is a KeyField reference of this symbolic key) put on the business class that this symbolic key is defined for.

### Attributes

#### Parent Field Name
```
[parent field name is <FieldName>]
```
The parent field name defaults to `Parent<KeyField>`.

#### Descendants Optimization
```
[descendants are <RelatedLink>]
```
- RelatedLink must point back to this BusinessClass and end in a OTM relation
- The descendants operator can be optimized by overriding it with `descendants are…`
- This requires you to create a 'shadow' table that flattens the structure

### Delete Behavior

When a node in a Parent Hierarchy is deleted, all sub-nodes will be deleted as well – it is an implicit delete cascades.

### Example

```
DEPARTMENT is a KeyField
    implements ParentHierarchy
    parent field name is PARENTDEPT
    business class is Department
    type is Alpha size 10
    
    Context
        COMPANY
```

## Versioning Pattern

```
[implements Versioning]
version field is <FieldName>
[<ClassicName>]
```

This pattern causes the symbolic key's business class to be versioned using the designated version field.

### Version Field Types

The version field is often of type Date but can also be other field types, for example a Number.

### Implementation

A field of type FieldName will be added to the business class and also be put at the bottom of the primary index.

### Example

```
CONTRACT is a KeyField
    implements Versioning
    version field is EFFECTIVEDATE
    business class is Contract
    type is Alpha size 20
    
    Context
        COMPANY
```

## Specialization Pattern

```
[implements Specialization]
[control valid key values]
[effective date as a group]
[set retrieval is inclusive]
```

The Specialization pattern can be specified when any Context Key is optional. This pattern allows a single reference to the KeyField to search the business class for the appropriate business object.

### Control Valid Key Values

```
[control valid key values]
```

When the valid key values are controlled:
- There must exist a key value at the non-optional key level before it can be specialized
- If the highest key value is deleted, then all of the specializations are also deleted (delete cascades)
- Delete restricted and delete ignored are not supported

If this is not specified:
- The key values of the specializations are uncontrolled
- A key value can be added at some specialization level without a higher level value being present

### Effective Date as a Group

```
[effective date as a group]
```

Valid only when EffectiveDated pattern is implemented. This option causes the set of specializations to be effective dated as a group.

### Set Retrieval

```
[set retrieval is inclusive]
```

- **Exclusive** is the default
- **Inclusive** means that the higher level 'specializations' are included as part of the lower level specializations

**Example:** JobQualifications - The company level qualifications are considered to be part of the job-specific qualifications.

### Specialization Search Algorithm

The appropriate business object is the one that is the most 'specialized' based on what keys are optional:
- The least specialized object is one where all the optional keys are blank
- The most specialized object is one where all the optional keys are filled in
- It searches most specialized to least specialized

#### Hierarchical Optional Keys

If the optional keys are strictly hierarchically related based on their ontology, the search algorithm blanks optional key values starting from the lowest optional key and moving toward the highest optional key.

#### Non-Hierarchical Optional Keys

If the optional keys are not hierarchically related, the search algorithm will similarly work from lowest to highest key, however it will put values back into the lower keys.

**Example:** Given keys k1, k2, k3 with k2 and k3 optional and not hierarchically related, the search pattern is:
1. (k1, k2, k3)
2. (k1, k2, blank)
3. (k1, blank, k3)
4. (k1, blank, blank)

### Specialization Example

```
QUALIFICATION is a KeyField
    implements Specialization
    control valid key values
    set retrieval is inclusive
    business class is Qualification
    type is Alpha size 20
    
    Context
        COMPANY
            delete restricted
            
        JOB
            optional
            delete restricted
```

## Pattern Combinations

### Versioning with Specialization

```
PRICELIST is a KeyField
    implements Versioning
    version field is EFFECTIVEDATE
    implements Specialization
    control valid key values
    effective date as a group
    business class is PriceList
    type is Alpha size 20
    
    Context
        COMPANY
            delete restricted
            
        CUSTOMER
            optional
            delete restricted
```

### Parent Hierarchy with Versioning

```
ACCOUNTCODE is a KeyField
    implements ParentHierarchy
    parent field name is PARENTACCOUNT
    descendants are AccountHierarchy.Descendants
    implements Versioning
    version field is EFFECTIVEDATE
    business class is Account
    type is Alpha size 20
    
    Context
        COMPANY
```

## Best Practices

### Static Translations

- Disable static translations only when you have a specific reason
- Consider the impact on localization and resource bundles
- Document why translations are disabled

### Mutually Exclusive

- Use for fields that represent different types of the same concept
- Ensure all states are covered
- Provide clear field determiner logic
- Test all state transitions

### Field Range

- Use for date ranges, numeric ranges, or any bounded values
- Validate that 'to' is always greater than 'from'
- Consider whether inclusive or exclusive boundaries are appropriate
- Provide clear error messages for invalid ranges

### Compact Format

- Choose delimiters that won't appear in field values
- Document the field order for users
- Consider using primary form formatting for consistency
- Test with edge cases (empty fields, special characters)

### User Defined States

- Use only when the number of states is small and manageable
- Ensure the business class is properly designed
- Consider performance implications
- Provide good UI for managing state values

### Array Hierarchy

- Understand the implicit delete cascades behavior
- Design the hierarchy depth carefully
- Consider query performance for deep hierarchies
- Document the hierarchy structure

### Parent Hierarchy

- Use when you need flexible, runtime-defined hierarchies
- Consider creating shadow tables for performance
- Test delete cascades thoroughly
- Document the parent-child relationships

### Versioning

- Choose appropriate version field types (Date, Number)
- Consider the impact on queries and indexes
- Plan for version history management
- Test version transitions

### Specialization

- Use when you need context-specific variations
- Carefully design optional key hierarchies
- Understand the search algorithm implications
- Test all specialization levels
- Consider performance with many specialization levels
- Document the specialization strategy
- Use "control valid key values" for data integrity
- Use "set retrieval is inclusive" when higher-level data should be inherited
