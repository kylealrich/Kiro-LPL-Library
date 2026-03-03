# Fields and Values

## Special Field References

### BOD ID
```
BODId ::= bod id
```

### Business Object Reference
```
BusinessObjectReference ::= <FieldName>
// Field that is of type BusinessObjectReference
```

### Current Async Action Fields
```
CurrentAsyncId ::= current async action request id
// valid only when in a background action

CurrentActionBackgroundGroupId ::= current action background group id
// valid only when in a background action
```

### Document Location
```
DocumentLocation ::= document location.(Local | AWSS3 | External)
// State values for 'document location' Type
```

## Field Name Definitions

### Operator Field or Array Name
```
OperatorFieldOrArrayName ::= ( <FieldName>
                             | <ArrayComponent>)
```

### Full Field Name
```
FullFieldName ::= ( <FieldOrArrayName>[.<FieldOrArrayName>…]
                  | <CreateStamp>
                  | <CreateDate>
                  | <UpdateStamp>
                  | <RelevanceScore>
                  | <ActionAttribute>
                  | <BODId>
                  | <CurrentAsyncId>
                  | <CurrentActionBackgroundGroupId>
                  | has future changes
                    // Boolean field; returns true if future-dated records exist
                  | user fields[<Parens><BusinessClass><Parens>]
                    // references the set of user fields that have been defined in this 
                    // context or the explicitly given BusinessClass context)
```

## Labels and Formatting

### Default Label
```
DefaultLabel ::= default label is (<LiteralMessage> | untranslatable)
```

- **untranslatable**: Marks the base translation text as untranslatable
- This overrides the default base label translation text
- The default base label is the LPLConstructName with spaces inserted before each uppercase character

**IMPORTANT: Label Syntax Rules**

Labels in LPL do NOT accept spaces directly in the literal string. Use one of these approaches:

1. **Use `untranslatable` alone (Recommended for simple cases):**
   ```
   UserID is Alpha size 20
       default label is untranslatable
   ```
   LPL automatically converts `UserID` to "User ID" by inserting spaces before uppercase letters.

2. **Use `untranslatable:"Label_Text"` with underscores:**
   ```
   Email is Alpha size 255
       default label is untranslatable:"Email_Address"
   ```
   Use underscores instead of spaces. LPL converts underscores to spaces in the UI, displaying as "Email Address".

3. **Use message IDs without spaces (auto-conversion):**
   ```
   default label is "UserID"
   ```
   LPL automatically inserts spaces before uppercase letters, displaying as "User ID".

**Examples:**

Simple field with automatic conversion:
```
CreatedDate is TimeStamp
    default label is untranslatable
```
Displays as: "Created Date"

Field with custom label using underscores:
```
Email is Alpha size 255
    default label is untranslatable:"Email_Address"
```
Displays as: "Email Address"

Action with custom label:
```
Create is a Create Action
    default label is untranslatable:"Create_User"
    completion message is untranslatable:"User_<UserID>_created_successfully"
```
Displays as: "Create User" and "User 123 created successfully"

### Java Format
```
JavaFormat ::= '"'(<Literal> | '<'<RelatedValue>'>')…'"'
```

Must resolve to a JavaFormat as defined in:
- [Java Formatter Documentation](http://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html)
- See also: [Regular Expressions on Wikipedia](https://en.wikipedia.org/wiki/Regular_expression)

## Relevance Score
```
RelevanceScore ::= relevance score
```

Returns the relevance score from a text search. Valid only on a List Definition.

## States Definition
```
States ::= States<StateName> value is <Literal> [with <Icon>][<DefaultLabel>]
```

## Value Types
```
Value ::= ( <Text>
          | <Number>
          | <#> [(year[s] | month[s] | day[s])]
          | <Percent>
          | <Constant>
          | <ActionTagValue>
          | <StateFieldName>)
```

### Numeric Values with Time Units
Numeric values can be suffixed with time units:
- `year` or `years`
- `month` or `months`
- `day` or `days`

Example: `30 days`, `12 months`, `1 year`

## Special System Fields

### Timestamp Fields
- **CreateStamp**: Timestamp when the record was created
- **CreateDate**: Date when the record was created
- **UpdateStamp**: Timestamp when the record was last updated

### Future Changes
```
has future changes
```
Boolean field that returns `true` if future-dated records exist for the current record.

### User Fields
```
user fields[<Parens><BusinessClass><Parens>]
```
References the set of user fields that have been defined in:
- The current context, or
- The explicitly given BusinessClass context

## Background Action Context

The following fields are valid only when executing in a background action:
- `current async action request id`
- `current action background group id`

These provide context about the currently executing asynchronous operation.
