# Navigations

## Overview

Navigations define logical navigation paths between business objects and UI components. They establish relationships for user interface navigation, enabling users to move between related data, forms, lists, and views. Navigations can be referenced by drills and can automatically populate drill lists.

## Syntax

```
NavigationDefinition ::=
    <NavigationName> is a Navigation
    [label is <Message>]
    [drill restricted]
    [valid when <SimpleCondition>]
    [show as pdf [in (foreground | background)] [in (portrait | landscape)] [font offset is <Number>]]
    ( [<DisplayType>] <Page>
      [when <SimpleCondition>]
    | [<DisplayType>] <Form>
      [open action is <ActionName>]
      [(restrict | enable) action [<StateName>.]<ActionName>…]
      [<FormDefinition>]
      [when <SimpleCondition>]
    | [<DisplayType>] <List>
      [position to <RelatedLink>]
      [when <SimpleCondition>]
    | [<DisplayType>] <Template>
      [when <SimpleCondition>]
    | view <RelatedField>
      [when <SimpleCondition>])
    …
```

## Components

### Navigation Name

Unique identifier for the navigation, following LPL naming conventions (uppercase alphanumeric).

### Label

```
label is <Message>
```

Human-readable label for the navigation displayed in the UI.

### Drill Restricted

```
drill restricted
```

Prevents this navigation from being automatically included in the drill list.

**Use Cases:**
- Navigation not appropriate for drill list
- Internal navigation only
- Conditional navigation that shouldn't always appear
- Specialized navigation paths

### Conditional Visibility

```
valid when <SimpleCondition>
```

Controls when navigation is available based on runtime conditions.

### PDF Display

```
show as pdf [in (foreground | background)] [in (portrait | landscape)] [font offset is <Number>]
```

Displays navigation target as PDF.

**Execution Mode:**
- `foreground` - Generate PDF synchronously, then display (default)
- `background` - Generate PDF asynchronously for long-running reports

**Orientation:**
- `portrait` - Vertical orientation (default)
- `landscape` - Horizontal orientation

**Font Offset:**
- Adjusts font size for PDF generation
- Positive number increases size
- Negative number decreases size

**Use Cases:**
- Complex pages as PDFs
- Printable reports
- Long-running reports (use background)
- Document generation

## Navigation Targets

### Page Navigation

```
[<DisplayType>] <Page>
    [when <SimpleCondition>]
```

Navigates to a specific page.

**Display Type:**
- Optional device/version-specific targeting
- Examples: `browser`, `desktop`, `smartphone`, `tablet`

**Conditional:**
- Multiple page targets with conditions
- First matching condition used

### Form Navigation

```
[<DisplayType>] <Form>
    [open action is <ActionName>]
    [(restrict | enable) action [<StateName>.]<ActionName>…]
    [<FormDefinition>]
    [when <SimpleCondition>]
```

Navigates to a form.

**Open Action:**
- Specifies action to execute when opening form
- Useful for initialization

**Action Control:**
- `restrict action` - Disables specific actions
- `enable action` - Enables specific actions
- Can specify state-qualified actions

**Inline Form Definition:**
- Can include complete form definition
- Useful for navigation-specific layouts

**Conditional:**
- Multiple form targets with conditions
- Device-specific forms

### List Navigation

```
[<DisplayType>] <List>
    [position to <RelatedLink>]
    [when <SimpleCondition>]
```

Navigates to a list.

**Position To:**
- Positions list to specific record
- `<RelatedLink>` must be a record within the list
- Highlights or scrolls to record

**Conditional:**
- Multiple list targets with conditions
- Device-specific lists

### Template Navigation

```
[<DisplayType>] <Template>
    [when <SimpleCondition>]
```

Navigates to template-driven UI.

**Use Cases:**
- Template-based record creation
- Guided data entry
- Structured workflows

### View Navigation

```
view <RelatedField>
    [when <SimpleCondition>]
```

Views a document or business object reference.

**Field Types:**
- **BinaryDocument** - Displays document (PDF, image, etc.)
- **BusinessObjectReference** - Displays primary form or valid drill target forms

**Conditional:**
- Multiple view targets with conditions

## Multiple Targets

Navigations can have multiple targets with conditions:

```
CUSTOMER_VIEW is a Navigation
    smartphone form is customer.MOBILE_FORM
        when mobile.mode
    
    desktop form is customer.DESKTOP_FORM
        when desktop.mode
    
    form is customer.DEFAULT_FORM
```

First matching condition determines target.

## Examples

### Example 1: Simple Form Navigation

```
VIEW_CUSTOMER is a Navigation
    label is "View Customer"
    form is customer.CUSTOMER_FORM
```

Basic navigation to customer form.

### Example 2: Drill Restricted Navigation

```
INTERNAL_ADMIN is a Navigation
    label is "Admin Panel"
    drill restricted
    valid when current.user.has.permission("Admin")
    page is ADMIN_PAGE
```

Navigation not included in automatic drill list.

### Example 3: Conditional Navigation

```
EDIT_ORDER is a Navigation
    label is "Edit Order"
    valid when order.status = "Draft"
    form is order.ORDER_FORM
```

Navigation available only for draft orders.

### Example 4: PDF Report Navigation

```
SALES_REPORT is a Navigation
    label is "Sales Report"
    show as pdf in background in landscape
    page is SALES_REPORT_PAGE
```

Generates PDF report in background with landscape orientation.

### Example 5: Device-Specific Navigation

```
PRODUCT_CATALOG is a Navigation
    label is "Product Catalog"
    
    smartphone list is product set.MOBILE_LIST
    
    tablet list is product set.TABLET_LIST
    
    desktop list is product set.DESKTOP_LIST
```

Different lists for different devices.

### Example 6: Navigation with Action Control

```
VIEW_DOCUMENT is a Navigation
    label is "View Document"
    form is document.DOCUMENT_FORM
        restrict action EDIT
        restrict action DELETE
```

Navigation to read-only form.

### Example 7: Navigation with Open Action

```
CREATE_ORDER is a Navigation
    label is "Create Order"
    form is order.ORDER_FORM
        open action is INITIALIZE_ORDER
```

Executes initialization action when opening form.

### Example 8: List with Position

```
VIEW_ORDER_LINES is a Navigation
    label is "Order Lines"
    list is order.line set.ORDER_LINE_LIST
        position to current.line
```

Navigates to list and positions to current line.

### Example 9: View Document Navigation

```
VIEW_ATTACHMENT is a Navigation
    label is "View Attachment"
    view attachment.document
```

Views binary document.

### Example 10: View Business Object Reference

```
VIEW_RELATED_RECORD is a Navigation
    label is "View Related Record"
    view related.object.reference
```

Views business object through reference field.

### Example 11: Template Navigation

```
CREATE_FROM_TEMPLATE is a Navigation
    label is "Create from Template"
    template is template.definition
        child designator field is template.type
        parent match field is record.type
        target business class is TARGET_CLASS
        form is TEMPLATE_FORM
```

Template-driven creation navigation.

### Example 12: PDF with Font Offset

```
DETAILED_REPORT is a Navigation
    label is "Detailed Report"
    show as pdf in foreground in portrait
        font offset is -2
    page is DETAILED_REPORT_PAGE
```

PDF with smaller font size.

### Example 13: Conditional Multi-Target

```
APPROVAL_FORM is a Navigation
    label is "Approval"
    
    form is approval.MANAGER_FORM
        when approval.level = "Manager"
    
    form is approval.DIRECTOR_FORM
        when approval.level = "Director"
    
    form is approval.STANDARD_FORM
```

Different forms based on approval level.

### Example 14: State-Specific Actions

```
ORDER_MANAGEMENT is a Navigation
    label is "Manage Order"
    form is order.ORDER_FORM
        restrict action DRAFT.DELETE
        enable action SUBMITTED.APPROVE
        enable action SUBMITTED.REJECT
```

Controls actions based on state.

### Example 15: Inline Form Definition

```
QUICK_EDIT is a Navigation
    label is "Quick Edit"
    form is record.INLINE_FORM
        FIELD1
        FIELD2
        FIELD3
```

Navigation with inline form definition.

### Example 16: Complex Conditional Navigation

```
CUSTOMER_PORTAL is a Navigation
    label is "Customer Portal"
    valid when (customer.status = "Active") and
               (customer.portal.enabled = true)
    
    smartphone page is MOBILE_PORTAL
        when mobile.device
    
    desktop page is DESKTOP_PORTAL
```

Complex conditions with device-specific targets.

### Example 17: Background Report

```
MONTHLY_REPORT is a Navigation
    label is "Monthly Report"
    show as pdf in background in landscape
        font offset is 0
    page is MONTHLY_REPORT_PAGE
```

Long-running report generated in background.

### Example 18: Multiple Action Restrictions

```
ARCHIVED_VIEW is a Navigation
    label is "View Archived"
    form is record.ARCHIVE_FORM
        restrict action EDIT
        restrict action DELETE
        restrict action COPY
        enable action RESTORE
```

Multiple action restrictions and enablements.

### Example 19: Conditional View

```
VIEW_DOCUMENT_OR_FORM is a Navigation
    label is "View Details"
    
    view document.file
        when document.file not empty
    
    form is document.METADATA_FORM
```

Views document if available, otherwise shows form.

### Example 20: Comprehensive Navigation

```
ORDER_DETAILS is a Navigation
    label is "Order Details"
    valid when order not empty
    
    smartphone form is order.MOBILE_FORM
        restrict action DELETE
        when mobile.device
    
    tablet form is order.TABLET_FORM
        restrict action DELETE
        when tablet.device
    
    desktop form is order.DESKTOP_FORM
        open action is LOAD_ORDER_DETAILS
        enable action EDIT
        enable action APPROVE
        when desktop.device
    
    form is order.DEFAULT_FORM
```

Comprehensive navigation with multiple device-specific targets.

## Best Practices

1. **Use Meaningful Names**
   - Clear, descriptive navigation names
   - Indicate navigation purpose
   - Follow naming conventions
   - Be consistent

2. **Provide Clear Labels**
   - User-friendly labels
   - Describe destination
   - Use action verbs
   - Keep concise

3. **Apply Drill Restrictions Appropriately**
   - Restrict internal navigations
   - Restrict conditional navigations
   - Keep drill lists focused
   - Document restrictions

4. **Leverage Conditional Visibility**
   - Show navigations when relevant
   - Check permissions
   - Validate state
   - Test conditions

5. **Design Device-Specific Experiences**
   - Optimize for device type
   - Consider screen size
   - Adapt layouts
   - Test on devices

6. **Use PDF Generation Wisely**
   - Foreground for quick pages
   - Background for long reports
   - Choose appropriate orientation
   - Adjust font size as needed

7. **Control Form Actions**
   - Restrict inappropriate actions
   - Enable necessary actions
   - Consider state context
   - Test action availability

8. **Position Lists Effectively**
   - Use position to for context
   - Highlight relevant records
   - Improve user experience
   - Test positioning

9. **Test Navigation Paths**
   - Verify all targets work
   - Test conditional logic
   - Check device-specific paths
   - Validate action controls

10. **Document Navigation Purpose**
    - Explain navigation intent
    - Note special behaviors
    - Document conditions
    - Describe restrictions

## Navigation vs Drill

| Aspect | Navigation | Drill |
|--------|------------|-------|
| Definition Location | Business class file | UI definition file |
| Purpose | Logical navigation path | UI navigation implementation |
| Reusability | Can be referenced by drills | References navigations |
| Automatic Inclusion | Can be in automatic drill list | Must be explicitly defined |
| Drill Restriction | Can be drill restricted | N/A |

Navigations define the logical paths; drills implement them in the UI.

## Automatic Drill List Inclusion

Navigations are automatically included in drill lists unless:
- Marked as `drill restricted`
- Manual Drill List is specified in UI definition

## Troubleshooting

### Issue: Navigation not appearing

**Possible Causes:**
- Conditional visibility false
- Drill restricted
- Permission denied
- Not in drill list

**Solutions:**
- Check condition logic
- Review drill restriction
- Verify permissions
- Check drill list configuration

### Issue: Wrong target displayed

**Possible Causes:**
- Condition logic error
- Wrong form/list reference
- Device type mismatch
- Order of conditions

**Solutions:**
- Review condition logic
- Verify target references
- Check device detection
- Test condition order

### Issue: PDF generation fails

**Possible Causes:**
- Page not suitable for PDF
- Font offset too large
- Background generation timeout
- Resource constraints

**Solutions:**
- Verify page content
- Adjust font offset
- Use foreground for testing
- Check system resources

### Issue: Actions not controlled

**Possible Causes:**
- Incorrect action names
- State not specified
- Action not on form
- Syntax error

**Solutions:**
- Verify action names
- Check state qualification
- Confirm action exists
- Review syntax

### Issue: Position to not working

**Possible Causes:**
- Record not in list
- Incorrect related link
- List not loaded
- Timing issue

**Solutions:**
- Verify record in list
- Check related link
- Ensure list loads
- Test with data

## Related Concepts

- [Drills](drills.md) - UI drill definitions
- [UI Definition](ui-definition.md) - Overall UI structure
- [UI Base Definitions](ui-base-definitions.md) - UI constructs
- [Relations](relations.md) - Business relationships
- [Actions](actions.md) - Action definitions

## Notes

- Navigations define logical navigation paths
- Can be referenced by drills
- Automatically included in drill lists unless restricted
- Support device-specific targets
- Can generate PDFs with various options
- Multiple targets with conditions supported
- First matching condition determines target
- Can control form actions (restrict/enable)
- Position to highlights records in lists
- View navigation works with BinaryDocument and BusinessObjectReference
- Drill restricted prevents automatic drill list inclusion
- PDF foreground generation is default
- Use background for long-running reports
- Font offset adjusts PDF font size
- Test all navigation paths thoroughly
- Document navigation purpose and behavior
- Consider user workflow when designing navigations
- Navigations are defined in business class files
- Drills reference navigations in UI definition files
