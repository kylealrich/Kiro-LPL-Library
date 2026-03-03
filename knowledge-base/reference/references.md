# References

## Base URL

```
BaseURL ::= base url<Parens>webapp is <WebAppName><Parens>
```

References the current base url context.

## Business Class Total

```
BusinessClassTotal ::= total<Parens><BusinessClass>.<RelatedValue>[,<DimensionField> = <RelatedValue>]…<Parens>
```

- First RelatedValue must be a defined TotalName on the specified BusinessClass
- DimensionField is a dimension in the BusinessClass

## Configuration Variables

### Config Category
```
ConfigCategory ::= <Literal>
```

### Config Variable
```
ConfigVar ::= [stack]config[<Parens><ConfigCategory><Parens>].<Literal>
```

Allows reference to a configuration variable. Configuration variables are defined in the BusinessClass ConfigurationParameter - if it is not found it is blank.

- **stackconfig**: Checks the parameter at the dataarea, then the tenant, then the stack

## Data Link

```
DataLink ::= [<LinkOperator>]( <KeyField> [set]
           | <hierarchy KeyField> ( parent
                                  | [and] children
                                  | [and] siblings
                                  | [and] descendants
                                  | [and] ancestors
                                  | ascendant)
           | <RelationName>
           | <CubeRelationName>
           | <ColumnarRelationName>
           | <BusinessClass>[<Parens>(<KeyField> | <BusinessObjectReference>)<Parens>] [set]
           | <BusinessObjectReference>[<Parens><BusinessClass><Parens>]
           | <BusinessClassTotal>
           | <Agent>
           | <SessionKey>
           | actor.context.<KeyField>
           | audit log records [descending]
             // retrieves the set of past and future (effective-dated) records
           | [(draft | in process | completed | rejected)] [<BusinessClass>.Create] action requests
           | this instance
           | related // valid within a Relation Definition
           | from // valid within a where condition on an OTM DataLink
           | each[<Parens><LPLConstructName><Parens>] // valid within a for each loop
           | invoked // valid within an invocation
           | result // valid within an invocation
           | child // valid within Parent Attach Rules
           | cube[<Parens><BusinessClass><Parens>]
             // used to address the AnalyticCube business class
           | cube drill set // used to address the drill set from an AnalyticCube
           | <TimeZoneVar>)
           [<AsOfOperator>]
           [<Parens>locale of <RelatedValue><Parens>]
             // RelatedValue must be an IsoLocale
           [<Parens>where <Condition><Parens>]
           [<Parens>level <#><Parens>] // only valid on a hierarchy KeyField
```

### DataLink Notes

- **RelatedLink option**: Indicates that you can string related links recursively
  - Example: `KeyField.RelationName.KeyField.RelationName.Field`

- **BusinessClass set**: The BusinessClass must either:
  - Have a 'part of' ontology to the preceding item, or
  - Have a field that is one of the symbolic keys of the preceding item
  - If there is more than one field that matches the preceding items symbolic keys, then `(<KeyField>)` must be present to disambiguate

- **BusinessObjectReference**: Must have a BusinessClass designation if it has not been defined as a particular kind of BusinessClass

- **Agent**: If this business class is an Agent, another agent that has been linked to this agent can be addressed with the `agent(<BusinessClass>)` designation. To access any agents that the current actor has been linked with, add the `actor` keyword in front of the agent keyword.

- **cube**: Must be the first item in any RelatedLink. It refers to this business class' AnalyticCube unless overridden with a specific BusinessClass inside the parens.

### Hierarchy Keywords

- **parent**: Parent node in hierarchy
- **children**: Child nodes
- **and children**: Include children
- **siblings**: Sibling nodes
- **and siblings**: Include siblings
- **descendants**: All descendant nodes
- **and descendants**: Include descendants
- **ancestors**: All ancestor nodes
- **and ancestors**: Include ancestors
- **ascendant**: Ascendant node

### Action Request States

- **draft**: Draft action requests
- **in process**: In-process action requests
- **completed**: Completed action requests
- **rejected**: Rejected action requests

## Distinct

```
Distinct ::= distinct (<FullFieldName> | <FieldGroupName>)
```

Currently used in 'for each' rule only. Returns a set of instances where the Field (or Fields) are distinct – that is, no duplicates of Field (or Fields) will be returned. The instances returned will have only the distinct fields filled out as well as any fixed fields, such as higher level keys that are fixed in the related definition.

## Document Template

```
DocTemplate ::= (template.<Literal> | template name.<RelatedField>)
```

Allows reference to an XML, JSON, Word rtf, or PDF template stored in the UserTemplate business class.

## Inline Equation

```
InlineEquation ::= [<Parens>]<RelatedValue> <MathOperator> <RelatedValue>[<Parens>]
```

A basic InlineEquation `(A + B)` can also be considered a RelatedValue, because InlineEquation is an option of RelatedField, which is an option of RelatedValue. This means it can be used as a RelatedValue in the more complex InlineEquation `((A + B) + C)`, where:
- `(A + B)` is the first `<RelatedValue>`
- The second `+` is the `<MathOperator>`
- `C` is the second `<RelatedValue>`

## Logical ID

```
LogicalID ::= logical id
```

Logical id is set in LogicalIdMapping.

## Parent Context

```
ParentContext ::= parentcontext. ( isbusclass
                                  | name
                                  | locale
                                  | stereotype
                                  | istransaction
                                  | module
                                  | dataarea
                                  | <SymbolicKeyVar>
                                  | isbustask>)
```

- **parentcontext**: Refers to the parent context of the current context
- **symbolickey**: Retrieves the context information of the Symbolic Key that this business object is 'centered' on
- If the business object is not defined directly via a Symbolic Key but is defined as being 'part of' some other business class, then it gets the context information of the Symbolic Key of that business class.

## Phrase

```
Phrase ::= phrase[<Parens>locale of <RelatedValue><Parens>].<Literal>
```

Allows reference to a translatable phrase. Phrases are defined in the UserPhrase business class.

## Related Condition

```
RelatedCondition ::= [<FieldOperator>]( [<RelatedLink>.]<ConditionName>
                                       | <RelatedField>.<StateName>)
```

## Related Field

```
RelatedField ::= [<FieldOperator>] +
                 ([<RelatedLink>.]<FullFieldName>
                   // <FullFieldName> is optional with the FieldOperators 
                   // instance count of and (first | last) iteration of
                 | <RelatedLink> (first | last) date within <RelatedLink>
                   // Second RelatedLink must point to a BusClass group. 
                   // The result of this statement is a date (never a condition) 
                   // so 'first' or 'last' is required. 
                   // For example, 'Employee date within EnrollmentGroup' implies a condition 
                   // whereas 'first date within' clearly implies that the result is a date.
                 | instance count [where <Condition>]
                   // valid in List Definition Summary Total Fields and ColumnarView lists only
                 | <InlineEquation>) 
                 [<TypeOperator>] 
                 [<TotalOperator>]
```

## Related Link

```
RelatedLink ::= <DataLink>[.<DataLink>…]
```

## Related Value

```
RelatedValue ::= ( <RelatedField>[.<StateName>]
                   // StateName is one of the States defined on the RelatedField
                 | <Value>
                 | <ParentContext>
                 | <Actor>
                 | <CurrentDateTime>
                 | <DurationVars>
                 | <CreateStamp>
                 | <UpdateStamp>
                 | <ConfigVar>
                 | <DocTemplate>
                 | <Phrase>
                 | <URLVar>
                 | <SessionKey>
                 | <SessionClaim>
                 | <LinkBack>
                 | <BaseURL>
                 | error message [(key | field name | stack trace)]
                   // valid with InvokeRule 'resume on error' only
                 | page number // valid in Page Header or Page Footer only
                 | <NavigationName> as pdf [in (portrait | landscape)] [font offset is <Number>]
                   // default is portrait
                 | <PeriodLabel>
                 | <CreateOrUpdateMode>
                 | <TenantID>
                 | <LogicalID>
                 | <DocumentLocation>
                 | <TotalVar>
                 | <TimeZoneVar>)
```

## Session References

### Session Claim
```
SessionClaim ::= session.claim.<Literal>
```

Allows reference to a session claim. Session claims are typically Security Claims set on the session context.

### Session Key
```
SessionKey ::= session.key.<FieldName>
```

Allows reference to a session key. Session keys are high level KeyField values typically tied to the invocation of a WebApp by specifying them on the url (e.g. `session.key.SupplierGroup=8181`).

## Select Statements

### Select Statement
```
SelectStatement ::= <Message>
```

- If the field is an **XMLDocument**, the SelectStatement is interpreted as **XPath**
- If the field is a **JSONObject**, the SelectStatement is interpreted as **JSON**
- If the field is any other type, the SelectStatement is treated as a **Regular Expression**

### Select Line Statement
```
SelectLineStatement ::= that (starts with | contains | ends with) <Message>
```

## Tenant ID

```
TenantID ::= tenant id
```

## Total Variable

```
TotalVar ::= ( total text
               // references "sub-total text is" and "grand total text is" from ListDefinition
               // used on print total form
             | total key[.representative text])
               // can be used in "sub-total text is" and "grand total text is" <Message> 
               // in ListDefinition
```

## URL Variable

```
URLVar ::= url.<Literal>
```

Allows reference to a url variable. These variables are available when:
- The flag `byw` is set to true on the url (`…&byw=true&` - automatically done on a linkback), or
- When they are individually specified in the WebApp definition as a URL Parameter

## Special Context References

### Error Message Components
Available with InvokeRule 'resume on error' only:
- `error message` - Full error message
- `error message key` - Error key
- `error message field name` - Field name causing error
- `error message stack trace` - Stack trace

### Page Context
Valid in Page Header or Page Footer only:
- `page number` - Current page number

### Special Instance References
- `this instance` - Current instance
- `related` - Valid within a Relation Definition
- `from` - Valid within a where condition on an OTM DataLink
- `each[<Parens><LPLConstructName><Parens>]` - Valid within a for each loop
- `invoked` - Valid within an invocation
- `result` - Valid within an invocation
- `child` - Valid within Parent Attach Rules

### Audit Log
```
audit log records [descending]
```
Retrieves the set of past and future (effective-dated) records.
