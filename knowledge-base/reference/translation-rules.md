# Translation Rules

## Overview

Translation Rules define business logic that executes when any translatable field's translation is changed. These rules fire for all translation operations (create, update, and delete) across all translatable fields in the business class, providing a centralized location for translation management logic.

## Syntax

```
Translation Rules
    <Rule>
    …
```

## When Translation Rules Fire

Translation Rules execute:
- **When any translation is created** - For any translatable field
- **When any translation is updated** - For any translatable field
- **When any translation is deleted** - For any translatable field
- **Across all translatable fields** - Not specific to one field

## Execution Context

Translation Rules run when:
- User creates a new translation
- User updates an existing translation
- User deletes a translation
- System processes translation changes
- Translation import/export operations occur

## Translation Rules vs Translation Field Rules

| Aspect | Translation Rules | Translation Field Rules |
|--------|------------------|------------------------|
| Scope | All translatable fields | Specific field |
| Trigger | Any field translation change | Specific field translation change |
| Use Case | Global translation logic | Field-specific translation logic |
| Granularity | Business class level | Field level |

## Use Cases

### Translation Quality Management
- Validate translation completeness
- Check translation quality
- Enforce translation standards
- Monitor translation coverage

### Translation Workflow
- Route to translators
- Trigger review processes
- Manage approval workflows
- Track translation status

### Translation Audit
- Log all translation changes
- Track translator activity
- Monitor translation history
- Create audit trail

### Translation Synchronization
- Update related translations
- Maintain consistency
- Propagate changes
- Sync with external systems

### Translation Metrics
- Track translation coverage
- Measure completion rates
- Monitor translation activity
- Generate reports

## Examples

### Example 1: Log All Translation Changes

```
Translation Rules
    log "Translation changed - Field: " + translated.field.name + 
        " Language: " + language.code + " User: " + current.user
    
    translation.log.count += 1
    last.translation.change = today
```

Logs all translation activity.

### Example 2: Track Translation Coverage

```
Translation Rules
    if translation.text not empty
        increment translation.completed.count
    else
        decrement translation.completed.count
    
    translation.coverage.percent = 
        (translation.completed.count / translation.total.count) * 100
```

Calculates translation coverage percentage.

### Example 3: Validate Translation Quality

```
Translation Rules
    if translation.text not empty
        constraint length(translation.text) >= minimum.length
            "Translation too short for field " + translated.field.name
        
        constraint translation.text not = base.text
            "Translation should not be identical to base text"
```

Enforces translation quality standards.

### Example 4: Trigger Review Workflow

```
Translation Rules
    if critical.field.translated and translation.text not empty
        initiate TRANSLATION_REVIEW workflow
            Variables
                field.name = translated.field.name
                language.code
                translation.text
                translator = current.user
```

Routes critical translations for review.

### Example 5: Update Translation Status

```
Translation Rules
    if all.required.translations.complete
        record.translation.status = "Complete"
    else if some.translations.exist
        record.translation.status = "Partial"
    else
        record.translation.status = "Not Translated"
```

Maintains overall translation status.

### Example 6: Notify Translation Team

```
Translation Rules
    if translation.text not empty and language.code in priority.languages
        send notification
            to translation.coordinator
            description is "Priority language translation added"
            detail is "Field: " + translated.field.name + 
                     " Language: " + language.code
```

Alerts team of priority language translations.

### Example 7: Validate Required Languages

```
Translation Rules
    if record.status = "Published"
        for each required.language set
            constraint translation.exists(translated.field.name, each.language.code)
                "Translation required for " + each.language.code + 
                " before publishing"
```

Ensures required translations exist before publishing.

### Example 8: Track Translator Activity

```
Translation Rules
    translator.translation.count += 1
    translator.last.translation.date = today
    
    if translator.translation.count >= milestone.threshold
        send notification
            to translation.manager
            description is "Translator milestone reached"
            detail is current.user + " completed " + 
                     translator.translation.count + " translations"
```

Monitors translator productivity.

### Example 9: Synchronize Translation Cache

```
Translation Rules
    clear in-memory cache for TRANSLATIONS
    
    if external.cache.enabled
        invoke UPDATE_EXTERNAL_CACHE
            invoked.field.name = translated.field.name
            invoked.language.code = language.code
            invoked.translation.text = translation.text
```

Refreshes translation caches.

### Example 10: Validate Character Encoding

```
Translation Rules
    if translation.text not empty
        constraint valid.character.encoding(translation.text, language.code)
            "Invalid characters for language " + language.code
        
        constraint no.control.characters(translation.text)
            "Translation contains invalid control characters"
```

Ensures proper character encoding.

### Example 11: Update Translation Metrics

```
Translation Rules
    if translation.text not empty
        translation.word.count = count.words(translation.text)
        translation.character.count = length(translation.text)
        
        if translation.word.count > field.max.words
            translation.requires.review = true
```

Calculates and validates translation metrics.

### Example 12: Manage Translation Versions

```
Translation Rules
    if translation.text.changed
        invoke CREATE.TRANSLATION_HISTORY
            invoked.field.name = translated.field.name
            invoked.language.code = language.code
            invoked.old.text = previous.translation.text
            invoked.new.text = translation.text
            invoked.changed.by = current.user
            invoked.change.date = today
```

Maintains translation version history.

### Example 13: Validate Placeholder Consistency

```
Translation Rules
    if translation.text not empty and base.text.has.placeholders
        constraint translation.has.same.placeholders(base.text, translation.text)
            "Translation must contain same placeholders as base text"
```

Ensures placeholders are preserved in translations.

### Example 14: Route to Qualified Translators

```
Translation Rules
    if translation.text empty and translation.requested
        for each qualified.translator set
            if each.languages includes language.code
                send notification
                    to each.translator
                    description is "Translation request"
                    detail is "Field: " + translated.field.name + 
                             " Language: " + language.code
```

Notifies qualified translators of translation needs.

### Example 15: Compliance Tracking

```
Translation Rules
    if compliance.relevant.field
        invoke CREATE.COMPLIANCE_LOG
            invoked.action = "Translation Change"
            invoked.field.name = translated.field.name
            invoked.language.code = language.code
            invoked.user = current.user
            invoked.timestamp = today
```

Tracks translation changes for compliance.

### Example 16: Update Search Index

```
Translation Rules
    if searchable.field.translated
        build text search field MULTILINGUAL_SEARCH_INDEX
            Fields
            all.translated.fields
```

Rebuilds multilingual search index.

### Example 17: Validate Translation Length

```
Translation Rules
    if translation.text not empty
        if field.max.length not empty
            constraint length(translation.text) <= field.max.length
                "Translation exceeds maximum length for " + 
                translated.field.name
```

Enforces field length constraints.

### Example 18: Manage Translation Approval

```
Translation Rules
    if translation.text not empty and requires.approval
        translation.approval.status = "Pending"
        translation.submitted.date = today
        translation.submitted.by = current.user
        
        send notification
            to translation.approver
            description is "Translation approval required"
```

Initiates translation approval process.

### Example 19: Detect Machine Translation

```
Translation Rules
    if translation.source = "Machine"
        translation.requires.human.review = true
        translation.confidence.score = machine.confidence
        
        if translation.confidence.score < minimum.confidence
            translation.approval.required = true
```

Flags machine translations for review.

### Example 20: Update Translation Statistics

```
Translation Rules
    record.last.translation.update = today
    record.translation.update.count += 1
    
    if language.code in primary.languages
        record.primary.language.coverage += 1
```

Maintains translation statistics.

## Best Practices

1. **Use for Global Translation Logic**
   - Logic that applies to all translatable fields
   - Cross-field translation validation
   - Translation workflow management
   - Overall translation tracking

2. **Validate Translation Quality**
   - Check completeness
   - Validate format
   - Verify character encoding
   - Ensure placeholder consistency

3. **Track Translation Activity**
   - Log all changes
   - Monitor coverage
   - Track translator activity
   - Measure completion rates

4. **Manage Translation Workflow**
   - Route to qualified translators
   - Trigger review processes
   - Manage approvals
   - Track status

5. **Maintain Translation Consistency**
   - Synchronize related translations
   - Update caches
   - Propagate changes
   - Maintain terminology

6. **Keep Rules Efficient**
   - Minimize processing
   - Avoid heavy calculations
   - Use efficient queries
   - Consider performance impact

7. **Document Translation Policies**
   - Explain validation rules
   - Note workflow processes
   - Describe quality standards
   - Document requirements

## Common Patterns

### Logging Pattern
```
Translation Rules
    log "Translation: " + translated.field.name + 
        " Language: " + language.code
```

### Coverage Tracking Pattern
```
Translation Rules
    if translation.text not empty
        increment translation.count
    translation.coverage = calculate.coverage
```

### Validation Pattern
```
Translation Rules
    if translation.text not empty
        constraint <quality.check>
            <error.message>
```

### Workflow Pattern
```
Translation Rules
    if <trigger.condition>
        initiate <translation.workflow> workflow
            Variables
                translated.field.name
                language.code
```

### Notification Pattern
```
Translation Rules
    send notification
        to <translation.team>
        description is "Translation change"
```

## Available Context

Within Translation Rules, you have access to:

- `translated.field.name` - Name of the field being translated
- `language.code` - Language of the translation
- `translation.text` - The translated text value
- `previous.translation.text` - Previous translation value
- `base.text` - Base language text
- `current.user` - User making the change
- All business class fields

## Troubleshooting

### Issue: Translation Rules not firing

**Possible Causes:**
- Field not configured as translatable
- Translation system not enabled
- Syntax errors in rules
- Rules in wrong section

**Solutions:**
- Verify field translation configuration
- Check translation system settings
- Validate rule syntax
- Verify rule location

### Issue: Performance degradation

**Possible Causes:**
- Heavy processing on every translation
- Complex validation logic
- Inefficient queries
- Cache invalidation overhead

**Solutions:**
- Optimize validation logic
- Use efficient queries
- Minimize processing
- Review caching strategy

### Issue: Translation validation failing

**Possible Causes:**
- Validation too strict
- Character encoding issues
- Placeholder mismatch
- Length constraints

**Solutions:**
- Review validation rules
- Check character encoding
- Verify placeholder logic
- Adjust length limits

### Issue: Workflow not triggering

**Possible Causes:**
- Condition not met
- Workflow configuration
- Permission issues
- Missing required data

**Solutions:**
- Review trigger conditions
- Check workflow setup
- Verify permissions
- Validate required data

## Related Concepts

- [Translation Field Rules](translation-field-rules.md) - Field-specific translation rules
- [Messages](messages.md) - Message localization and translation
- [Field Rules](field-rules.md) - General field rules
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Update Action Rules](update-action-rules.md) - Update logic

## Notes

- Translation Rules fire for all translatable fields in the business class
- Rules execute on create, update, and delete of translations
- Use Translation Rules for global translation management logic
- Use Translation Field Rules for field-specific translation logic
- Translation Rules have access to translation-specific context
- Rules fire per translation operation, not per field value change
- Consider performance impact of translation validation
- Keep rules efficient to avoid slowing translation operations
- Test with various languages and character sets
- Document translation quality standards
- Validate translations before saving to avoid errors
- Use appropriate workflow for translation review processes
- Track translation coverage and completion metrics
- Maintain translation audit trail for compliance
- Consider caching implications of translation changes
