# Business Class Patterns

Design patterns that can be implemented on business classes.

## Auditing Patterns

### Disable Auditing

```
[disable Auditing]
```

By default, non-'Classic' Business Classes implement the Auditing pattern. BinaryDocuments and BinaryObjects do not participate in this pattern.

### Lightweight Auditing

```
[implements LightweightAuditing]
```

Initial create does not create the log; it is instantiated on the first update or delete.

### Disable Audit Index

```
[disable AuditIndex]
```

By default, an AuditIndex (the 'snapshot' table) is created when a BusinessClass is Audited and AsOfDateProcessing is not disabled. The AuditIndex optimizes as of date queries at the expense of creates and updates.

### Audit Log Entry Actions

```
[implements AuditLogEntryActions]
```

Force AuditLogEntry record create for actions (overrides config param).

### Surrogate Audit Log

```
[implements SurrogateAuditLog for <FullFieldName>]
```

Causes the audit log of this business class to really be the audit log of FullFieldName. FullFieldName must be a Text field that contains a serialized DataView. This is currently only being used for environment business classes.

## Effective Dating Patterns

### Implements Effective Dated

```
[implements EffectiveDated]
[bypass integrity rules]
```

Enables effective dating for the business class.

### Disable Effective Dated

```
[disable EffectiveDated]
```

By default, non-'Classic' Business Classes implement the EffectiveDated pattern. Also by default, BinaryDocument and BinaryObject fields do not participate in this pattern. They can be made to participate in this pattern by specifying `enable EffectiveDated` on the field definition.

### Disable Retroactive Effective Dating

```
[disable RetroactiveEffectiveDating]
```

Prevents retroactive effective dating.

### History Correction

```
[implements HistoryCorrection]
```

Allows past audit log entries to be adjusted.

### Future Correction

```
[implements FutureCorrection]
```

Allows future entries to be adjusted.

### Disable As Of Date Processing

```
[disable AsOfDateProcessing]
```

Disables as-of-date query processing.

## Deletion Patterns

### Once Used Never Delete

```
[implements OnceUsedNeverDelete]
```

Prevents deletion of records that have been used.

### Delete Flag

```
[implements DeleteFlag]
```

Valid only for business classes that specify "store using". Uses a flag instead of physical deletion.

## Unique ID Patterns

### Disable UniqueID

```
[disable UniqueID]
```

Disables automatic UniqueID generation.

### Implements UniqueID

```
[implements UniqueID]
```

By default UniqueID is disabled and LazyUniqueId is enabled.

### Lazy UniqueID

```
[implements LazyUniqueID]
```

All business classes automatically get a UniqueId put on them to support the ability to address any LPL based business class directly through a UniqueId. If the business class is not created through the IJF runtime but is rather an 8.1 or third party database table, then it needs to implement this pattern. This pattern will generate an outboard file and the appropriate logic in the business class to allow the IJF runtime to access the outboard table via a UniqueId.

## Translation Patterns

### Disable Data Translations

```
[disable DataTranslations]
```

This disables all data translation for this business class.

### Disable Static Translations

```
[disable StaticTranslations]
```

All `<Message>` definitions will not participate in Translation processes. There will be no `<BusinessClass>Bundle.properties` file for the BL or the UI in the resource language packs. Conversion of `<LiteralMessage>` to resource string will still take place.

## Replication Patterns

### Incremental Replication

```
[implements IncrementalReplication]
    [indicator field is <FullFieldName>]…
    replicate when <Value>
    then (set to <Value> | invoke <ActionName> [<FullStateName>])
```

If no indicator field is specified, then must have a change stamp meaning that it must be audited or implements UpdateStamp.

**CRITICAL NOTE:** If the indicator field is included in an index, and the indicator field value is changed on a record during a replication, it is possible to pick up the record twice due to the notion of laying tracks. There are ways to mitigate this.

### Disable Incremental Replication

```
[disable IncrementalReplication]
```

Disables IncrementalReplication if implemented at the Product Line level.

## User Fields Pattern

### Inline User Fields

```
[implements InlineUserFields]
size is <Number>
```

Puts user field values directly on the business class in a VarChar field. Must define how big the field is that contains all the user field values. Field will contain user field name plus actual value.

## Java Integration Patterns

### Static Java

```
[implements StaticJava]
```

This pattern generates a StaticJava DB interface class.

## Attachment Patterns

### Has Attachments

```
[has attachments]
```

Generates `<BusClass>_URL` and `<BusClass>_Comment` tables.

### Comment Attachments

```
[has attachments]
[<attachment FieldName> is comment of type <Literal>…]
```

Comment type attachments are accessed through a Text field name `<AttachmentField>`. All comments of a particular comment type are concatenated into a single Text field. Also disables Auditing and EffectiveDated.

### S3 Classic Attachments

```
[implements S3ClassicAttachments]
[<attachment FieldName> is comment of type <Literal>…]
```

## Performance Patterns

### In Memory Cache

```
[implements InMemoryCache]
```

This pattern stores and retrieves business class instances from an in-memory cache. The cache is valid only within a process and records can only be retrieved through the KeyField.

### Force UI Refresh On Stale

```
[implements ForceUIRefreshOnStale]
```

Forces UI refresh when data becomes stale.

## Work File Pattern

### Work File

```
[implements WorkFile]
```

This causes the business class to be treated as a WorkFile.

## External Data Patterns

### Foreign Table

```
[implements ForeignTable]
[allow updates]
```

This pattern by default implements the ReadOnly pattern and the LazyUniqueId pattern. The ReadOnly pattern can be inhibited by specifying `allow updates`.

### S3 Classic Table

```
[implements S3ClassicTable]
[allow updates]
```

This pattern by default implements the ReadOnly pattern and the LazyUniqueId pattern. The ReadOnly pattern can be inhibited by specifying `allow updates`.

### M3 Classic Table

```
[implements M3ClassicTable]
[allow updates]
```

This pattern by default implements the ReadOnly pattern and the LazyUniqueId pattern. The ReadOnly pattern can be inhibited by specifying `allow updates`. Also disables Auditing and EffectiveDated.

### Read Only

```
[implements ReadOnly]
```

Makes the business class read-only.

## Resequence Pattern

```
[implements Resequence on <FieldName>]
new sequence field is <FieldName>
set is <SetName>
```

- `FieldName` must be numeric
- Resequence field and new sequence field must be different fields
- SetName must contain the Resequence field
- Whenever the new sequence field is entered with a different value from the resequence field, the record will be moved to the new sequence field position and then the whole set as defined by SetName will be resequenced
- Typically new sequence field would be a Transient Field
- This pattern will also automatically generate an autosequence rule if the sequence field as long as it does not already have an autosequence rule manually defined
- When doing mass import using an update action, the new sequence field can be set to -1 to prevent resequencing, which will significantly improve performance

## Dynamic Creation Pattern

```
[implements DynamicCreation]
```

Causes an object to be created automatically if it does not exist when another business object 'attaches' to it or if an Update type action is called. The creation can be constrained or special action can be taken upon dynamic creation by putting rules in the Dynamic Creation Rules section.

## Analytic Cube Pattern

```
[implements AnalyticCube]
[label is <Message>]
[columnar only]
[(pre-calculate totals | dynamically calculate totals)]
[(allow write to summary level | write to base level only)]
[refresh using (audit log | message queue)]
[beginning balance is period zero]
[Instance Selection]
    where <Condition>
```

### Analytic Cube Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `label is <Message>` | If not blank, this will be used as the cube name caption | |
| `columnar only` | Restricts to columnar storage | |
| `pre-calculate totals` | Pre-calculates totals | Default |
| `dynamically calculate totals` | Calculates totals on demand | |
| `allow write to summary level` | Allows writing to summary level | Default |
| `write to base level only` | Restricts writes to base level | |
| `refresh using audit log` | Refreshes using audit log | Default (unless auditing disabled) |
| `refresh using message queue` | Refreshes using message queue | |
| `beginning balance is period zero` | Period operators will use period zero for beginning balance | |

### Instance Selection

```
where <Condition>
```

Limits what fact rows are put in the cube.

## Child Hierarchy Pattern

```
[implements ChildHierarchy]
[top node when <SimpleCondition>]
[leaf node when <SimpleCondition>]
( children are <RelatedLink>
| child set is <RelatedLink>
  child is <KeyField>
  [aggregation percent is <FullFieldName>])
[descendants are <RelatedLink>]
```

A ChildHierarchy is a Hierarchy defined by its children. It can have multiple parents, which means that the hierarchy operators parent, siblings, ancestors, and ascendant are not valid. When used as a Dimension, `child set is…` is required and `top node when…` is highly recommended.

### Child Hierarchy Attributes

- **children are**: RelatedLink must point back to this same business class and end in a OTM relation
- **child set is**: RelatedLink must point to the intermediate business class that determines the children (required if this KeyField is used in a Dimension)
- **child is**: KeyField must be a KeyField to this business class
- **aggregation percent**: Must be a Percent field
- **descendants are**: Similar to 'children are' but for all descendants

## Stamp Patterns

### Create Stamp

```
[implements CreateStamp]
```

Adds create timestamp tracking.

### Update Stamp

```
[implements UpdateStamp]
```

Adds update timestamp tracking.

## Proxy Pattern

```
[implements Proxy for <RelatedLink>]
[Additional Field Mapping]
    related.<FullFieldName> = <RelatedValue>…
```

Must be OTO relation or KeyField. Allows this business class to act as a proxy for another.

## Compound Document Pattern

```
[implements CompoundDocument]
Document Components
    <RelatedLink>…
```

Must have at least one RelatedLink. Defines a compound document structure.

## BOD ID Pattern

```
[implements BODId]
```

Implements Business Object Document ID pattern.

## Encryption Pattern

```
[implements Encrypted]
[when <Condition>]
```

Encrypts the business class data. Condition can reference only mutable non-related fields.

## Contextual Parent Pattern

```
[implements ContextualParent]
```

Extends Context search to ontological parent business class.

## Template Driven Pattern

```
[implements TemplateDriven by <BusinessClass>]
[completion message is <Message>]
[( create all instances
 | create instance
   when <Condition>)]
```

**Example:** Answer is a part of Response and extends Question. Answer is designated as being TemplateDriven by Question. Question is a template for adding, updating and deleting corresponding Answer instances. An Answer instance cannot be created without a corresponding Question instance.

This pattern works with a `<Template>` definition within a `<NavigationDefinition>` or a `<PanelDefinition>`.

- If `create all instances` is specified (default), an Answer instance will be created for every Question in the template list
- If the create is conditional, an Answer instance will be created only if the condition evaluates to true. If the condition is false, any existing corresponding Answer instance will be deleted
- `<Message>` can contain text only
- Condition can reference only mutable non-related fields

## Data Source Patterns

### Data Source

```
[implements <DataSourceType> DataSource]
```

This pattern defines an alternate data source to satisfy the business class contract. This means the CRUD pattern and additional actions are realized by an external data source.

### Extends External Data

```
[implements ExtendsExternalData]
[using <ClassName>]
```

ClassName is a JavaClass that provides access to the external data. This pattern allows for the extension of an external data source. With this pattern, external data is viewed as a business class and the user is able to logically add to or change that data. The actual underlying data is not changed. The underlying data might be a property file on disk or some other flat file format.

## Pattern Examples

### Audited Business Class

```
CUSTOMER is a BusinessClass
    owned by AR
    
    Patterns
        implements LightweightAuditing
        implements HistoryCorrection
    
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNAME is Alpha size 50
```

### Effective Dated Business Class

```
PRICELIST is a BusinessClass
    owned by Pricing
    
    Patterns
        implements EffectiveDated
        implements FutureCorrection
    
    Persistent Fields
        PRICELIST is PriceList
        EFFECTIVEDATE is Date
        PRICE is Decimal size 15.2
```

### Cached Business Class

```
SYSTEMCONFIG is a BusinessClass
    owned by System
    
    Patterns
        implements InMemoryCache
        implements ReadOnly
    
    Persistent Fields
        CONFIGKEY is Alpha size 50
        CONFIGVALUE is Alpha size 255
```

### Resequenceable Business Class

```
MENUITEM is a BusinessClass
    owned by UI
    
    Patterns
        implements Resequence on SEQUENCE
        new sequence field is NEWSEQUENCE
        set is MenuItemSet
    
    Persistent Fields
        MENU is Menu
        SEQUENCE is Integer
    
    Transient Fields
        NEWSEQUENCE is Integer
```

### Analytic Cube

```
SALESFACT is a BusinessClass
    owned by Analytics
    
    Patterns
        implements AnalyticCube
        label is "Sales Analysis"
        pre-calculate totals
        allow write to summary level
        refresh using audit log
        Instance Selection
            where SalesAmount > 0
    
    Persistent Fields
        SALESDATE is Date
        PRODUCT is Product
        CUSTOMER is Customer
        SALESAMOUNT is Decimal size 15.2
```

### Template Driven Business Class

```
ANSWER is a BusinessClass
    owned by Survey
    
    Patterns
        implements TemplateDriven by QUESTION
        completion message is "Survey completed"
        create instance
            when Question.Required == true
    
    Persistent Fields
        RESPONSE is Response
        QUESTION is Question
        ANSWERTEXT is Alpha size 255
```

### Example: PurchaseOrder - Multiple Enterprise Patterns

From the PurchaseOrder business class, demonstrating multiple pattern implementations:

```
PurchaseOrder is a BusinessClass
    owned by po
    prefix is PCR
    classic name is PURCHORDER

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
```

This example demonstrates:
- **ContextualParent**: Extends context search to parent business class for child records
- **StaticJava**: Generates StaticJava DB interface class for Java integration
- **BODId**: Implements Business Object Document ID for integration scenarios
- **disable AuditIndex**: Disables audit index (snapshot table) to improve create/update performance
- **AnalyticCube**: Enables analytical reporting with specific configuration:
  - `disable continuous update`: Prevents automatic cube updates
  - `dynamically calculate totals`: Calculates totals on-demand rather than pre-calculating
  - `write to base level only`: Restricts writes to base level, not summary levels
- **ForceUIRefreshOnStale**: Forces UI refresh when data becomes stale
- **Archivable**: Supports data archiving for historical records

This combination is typical for enterprise transactional documents that:
- Need integration with external systems (BODId, StaticJava)
- Require analytical reporting (AnalyticCube)
- Have parent-child relationships (ContextualParent)
- Need performance optimization (disable AuditIndex, dynamically calculate totals)
- Support data lifecycle management (Archivable)

## Best Practices

### Auditing

- Use LightweightAuditing for high-volume tables
- Disable AuditIndex if as-of-date queries are rare
- Use HistoryCorrection only when necessary
- Consider storage implications of full auditing

### Effective Dating

- Enable EffectiveDated for temporal data
- Use FutureCorrection for planning scenarios
- Disable RetroactiveEffectiveDating for data integrity
- Test effective dating scenarios thoroughly

### Performance

- Use InMemoryCache for frequently-accessed, rarely-changed data
- Consider DisableAuditIndex for write-heavy tables
- Use LightweightAuditing to reduce audit overhead
- Monitor cache memory usage

### External Data

- Use ReadOnly for external data sources
- Implement LazyUniqueID for third-party tables
- Document external data dependencies
- Test external data integration thoroughly

### Replication

- Configure IncrementalReplication carefully
- Avoid indicator fields in indexes
- Test replication scenarios
- Monitor replication performance

### User Fields

- Size InlineUserFields appropriately
- Document user field structure
- Consider query performance
- Test with maximum user field data

### Analytic Cubes

- Pre-calculate totals for query performance
- Use columnar storage for large datasets
- Filter instances appropriately
- Monitor cube refresh performance

### Template Driven

- Use for survey/questionnaire patterns
- Define clear completion criteria
- Test conditional creation logic
- Document template relationships
