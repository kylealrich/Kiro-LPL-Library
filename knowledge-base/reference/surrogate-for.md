# Surrogate For

Valid on key fields only.

## Syntax

```
Surrogate For
    <FieldName> value is <RelatedField>
    <FieldName> value is <RelatedField>
    ...
```

## Purpose

The Surrogate For section defines surrogate key relationships, allowing one key field to act as a substitute or alternative identifier for another key field.

## Context Fields

The Context Fields section declares a new 'non-ontological' context field.

### Non-Ontological Context Fields

- Non-ontological context fields are not required to be in the context
- They are evaluated newly each time the field is used
- Context rules are not automatically arranged for execution as are Field Rules
- The order they are in is the order in which they are executed

## Field Addressing with the Dot (.) Operator

Special considerations for field addressing, or what does the '.' operator do when used with different field types.

### On a KeyField

The '.' operator automatically addresses all the elements on the business class that the KeyField is centered on.

#### Subtype KeyField Addressing

If KeyField APCompany is a subtype of another KeyField Company:

**To address fields on the business class Company from APCompany:**
```
APCompany.Company.FieldName
```

**To address fields on APCompany from Company:**
```
Company.APCompany.FieldName
```

### On a GroupField

The '.' operator or any Field reference in a business class can reach through a GroupField or an ArrayField and address a Field on the GroupField if the reference results in an unambiguous target.

#### Reaching Through GroupFields

If Address is a GroupField that has a GroupField called PostalArea, which in turn contains City, State, and Zip:

**The reference:**
```
Address.City
```

Reaches through the GroupField PostalArea to find its target.

#### Reaching Through ArrayFields

The dot operator also reaches through an ArrayField.

#### KeyField Limitation

It does **not** reach through KeyFields to the KeyField's business class.

#### Direct Addressing in Business Class

If Address is on the business class Vendor, any rule on Vendor can directly address `City` and it does reach through both Address and PostalArea to find `City`.

## Examples

### Surrogate Key Example

```
GLCOMPANY is a KeyField
    business class is GlCompany
    type is Alpha size 4
    
    Context
        // context definitions
    
    Surrogate For
        APCOMPANY value is ApCompanyMapping
        POCOMPANY value is PoCompanyMapping
```

In this example, GLCOMPANY can act as a surrogate for both APCOMPANY and POCOMPANY, with the mappings defined by the respective fields.

### KeyField Subtype Addressing

```
// Given:
// COMPANY is a KeyField with business class Company
// APCOMPANY extends COMPANY with business class ApCompany

// From APCompany context, to access Company fields:
APCompany.Company.CompanyName

// From Company context, to access APCompany fields:
Company.APCompany.ApVendorCount
```

### GroupField Addressing

```
// Given structure:
ADDRESS is a GroupField
    Group Fields
        POSTALAREA is a GroupField
            Group Fields
                CITY is Alpha size 30
                STATE is Alpha size 2
                ZIP is Alpha size 10

// Direct addressing (reaches through both GroupFields):
Address.City
Address.State
Address.Zip

// Or more explicit:
Address.PostalArea.City
```

### ArrayField Addressing

```
// Given:
PHONENUMBERS is an ArrayField
    Group Fields
        PHONETYPE is Alpha size 10
        PHONENUMBER is Alpha size 20

// Addressing through array:
PhoneNumbers.PhoneType
PhoneNumbers.PhoneNumber

// In a rule on the business class:
for each PhoneNumbers
    if PhoneNumbers[i].PhoneType == "Mobile"
        // Process mobile phone
```

### Business Class Direct Addressing

```
// Given Vendor business class with Address GroupField:
VENDOR is a KeyField
    business class is Vendor
    
// In Vendor business class:
Persistent Fields
    ADDRESS is Address group
        // Contains PostalArea.City, PostalArea.State, etc.

// In a Vendor rule, you can directly address:
if City == "Chicago"
    // City is found by reaching through Address and PostalArea
```

## Best Practices

### Surrogate Keys

- Use surrogate keys to provide alternative identifiers
- Document the mapping logic clearly
- Ensure surrogate mappings are maintained consistently
- Consider performance implications of surrogate lookups

### Field Addressing

- Use the simplest addressing path that is unambiguous
- Leverage automatic reach-through for GroupFields and ArrayFields
- Be explicit when addressing across KeyField subtypes
- Document complex addressing patterns

### Context Fields

- Order context field definitions carefully since they execute in order
- Remember that context fields are evaluated each time they're used
- Use context fields for derived or calculated values
- Keep context field logic simple and efficient

### GroupField Design

- Design GroupField hierarchies to support intuitive addressing
- Avoid deeply nested GroupFields when possible
- Ensure field names are unique within reachable scope
- Test addressing patterns to ensure they're unambiguous

## Common Patterns

### Surrogate Mapping Pattern

```
MASTERCOMPANY is a KeyField
    business class is MasterCompany
    type is Alpha size 4
    
    Surrogate For
        GLCOMPANY value is GlCompanyId
        APCOMPANY value is ApCompanyId
        ARCOMPANY value is ArCompanyId
```

### Subtype Navigation Pattern

```
// Base KeyField
EMPLOYEE is a KeyField
    business class is Employee
    
// Specialized KeyField
MANAGER is a KeyField
    extends EMPLOYEE
    business class is Manager
    
// Accessing base fields from specialized:
Manager.Employee.HireDate

// Accessing specialized fields from base:
Employee.Manager.DepartmentCount
```

### Nested GroupField Pattern

```
CONTACTINFO is a GroupField
    Group Fields
        PRIMARYCONTACT is a GroupField
            Group Fields
                NAME is Alpha size 50
                EMAIL is Alpha size 100
                
        ALTERNATECONTACT is a GroupField
            Group Fields
                NAME is Alpha size 50
                EMAIL is Alpha size 100

// Addressing:
ContactInfo.PrimaryContact.Name
ContactInfo.PrimaryContact.Email
ContactInfo.AlternateContact.Name
ContactInfo.AlternateContact.Email

// Or with reach-through (if unambiguous):
// This would be ambiguous because NAME exists in both contacts
// ContactInfo.Name  // ERROR: Ambiguous

// But if there was a unique field:
ContactInfo.PrimaryContact.PreferredContactMethod  // OK if unique
```

## Advanced Concepts

### Surrogate Value Resolution

When a surrogate relationship is defined, the system uses the specified RelatedField to resolve the surrogate value. This allows for:
- Dynamic mapping between key systems
- Support for multiple identifier schemes
- Integration with external systems

### Context Field Evaluation

Context fields are evaluated in the order they appear and are re-evaluated each time they're referenced. This means:
- Later context fields can reference earlier ones
- Changes to source values are immediately reflected
- Performance considerations for frequently-accessed context fields

### Addressing Ambiguity Resolution

When the dot operator encounters multiple possible targets:
1. It first looks in the immediate scope
2. Then reaches through GroupFields in definition order
3. Then reaches through ArrayFields
4. It does not reach through KeyFields automatically
5. If still ambiguous, it results in an error

## Related Concepts

### Peer Groups

Surrogate For is related to the peer group concept where multiple KeyFields can act as surrogates for each other with identical values.

### Field Extensions

When KeyFields extend other KeyFields, the addressing patterns become important for accessing fields at different levels of the hierarchy.

### Business Class Ontology

The ontological context determines which fields are automatically in scope, while non-ontological context fields provide additional calculated or derived values.
