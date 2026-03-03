# Ontology

Valid on key fields only.

## Stereotype

```
[stereotype is <Stereotype>]
```

Defines the stereotype classification for the business class.

### Stereotype Types

```
Stereotype ::= ( Agent
               | BusinessPolicy
               | Document
               | DocumentAccounting
               | DocumentDistribution
               | DocumentFulfillment
               | AccountingTransaction)
```

### Stereotype Classes

Stereotypes are grouped into two classes:

#### Non-Transaction Stereotypes
- **Agent**: Represents an actor or agent in the system
- **BusinessPolicy**: Represents business rules and policies

#### Transaction Stereotypes
- **Document**: Base document stereotype
- **DocumentDistribution**: Distribution-related documents
- **DocumentFulfillment**: Fulfillment-related documents
- **AccountingTransaction**: Accounting transaction documents
- **DocumentAccounting**: Accounting-related documents

## Business Class

```
business class is <BusinessClass>
```

This is the business class that contains the instances of this KeyField. This business class must specify this KeyField as its symbolic key.

## Existence

```
[existence not required]
```

Indicates that the existence of this key field is not required.

## Context

The ontological context defines the affordance structure.

### Affordance Structure Rules

- Typically there is only a single KeyField in an affordance structure
- If the single Keyfield itself has an affordance context KeyField, that KeyField is also implicitly part of this KeyField's affordance structure
- If there are two KeyFields in an affordance context, it means that the second one is not afforded by the first one, therefore there is a dual affordance structure
- The first key can be a surrogate for the higher level context of the second key

### Context Syntax

```
Context
    <KeyField>
        [version is (latest | exact)]
        [value is <RelatedValue>]
        [delete ( restricted | cascades | ignored)]
        [disable surrogates]
        [context of <FullFieldName>]…
        [optional]
        [data area is <RelatedValue>]
        [(enable | disable) surrogate context]
        [within <RelatedValue>]
        [<Message>]
```

## Context Attributes

### Version

```
[version is (latest | exact)]
```

A versioned Context field will automatically have its version field included in the ontology unless `version is latest` is specified.

| Option | Description |
|--------|-------------|
| `latest` | Uses the latest version of the context |
| `exact` | Uses the exact version specified |

### Value

```
[value is <RelatedValue>]
```

A Context key can be set to a specific value. Typically this only happens when the key is extending another key that has this Context key.

**Example:** BargainingUnit extends Pcode and sets Pcode's PcodeType context to 'BU'.

### Delete Rules

```
[delete ( restricted | cascades | ignored)]
```

| Rule | Description | Default |
|------|-------------|---------|
| `restricted` | Deletion is prevented if child records exist | Yes |
| `cascades` | Child records are deleted when parent is deleted | No |
| `ignored` | Deletion of parent ignores child records | No |

### Disable Surrogates

```
[disable surrogates]
```

Disables surrogate key handling for this context.

### Context Of

```
[context of <FullFieldName>]…
```

This can be used to disambiguate which field on this business class should be used as the Context field for this KeyField.

### Optional Context

```
[optional]
```

If one of the Context Keys is optional, there must be a higher level Context Key specified even though the first Context Key implicitly refers to it.

**Purpose:**
- Makes it clear that this KeyField can be afforded at both levels
- Allows for the delete rule of the higher Context Key to be specified
- This higher level Context Key that is not optional is the group level key
- This is the key level at which effective dating is done when "effective date as a group" is specified in a Specialization pattern

### Data Area

```
[data area is <RelatedValue>]
```

Valid only when in a 'stored in environment' busclass.

### Surrogate Context

```
[(enable | disable) surrogate context]
```

- **enable** (default): If a context KeyField has a surrogate, by default this surrogate field is put on the business class and a secondary index is created so that this field can be validated from either the primary context or the surrogate context
- **disable**: Disables surrogate context handling

### Within

```
[within <RelatedValue>]
```

Valid on a KeyField only. `<RelatedValue>` must be a field referenced via a OTM relation that defines a set of KeyFields of this KeyField's type.

### Message

```
[<Message>]
```

An optional error message associated with the context constraint.

## Field Naming with Extension

If a Key Field extends another key field, the default name of that key field is the highest level field name in the extends chain. This can be overridden with a `name is` clause.

**Nuance:** When a key field extends another key field but has a context value set, then that field should have a default `name is` of the key field name itself.

## Ontology Examples

### Simple Business Class with Stereotype

```
COMPANY is a KeyField
    stereotype is BusinessPolicy
    business class is Company
    type is Alpha size 4
```

### Document Stereotype

```
PURCHASEORDER is a KeyField
    stereotype is Document
    business class is PurchaseOrder
    type is Alpha size 10
    
    Context
        COMPANY
            delete restricted
```

### Agent Stereotype

```
EMPLOYEE is a KeyField
    stereotype is Agent
    business class is Employee
    type is Alpha size 10
    
    Context
        COMPANY
            delete restricted
```

### Context with Value

```
BARGAININGUNIT is a KeyField
    extends PCODE
    business class is BargainingUnit
    
    Context
        PCODETYPE
            value is PcodeType.BU
```

### Versioned Context

```
CONTRACTLINE is a KeyField
    business class is ContractLine
    type is Integer
    
    Context
        CONTRACT
            version is exact
            delete cascades
```

### Optional Context

```
DEPARTMENT is a KeyField
    business class is Department
    type is Alpha size 10
    
    Context
        COMPANY
            delete restricted
            
        DIVISION
            optional
            delete restricted
```

### Dual Affordance Structure

```
ALLOCATION is a KeyField
    business class is Allocation
    type is Integer
    
    Context
        SOURCEACCOUNT
            delete restricted
            
        TARGETACCOUNT
            delete restricted
```

### Context with Surrogate Disabled

```
PROJECTTASK is a KeyField
    business class is ProjectTask
    type is Integer
    
    Context
        PROJECT
            delete cascades
            disable surrogates
```

### Context with Data Area

```
GLOBALCONFIG is a KeyField
    business class is GlobalConfig
    type is Alpha size 20
    stored in environment
    
    Context
        TENANT
            data area is "GLOBAL"
```

### Context with Within Constraint

```
LINEITEM is a KeyField
    business class is LineItem
    type is Integer
    
    Context
        ORDER
            delete cascades
            within ValidLineItems
            "Line item must be within valid line items for this order"
```

### Disable Surrogate Context

```
SUBSIDIARY is a KeyField
    business class is Subsidiary
    type is Alpha size 4
    
    Context
        PARENTCOMPANY
            delete restricted
            disable surrogate context
```

## Best Practices

### Stereotype Selection

- Use **Agent** for entities that perform actions (employees, vendors, customers)
- Use **BusinessPolicy** for configuration and policy entities
- Use **Document** stereotypes for transactional entities
- Choose the most specific document stereotype (Distribution, Fulfillment, Accounting)

### Context Design

- Keep affordance structures simple when possible
- Use single KeyField contexts unless dual affordance is truly needed
- Document the reasoning for dual affordance structures
- Use optional contexts sparingly and document their purpose

### Delete Rules

- Use **restricted** (default) for most parent-child relationships
- Use **cascades** only when child records have no independent meaning
- Use **ignored** rarely and with caution
- Document any non-default delete rules

### Versioning

- Use `version is exact` when historical accuracy is critical
- Use `version is latest` for configuration-type relationships
- Consider the impact on queries and performance

### Surrogates

- Enable surrogate context by default for flexibility
- Disable surrogates only when there's a clear performance or design reason
- Document why surrogates are disabled

### Context Values

- Use context values when extending keys to specialize behavior
- Ensure context values are well-documented
- Consider using states for context value types

### Optional Contexts

- Use optional contexts to support multiple affordance levels
- Always specify the higher-level non-optional context
- Document the effective dating implications
- Consider the impact on data integrity

### Within Constraints

- Use within constraints to enforce complex business rules
- Provide clear error messages
- Ensure the OTM relation is properly defined
- Test constraint validation thoroughly
