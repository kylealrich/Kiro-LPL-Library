# User Interface Definition

## Overview

User Interface (UI) Definitions specify the presentation layer for business classes, business tasks, and fields. They define how data is displayed and interacted with in the user interface, including forms, lists, navigations, drills, and other UI components. UI definitions are logically part of their associated business class, business task, or field but are stored in separate files.

## File Structure

### Directory Organization

UI definitions use a parallel directory structure to business logic:

- **Business Logic**: `com/lawson/apps/<ModuleName>`
- **UI Definitions**: `com/lawson/forms/<ModuleName>`

### File Naming

UI definition files have the same name as their corresponding business class, business task, or field:

**Examples:**
- Business Class: `com/lawson/apps/order/Order.businessclass`
- UI Definition: `com/lawson/forms/order/Order.businessclass`

- Field: `com/lawson/apps/field/Address.field`
- UI Definition: `com/lawson/forms/field/Address.field`

### Multiple UI Definitions

Each business class, business task, or field can have:
- **Default UI definition** - Primary UI presentation
- **Alternate UI definitions** - Alternative presentations for different contexts

## Syntax

```
UI Structure ::=
    ( <BusinessClass> is a BusinessClass
    | <BusinessTask> is a BusinessTask
    | <FieldName> is a Field)
    
    [Drill Backs]
        [<DrillBackDefinition>…]
    
    [Drill List]
        [(<DrillDefinition> | primary audit list | data translation list)…]
    
    [<DrillListDefinition>…]
    [<NavigationDefinition>…]
    [<ContextMessageDefinition>…]
    
    [Context Message Invocations]
        [<ContextMessageInvocation>…]
    
    [<ListDefinition>…]
    [<CardViewDefinition>…]
    [<InstanceCountChartDefinition>…]
    [<FormDefinition>…]
    [<ActionFormDefinition>…]
    [<CompositeFormDefinition>…]
    [<MatrixFormDefinition>…]
    [<SearchFormDefinition>…]
    [<SummaryFormDefinition>…]
```

## Components

### Drill Backs

```
Drill Backs
    <DrillBackDefinition>
    …
```

Defines drill-back navigation paths from this UI to parent or related objects.

### Drill List

```
Drill List
    (<DrillDefinition> | primary audit list | data translation list)
    …
```

**Automatic Drill List:**
If no Drill List is specified, it is automatically built from:
- All non-drill-restricted Navigations
- All Lists and Forms propagated as drills

**Manual Drill List:**
When Drill List section is present, it overrides automatic drill propagation.

**Special Drill Types:**
- `primary audit list` - Includes primary audit log list
- `data translation list` - Includes data translation list

### Drill Propagation

**List Propagation:**
- If a List is propagated as a Drill, it doesn't put a drill on this business class
- Instead, it drills to all business classes in the context of this business class
- Makes sense to have both a Drill List and lists propagated as drills

**Form Propagation:**
- Does not make sense to propagate a form as a Drill if Drill List is specified
- Forms would not be automatically included in the Drill List

**Navigation Restrictions:**
- Does not make sense to drill-restrict Navigations if Drill List is specified
- Restricted navigations would not be automatically included

### Navigations

```
<NavigationDefinition>
…
```

Defines navigation paths to related business objects or external resources.

### Context Messages

```
<ContextMessageDefinition>
…

Context Message Invocations
    <ContextMessageInvocation>
    …
```

Defines context-sensitive messages and their invocations.

### Lists

```
<ListDefinition>
…
```

Defines list views for displaying multiple records.

### Card Views

```
<CardViewDefinition>
…
```

Defines card-based views for displaying records.

### Charts

```
<InstanceCountChartDefinition>
…
```

Defines charts for visualizing instance counts and metrics.

### Forms

```
<FormDefinition>
…
```

Defines standard forms for viewing and editing records.

### Action Forms

```
<ActionFormDefinition>
…
```

Defines forms specifically for action execution.

### Composite Forms

```
<CompositeFormDefinition>
…
```

Defines forms that combine multiple sub-forms or sections.

### Matrix Forms

```
<MatrixFormDefinition>
…
```

Defines grid-based forms for cross-tabulated data entry.

### Search Forms

```
<SearchFormDefinition>
…
```

Defines forms for searching and filtering records.

### Summary Forms

```
<SummaryFormDefinition>
…
```

Defines forms for displaying summary information.

## Use Cases

### Business Class UI
- Define how business objects are displayed
- Specify available lists and forms
- Configure drill paths
- Set up navigations

### Business Task UI
- Define task-specific interfaces
- Configure task workflows
- Set up task navigations
- Specify task forms

### Field UI
- Define field presentation
- Configure field widgets
- Set up field-specific forms
- Specify field validations

### Drill Navigation
- Configure drill-down paths
- Set up drill-back navigation
- Define related object access
- Enable contextual navigation

## Examples

### Example 1: Simple Business Class UI

```
ORDER is a BusinessClass
    
    Drill List
        primary audit list
        ORDER_LINES
        CUSTOMER_INFO
    
    ORDER_LIST is a List
        // List definition
    
    ORDER_FORM is a Form
        // Form definition
    
    ORDER_SEARCH is a SearchForm
        // Search form definition
```

Basic UI definition with drill list, list view, form, and search.

### Example 2: UI with Navigations

```
EMPLOYEE is a BusinessClass
    
    Navigations
        DEPARTMENT_NAV navigates to DEPARTMENT
            using department
        
        MANAGER_NAV navigates to EMPLOYEE
            using manager
    
    Drill List
        primary audit list
        EMPLOYEE_HISTORY
        EMPLOYEE_BENEFITS
    
    EMPLOYEE_LIST is a List
        // List definition
    
    EMPLOYEE_FORM is a Form
        // Form definition
```

UI with navigation paths to related objects.

### Example 3: UI with Automatic Drill List

```
PRODUCT is a BusinessClass
    
    // No Drill List specified - automatic drill propagation
    
    PRODUCT_CATEGORIES navigates to CATEGORY
        using category
        // Not drill restricted - will be in automatic drill list
    
    PRODUCT_LIST is a List
        propagate as drill
        // Will drill to business classes in context
    
    PRODUCT_FORM is a Form
        // Standard form
```

Relies on automatic drill list generation.

### Example 4: UI with Context Messages

```
INVOICE is a BusinessClass
    
    Context Messages
        OVERDUE_MESSAGE
            when invoice.due.date < today
            message is "Invoice is overdue"
        
        PAID_MESSAGE
            when invoice.status = "Paid"
            message is "Invoice has been paid"
    
    Context Message Invocations
        show OVERDUE_MESSAGE on INVOICE_FORM
        show PAID_MESSAGE on INVOICE_LIST
    
    INVOICE_FORM is a Form
        // Form definition
```

Displays context-sensitive messages.

### Example 5: Field UI Definition

```
ADDRESS is a Field
    
    ADDRESS_FORM is a Form
        STREET
        CITY
        STATE
        ZIP_CODE
    
    ADDRESS_SEARCH is a SearchForm
        CITY
        STATE
        ZIP_CODE
```

UI definition for a field.

### Example 6: UI with Multiple Form Types

```
PROJECT is a BusinessClass
    
    Drill List
        primary audit list
        PROJECT_TASKS
        PROJECT_RESOURCES
    
    PROJECT_LIST is a List
        // List view
    
    PROJECT_FORM is a Form
        // Standard form
    
    PROJECT_ACTION_FORM is an ActionForm
        for START_PROJECT action
        // Action-specific form
    
    PROJECT_MATRIX is a MatrixForm
        rows are task set
        columns are resource set
        cell is ASSIGNMENT
    
    PROJECT_SUMMARY is a SummaryForm
        // Summary view
```

Comprehensive UI with multiple form types.

### Example 7: Business Task UI

```
APPROVAL_TASK is a BusinessTask
    
    Navigations
        DOCUMENT_NAV navigates to DOCUMENT
            using document
        
        REQUESTER_NAV navigates to EMPLOYEE
            using requester
    
    APPROVAL_FORM is a Form
        DOCUMENT_INFO
        APPROVAL_DECISION
        COMMENTS
    
    APPROVAL_HISTORY is a List
        // History of approvals
```

UI for a business task.

### Example 8: UI with Card View

```
CUSTOMER is a BusinessClass
    
    Drill List
        primary audit list
        CUSTOMER_ORDERS
        CUSTOMER_CONTACTS
    
    CUSTOMER_LIST is a List
        // Standard list
    
    CUSTOMER_CARDS is a CardView
        // Card-based view
    
    CUSTOMER_FORM is a Form
        // Standard form
```

Includes card view for alternative display.

### Example 9: UI with Charts

```
SALES is a BusinessClass
    
    SALES_BY_REGION is an InstanceCountChart
        group by region
        // Chart definition
    
    SALES_BY_PRODUCT is an InstanceCountChart
        group by product.category
        // Chart definition
    
    SALES_LIST is a List
        // List view
    
    SALES_FORM is a Form
        // Form view
```

Includes charts for data visualization.

### Example 10: Composite Form UI

```
ORDER is a BusinessClass
    
    ORDER_COMPOSITE is a CompositeForm
        ORDER_HEADER section
            // Header fields
        
        ORDER_LINES section
            // Line items
        
        ORDER_TOTALS section
            // Totals and summary
    
    ORDER_LIST is a List
        // List view
```

Uses composite form for complex layout.

## Best Practices

1. **Organize UI Definitions**
   - Keep UI separate from business logic
   - Use consistent file naming
   - Follow directory structure conventions
   - Group related UI components

2. **Design Drill Paths**
   - Provide intuitive navigation
   - Include relevant drill targets
   - Use automatic drill list when appropriate
   - Override with manual list when needed

3. **Create Appropriate Views**
   - Provide list views for browsing
   - Create forms for editing
   - Add search forms for filtering
   - Include summary views for overview

4. **Use Context Messages**
   - Display relevant information
   - Provide timely alerts
   - Guide user actions
   - Enhance user experience

5. **Leverage Multiple Form Types**
   - Use standard forms for basic editing
   - Use action forms for specific actions
   - Use matrix forms for grid data
   - Use composite forms for complex layouts

6. **Consider User Workflow**
   - Design intuitive navigation
   - Provide clear action paths
   - Minimize clicks to common tasks
   - Support efficient data entry

7. **Test UI Definitions**
   - Verify all navigations work
   - Test drill paths
   - Validate form layouts
   - Check context messages

## Drill List Behavior

### Automatic Drill List

When no Drill List section is specified:
- Includes all non-drill-restricted Navigations
- Includes all Lists propagated as drills
- Includes all Forms propagated as drills
- Automatically maintained

### Manual Drill List

When Drill List section is present:
- Overrides automatic propagation
- Must explicitly list all drills
- Provides full control
- Requires maintenance

### Propagation Considerations

**With Manual Drill List:**
- Don't propagate forms as drills (won't be included)
- Don't drill-restrict navigations (won't be included)
- Can still propagate lists as drills (drills to context classes)

**With Automatic Drill List:**
- Propagate forms and lists as needed
- Use drill restrictions to control inclusion
- System maintains drill list automatically

## Related Concepts

- [Business Class Definition](business-class-definition.md) - Business class structure
- [Forms](matrix-forms.md) - Form definitions
- [Actions](actions.md) - Action definitions
- [Relations](relations.md) - Relationship navigation
- [Messages](messages.md) - Message definitions

## Notes

- UI definitions are logically part of business class, business task, or field
- Stored in separate files in parallel directory structure
- File names match corresponding business logic files
- Each can have default and alternate UI definitions
- Drill List section overrides automatic drill propagation
- Lists propagated as drills affect context business classes
- Forms should not be propagated as drills with manual Drill List
- Navigations should not be drill-restricted with manual Drill List
- UI definitions enable separation of presentation from business logic
- Multiple form types support different use cases
- Context messages enhance user experience
- Drill paths enable intuitive navigation
- Test UI definitions thoroughly with users
