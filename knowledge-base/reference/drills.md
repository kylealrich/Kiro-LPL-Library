# Drills

## Overview

Drills define navigation paths that allow users to navigate from one business object to related objects or views. They provide contextual navigation, enabling users to explore related data, view details, and access associated forms and lists. Drills are a fundamental part of the user interface navigation model.

## Drill Types

### Drill Backs

Drill backs provide navigation paths back to parent or originating objects.

### Drills

Standard drills navigate to related objects, forms, lists, or views.

### Drill Lists

Drill lists group multiple drill definitions together for organized navigation.

## Syntax

### Drill Back Definition

```
DrillBackDefinition ::=
    <LinkBack>
```

Defines a drill-back navigation using a LinkBack.

### Drill Definition

```
DrillDefinition ::=
    <DrillName> [is a[n] <NavigationName>]
    [valid when <SimpleCondition>]
    [(<Page>
     | <Form>
       [(restrict | enable) action [<StateName>.]<ActionName>…]
       [<FormDefinition>]
     | <List>
       [position to <RelatedLink>]
     | <Template>
     | view <RelatedField>)]
```

**Navigation Reference:**
- If `is a <NavigationName>` is specified, drill takes on Navigation definition
- If `<DrillName>` is itself a NavigationName, drill uses Navigation definition
- Navigation definition cannot be overridden in these cases

**Drill Targets:**
- **Page** - Navigate to a page
- **Form** - Display a form
- **List** - Display a list
- **Template** - Use template-driven UI
- **View** - View a document or business object reference

### Drill List Definition

```
DrillListDefinition ::=
    <DrillListName> is a DrillList
        (<DrillDefinition> | primary audit list | data translation list)
        …
```

Groups multiple drills into a named drill list.

**Special Drill Types:**
- `primary audit list` - Includes primary audit log
- `data translation list` - Includes data translation interface

## Drill Components

### Drill Name

Unique identifier for the drill, following LPL naming conventions.

### Navigation Reference

```
<DrillName> is a[n] <NavigationName>
```

Associates drill with an existing navigation definition.

**Behavior:**
- Drill inherits navigation definition
- Cannot override navigation properties
- Simplifies drill definition

### Conditional Visibility

```
valid when <SimpleCondition>
```

Controls when drill is available based on runtime conditions.

### Page Drill

```
<Page>
```

Navigates to a specific page.

**Syntax:**
```
page is <PageName>[.<PanelName>]
```

### Form Drill

```
<Form>
    [(restrict | enable) action [<StateName>.]<ActionName>…]
    [<FormDefinition>]
```

Displays a form for viewing or editing.

**Action Control:**
- `restrict action` - Disables specific actions on the form
- `enable action` - Enables specific actions on the form
- Can specify state-qualified actions

**Inline Form Definition:**
- Can include complete form definition
- Useful for drill-specific form layouts

### List Drill

```
<List>
    [position to <RelatedLink>]
```

Displays a list of related records.

**Position To:**
- Positions list to specific record
- `<RelatedLink>` must be a record within the list
- Useful for highlighting specific items

### Template Drill

```
<Template>
```

Uses template-driven UI for creating instances.

### View Drill

```
view <RelatedField>
```

Views a document or business object reference.

**Field Types:**
- **BinaryDocument** - Displays document
- **BusinessObjectReference** - Displays primary form or valid drill target forms

## Examples

### Example 1: Simple Drill to Form

```
CUSTOMER_DETAIL
    form is customer.CUSTOMER_FORM
```

Drills to customer detail form.

### Example 2: Drill with Navigation Reference

```
VIEW_ORDERS is a ORDER_NAVIGATION
```

Drill uses existing ORDER_NAVIGATION definition.

### Example 3: Conditional Drill

```
EDIT_ORDER
    valid when order.status = "Draft"
    form is order.ORDER_FORM
```

Drill available only for draft orders.

### Example 4: Drill to List

```
ORDER_LINES
    list is order.line set.ORDER_LINE_LIST
```

Drills to list of order lines.

### Example 5: Drill with Position

```
CURRENT_LINE
    list is order.line set.ORDER_LINE_LIST
        position to current.line
```

Drills to list and positions to current line.

### Example 6: Drill with Action Restrictions

```
VIEW_ONLY_FORM
    form is document.DOCUMENT_FORM
        restrict action EDIT
        restrict action DELETE
```

Drills to form with edit and delete disabled.

### Example 7: Drill with Action Enablement

```
APPROVAL_FORM
    form is request.REQUEST_FORM
        enable action APPROVE
        enable action REJECT
```

Drills to form with specific actions enabled.

### Example 8: View Document Drill

```
VIEW_ATTACHMENT
    view attachment.document
```

Views a binary document attachment.

### Example 9: View Business Object Reference

```
VIEW_RELATED_OBJECT
    view related.object.reference
```

Views business object through reference field.

### Example 10: Drill to Page

```
DASHBOARD
    page is MAIN_DASHBOARD.SUMMARY_PANEL
```

Drills to specific page and panel.

### Example 11: Template Drill

```
CREATE_FROM_TEMPLATE
    template is template.definition
        child designator field is template.type
        parent match field is record.type
        target business class is TARGET_CLASS
        form is TEMPLATE_FORM
```

Uses template-driven creation.

### Example 12: Drill List

```
ORDER_DRILLS is a DrillList
    primary audit list
    ORDER_LINES
    ORDER_HISTORY
    CUSTOMER_INFO
    SHIPPING_ADDRESS
```

Groups multiple drills into organized list.

### Example 13: Conditional Drill List

```
ADMIN_DRILLS is a DrillList
    primary audit list
    
    EDIT_CONFIG
        valid when current.user.has.permission("Admin")
        form is CONFIG_FORM
    
    VIEW_LOGS
        valid when current.user.has.permission("Admin")
        list is system.log set.LOG_LIST
```

Drill list with conditional drills.

### Example 14: State-Specific Action Control

```
APPROVAL_DRILL
    form is request.REQUEST_FORM
        restrict action DRAFT.SUBMIT
        enable action PENDING.APPROVE
        enable action PENDING.REJECT
```

Controls actions based on state.

### Example 15: Inline Form Definition

```
QUICK_VIEW
    form is order.INLINE_FORM
        ORDER_NUMBER
        CUSTOMER
        ORDER_DATE
        TOTAL_AMOUNT
```

Drill with inline form definition.

### Example 16: Multiple Drill Backs

```
Drill Backs
    linkback "Return to Customer" to customer.CUSTOMER_FORM
    linkback "Return to Order List" to order set.ORDER_LIST
```

Multiple drill-back options.

### Example 17: Data Translation Drill

```
TRANSLATION_DRILLS is a DrillList
    data translation list
    FIELD_TRANSLATIONS
    MESSAGE_TRANSLATIONS
```

Includes data translation interface.

### Example 18: Drill with Complex Condition

```
MANAGER_APPROVAL
    valid when (status = "Pending") and 
               (amount > 1000) and
               (current.user = manager)
    form is approval.APPROVAL_FORM
```

Drill with multiple conditions.

### Example 19: Drill to Aggregation

```
SALES_SUMMARY
    list is row(SALES_BY_REGION).SUMMARY_LIST
```

Drills to aggregated data list.

### Example 20: Comprehensive Drill List

```
DOCUMENT_DRILLS is a DrillList
    primary audit list
    data translation list
    
    VIEW_DOCUMENT
        view document.file
    
    EDIT_METADATA
        valid when document.status = "Draft"
        form is document.METADATA_FORM
    
    VERSION_HISTORY
        list is document.version set.VERSION_LIST
    
    RELATED_DOCUMENTS
        list is related.document set.DOCUMENT_LIST
            position to current.document
    
    APPROVAL_WORKFLOW
        valid when document.requires.approval
        form is approval.WORKFLOW_FORM
            restrict action CANCEL
```

Comprehensive drill list with various drill types.

## Best Practices

1. **Organize Drills Logically**
   - Group related drills
   - Use drill lists for organization
   - Order by frequency of use
   - Provide intuitive navigation

2. **Use Meaningful Names**
   - Clear, descriptive drill names
   - Indicate drill target
   - Follow naming conventions
   - Be consistent

3. **Leverage Navigation References**
   - Reuse navigation definitions
   - Maintain consistency
   - Simplify drill definitions
   - Reduce duplication

4. **Apply Conditional Visibility**
   - Show drills when relevant
   - Hide unavailable drills
   - Check permissions
   - Validate state

5. **Control Form Actions**
   - Restrict inappropriate actions
   - Enable necessary actions
   - Consider state context
   - Test action availability

6. **Position Lists Appropriately**
   - Use position to for context
   - Highlight relevant records
   - Improve user experience
   - Test positioning

7. **Include Standard Drills**
   - Primary audit list
   - Data translation (when applicable)
   - Common related objects
   - Frequently accessed data

8. **Test Navigation Paths**
   - Verify all drills work
   - Test conditional visibility
   - Check action restrictions
   - Validate positioning

## Drill List Behavior

### Automatic Drill List

When no Drill List is specified in UI definition:
- Automatically includes non-drill-restricted navigations
- Includes lists propagated as drills
- Includes forms propagated as drills
- System maintains automatically

### Manual Drill List

When Drill List is specified in UI definition:
- Overrides automatic propagation
- Must explicitly list all drills
- Provides full control
- Requires maintenance

### Special Drill Types

**Primary Audit List:**
- Includes audit log for record
- Standard drill for audited objects
- Shows change history

**Data Translation List:**
- Includes translation interface
- For translatable fields
- Manages multi-language data

## Drill vs Navigation

| Aspect | Drill | Navigation |
|--------|-------|------------|
| Purpose | UI navigation path | Logical relationship |
| Definition | UI-specific | Business logic |
| Location | UI definition file | Business class file |
| Reusability | Can reference navigation | Reusable across drills |

Drills can reference navigations to inherit their definitions.

## Troubleshooting

### Issue: Drill not appearing

**Possible Causes:**
- Conditional visibility false
- Drill list doesn't include it
- Navigation restricted
- Permission denied

**Solutions:**
- Check condition logic
- Verify drill list inclusion
- Review navigation restrictions
- Check user permissions

### Issue: Drill goes to wrong target

**Possible Causes:**
- Incorrect related link
- Wrong form/list reference
- Navigation definition issue
- Context problem

**Solutions:**
- Verify related link
- Check form/list names
- Review navigation definition
- Validate context

### Issue: Actions not restricted/enabled

**Possible Causes:**
- Incorrect action name
- State not specified correctly
- Action not on form
- Syntax error

**Solutions:**
- Verify action names
- Check state qualification
- Confirm action exists
- Review syntax

### Issue: Position to not working

**Possible Causes:**
- Related link not in list
- Incorrect reference
- List not loaded
- Timing issue

**Solutions:**
- Verify record in list
- Check related link
- Ensure list loads first
- Test with various data

### Issue: View drill fails

**Possible Causes:**
- Field not BinaryDocument or BusinessObjectReference
- Document not available
- Reference null
- Permission issue

**Solutions:**
- Verify field type
- Check document exists
- Validate reference
- Check permissions

## Related Concepts

- [UI Definition](ui-definition.md) - Overall UI structure
- [UI Base Definitions](ui-base-definitions.md) - UI constructs
- [Relations](relations.md) - Business relationships
- [Actions](actions.md) - Action definitions
- [Forms](matrix-forms.md) - Form definitions

## Notes

- Drills provide contextual navigation in UI
- Can reference existing navigation definitions
- Conditional visibility controls drill availability
- Drill lists organize multiple drills
- Can restrict or enable specific actions on forms
- Position to highlights specific records in lists
- View drills work with BinaryDocument and BusinessObjectReference fields
- Primary audit list is standard drill for audited objects
- Data translation list enables multi-language management
- Automatic drill list generation can be overridden
- Drill names should be descriptive and meaningful
- Test all drill paths thoroughly
- Consider user workflow when organizing drills
- Use drill lists to group related navigation paths
- Drills are defined in UI definition files, not business class files
