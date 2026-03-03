# Translation Field Rules

## Overview

Translation Field Rules define business logic that executes when a field's translation is changed. These rules fire during translation create, update, and delete operations, enabling validation, defaulting, and side effects specific to multi-language field management.

## Syntax

```
[(merge | replace) specifications]
<FieldRules>
…
```

**Merge/Replace Specifications:**
- Valid only for LHF (Landmark Hosted Framework) LPL
- `merge` - Merges specifications with base (default behavior)
- `replace` - Replaces base specifications entirely

## When Rules Fire

Translation field rules execute when:
- A translation is created for the field
- An existing translation is updated
- A translation is deleted

These rules are separate from standard field rules and specifically handle translation-related events.

## Rule Structure

```
<FullFieldName>
    <Rule>
    …
```

The field name identifies which field's translations trigger these rules. Rules can include any valid field rule types.

## Use Cases

### Translation Validation
- Ensure translation completeness
- Validate translation format
- Check translation length constraints
- Verify required translations exist

### Translation Synchronization
- Update related translations
- Maintain translation consistency
- Propagate translation changes
- Synchronize across language variants

### Translation Audit
- Log translation changes
- Track translation history
- Record translation authors
- Monitor translation quality

### Translation Workflow
- Trigger translation review
- Initiate approval processes
- Notify translators
- Update translation status

## Examples

### Example 1: Validate Translation Length

```
PRODUCT_NAME
    constraint length(translation.text) <= 100
        "Product name translation cannot exceed 100 characters"
```

Ensures translated product names don't exceed maximum length.

### Example 2: Required Translation

```
DESCRIPTION
    if language.code in ("EN", "FR", "DE")
        constraint translation.text not empty
            "Translation required for English, French, and German"
```

Enforces required translations for specific languages.

### Example 3: Translation Audit

```
LABEL_TEXT
    log "Translation changed for " + field.name + " in " + language.code
    translation.modified.date = today
    translation.modified.by = current.user
```

Logs and tracks translation modifications.

### Example 4: Synchronize Related Translations

```
CATEGORY_NAME
    if translation.text not empty
        for each related.product set
            if each.category.name.translation empty
                each.category.name.translation = translation.text
```

Propagates category name translations to related products.

### Example 5: Translation Status Management

```
HELP_TEXT
    if translation.text not empty
        translation.status = "Pending Review"
    else
        translation.status = "Not Translated"
```

Updates translation status based on content.

### Example 6: Trigger Translation Review

```
LEGAL_DISCLAIMER
    if translation.text changed
        send notification
            to translation.reviewer
            description is "Legal disclaimer translation requires review"
            priority is high
```

Notifies reviewers when critical translations change.

### Example 7: Translation Completeness Check

```
PRODUCT_DESCRIPTION
    if translation.text not empty
        increment translation.count
        if translation.count = total.languages
            product.translation.complete = true
```

Tracks translation completeness across languages.

### Example 8: Format Validation

```
FORMATTED_MESSAGE
    if translation.text not empty
        constraint translation.text matches pattern
            "Translation must contain required placeholders"
```

Validates translation format and placeholders.

### Example 9: Translation Inheritance

```
CATEGORY_LABEL
    if translation.text empty and parent.category not empty
        default translation.text to parent.category.label.translation
```

Inherits translations from parent categories when not specified.

### Example 10: Translation Workflow

```
MARKETING_CONTENT
    if translation.text not empty
        if translation.word.count > 500
            initiate TRANSLATION_REVIEW workflow
                Variables
                    field.name
                    language.code
                    translation.text
```

Initiates review workflow for lengthy translations.

## Best Practices

1. **Validate Translation Quality**
   - Check length constraints
   - Validate format and placeholders
   - Ensure completeness
   - Verify character encoding

2. **Maintain Consistency**
   - Synchronize related translations
   - Apply consistent terminology
   - Preserve formatting
   - Handle special characters

3. **Track Changes**
   - Log translation modifications
   - Record translator information
   - Maintain translation history
   - Monitor quality metrics

4. **Handle Missing Translations**
   - Provide fallback logic
   - Use default language
   - Notify translators
   - Track translation gaps

5. **Performance Considerations**
   - Minimize cascading updates
   - Batch translation operations
   - Cache frequently-used translations
   - Optimize translation queries

6. **Workflow Integration**
   - Trigger appropriate reviews
   - Route to qualified translators
   - Track approval status
   - Manage translation lifecycle

7. **Error Handling**
   - Validate before saving
   - Provide clear error messages
   - Handle encoding issues
   - Manage concurrent updates

## Common Patterns

### Validation Pattern
```
<FieldName>
    constraint <ValidationCondition>
        <ValidationMessage>
```

### Audit Pattern
```
<FieldName>
    translation.modified.date = today
    translation.modified.by = current.user
    log "Translation updated"
```

### Synchronization Pattern
```
<FieldName>
    for each related.object set
        each.related.field.translation = translation.text
```

### Workflow Pattern
```
<FieldName>
    if <TriggerCondition>
        initiate <WorkflowName> workflow
            Variables
                translation.text
                language.code
```

## Translation Context

Within translation field rules, you have access to:

- `translation.text` - The translated text value
- `language.code` - The language of the translation
- `field.name` - The field being translated
- `current.user` - User making the change
- Standard field rule context

## Differences from Standard Field Rules

| Aspect | Translation Field Rules | Standard Field Rules |
|--------|------------------------|---------------------|
| Trigger | Translation change | Field value change |
| Context | Translation-specific | General field context |
| Scope | Per language | All languages |
| Use Case | Multi-language management | General validation |
| Frequency | Per translation operation | Per field operation |

## Integration with Translation System

Translation field rules work with the Landmark translation system:

1. **Translation Tables** - Rules access translation data structures
2. **Language Codes** - Rules can reference language identifiers
3. **Translation Status** - Rules can update translation workflow status
4. **Translation Cache** - Rules should consider caching implications

## Troubleshooting

### Issue: Rules not firing on translation change

**Possible Causes:**
- Rules defined in wrong section
- Field not configured for translation
- Translation system not enabled
- Syntax errors in rules

**Solutions:**
- Verify rules in Translation Field Rules section
- Check field translation configuration
- Confirm translation feature enabled
- Validate rule syntax

### Issue: Performance degradation

**Possible Causes:**
- Cascading translation updates
- Complex validation logic
- Inefficient queries
- Cache invalidation

**Solutions:**
- Minimize cascading updates
- Optimize validation conditions
- Use efficient queries
- Review caching strategy

### Issue: Translation validation failing

**Possible Causes:**
- Incorrect validation logic
- Character encoding issues
- Length calculation errors
- Missing translation context

**Solutions:**
- Review validation conditions
- Check character encoding
- Verify length calculations
- Ensure context available

### Issue: Synchronization conflicts

**Possible Causes:**
- Concurrent translation updates
- Circular dependencies
- Transaction conflicts
- Locking issues

**Solutions:**
- Implement proper locking
- Avoid circular updates
- Handle conflicts gracefully
- Use appropriate isolation levels

## Related Concepts

- [Field Rules](field-rules.md) - Standard field rules
- [Messages](messages.md) - Message localization and translation
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Action Rules](action-rules.md) - Action-level rules
- [Conditions](conditions.md) - Boolean expressions

## Notes

- Translation field rules are specific to translation operations
- Rules fire on create, update, and delete of translations
- Merge/replace specifications valid only for LHF LPL
- Merge is the default behavior for specifications
- Rules have access to translation-specific context
- Consider performance impact of translation synchronization
- Validate translations before saving to avoid errors
- Use appropriate workflow for translation review processes
- Translation rules are separate from standard field rules
- Rules execute per language, not per field value change
