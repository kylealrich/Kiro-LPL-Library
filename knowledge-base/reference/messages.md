# Messages

## ISO Locale

```
IsoLocale ::= <Literal>
```

Must be a valid ISO Locale identifier (e.g., `en_US`, `fr_FR`, `de_DE`).

## Literal Message

```
LiteralMessage ::= '"'( <Literal> | '<'<RelatedValue>'>')…'"'
```

### Literal Message Syntax

Messages can contain:
- Plain text literals
- Dynamic values embedded with angle brackets `<RelatedValue>`

**IMPORTANT: Spaces in Messages**

When using literal messages for labels, completion messages, or error messages, **do NOT use spaces directly**. Instead:

1. **Use underscores** which LPL converts to spaces:
   ```
   untranslatable:"User_ID_is_required"
   ```
   Displays as: "User ID is required"

2. **Use PascalCase** which LPL auto-converts by inserting spaces before uppercase letters:
   ```
   "CompanyIsRequired"
   ```
   Displays as: "Company Is Required"

### Examples

**Without spaces (using underscores):**
```
untranslatable:"Email_Address_is_required"
```
Displays: **Email Address is required**

**Without spaces (using PascalCase auto-conversion):**
```
"CompanyIsRequired"
```
Displays: **Company Is Required**

**With dynamic values:**
```
untranslatable:"User_<UserID>_created_successfully"
```
Displays: **User 123 created successfully** (where UserID value is 123)

**Complex message with embedded values:**
```
"Company<Company>IsRequired"
```
Displays: **Company 123 Is Required** (where Company field value is 123)

### Dynamic Value Embedding

You can embed any `<RelatedValue>` within a message by wrapping it in angle brackets:

```
"Employee <Employee.Name> has been assigned to <Department.Name>"
```

This allows for dynamic, context-aware messages that include field values, calculations, or other related data.

## Message ID

```
MessageID ::= '"'( <Literal> | '<'<RelatedValue>'>')…'"'
```

Message IDs follow the same syntax as Literal Messages:

### Examples

```
"CompanyIsRequired"
```
Displays: **Company is required**

```
"Company<Company>IsRequired"
```
Displays: **Company 123 is required** (where Company field value is 123)

## Message

```
Message ::= ( [(untranslatable: | configuration:)]<LiteralMessage>]
            | <MessageID>)
            [<MessageTranslation>…]
```

### Message Prefixes

| Prefix | Description | Valid Context |
|--------|-------------|---------------|
| `untranslatable:` | Marks the message as not requiring translation | Valid in all LPL |
| `configuration:` | Marks the message as configuration-specific | Valid in a configuration only |

### Message Translation

When using `configuration:` prefix, you can provide translations:

```
[<MessageTranslation>…]
```

This is valid only when `configuration:` is used.

## Message Translation

```
MessageTranslation ::= translation for <IsoLocale> is <LiteralMessage>
```

Provides a translation of the message for a specific locale.

### Syntax

```
translation for <IsoLocale> is <LiteralMessage>
```

### Examples

#### Single Translation

```
"WelcomeMessage"
translation for fr_FR is "Bienvenue"
```

#### Multiple Translations

```
configuration:"WelcomeMessage"
translation for fr_FR is "Bienvenue"
translation for de_DE is "Willkommen"
translation for es_ES is "Bienvenido"
```

#### Translation with Dynamic Values

```
configuration:"EmployeeAssigned"
translation for en_US is "Employee <Employee.Name> assigned to <Department.Name>"
translation for fr_FR is "Employé <Employee.Name> affecté à <Department.Name>"
translation for de_DE is "Mitarbeiter <Employee.Name> zugewiesen zu <Department.Name>"
```

## Message Types and Usage

### Untranslatable Messages

Use when the message should not be translated (e.g., technical identifiers, codes):

```
untranslatable:"ERROR_CODE_123"
```

### Configuration Messages

Use in configurations when you want to provide custom translations:

```
configuration:"CustomValidationMessage"
translation for en_US is "The value must be greater than zero"
translation for fr_FR is "La valeur doit être supérieure à zéro"
```

### Standard Messages

Simple messages without special prefixes:

```
"RecordSavedSuccessfully"
```

## Best Practices

### Message Naming Conventions

- Use PascalCase for message IDs
- Make message IDs descriptive and self-documenting
- Example: `"CompanyIsRequired"` rather than `"Msg001"`

### Dynamic Value Placement

- Place dynamic values in angle brackets: `<FieldName>`
- Ensure the embedded values make sense in all translated versions
- Consider word order differences across languages

### Translation Guidelines

- Provide translations for all supported locales
- Test dynamic value placement in each language
- Consider cultural differences in messaging tone
- Keep messages concise and clear

## Common Locale Codes

| Locale Code | Language/Region |
|-------------|-----------------|
| `en_US` | English (United States) |
| `en_GB` | English (United Kingdom) |
| `fr_FR` | French (France) |
| `de_DE` | German (Germany) |
| `es_ES` | Spanish (Spain) |
| `it_IT` | Italian (Italy) |
| `pt_BR` | Portuguese (Brazil) |
| `ja_JP` | Japanese (Japan) |
| `zh_CN` | Chinese (Simplified, China) |
| `ko_KR` | Korean (Korea) |

## Examples in Context

### Validation Message

```
configuration:"QuantityMustBePositive"
translation for en_US is "Quantity must be greater than zero"
translation for fr_FR is "La quantité doit être supérieure à zéro"
translation for de_DE is "Die Menge muss größer als Null sein"
```

### Confirmation Message with Values

```
configuration:"RecordDeleted"
translation for en_US is "Record <RecordID> has been deleted"
translation for fr_FR is "L'enregistrement <RecordID> a été supprimé"
translation for de_DE is "Datensatz <RecordID> wurde gelöscht"
```

### Error Message

```
configuration:"InvalidDateRange"
translation for en_US is "End date <EndDate> must be after start date <StartDate>"
translation for fr_FR is "La date de fin <EndDate> doit être postérieure à la date de début <StartDate>"
```
