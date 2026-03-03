# Forms

## Overview

Forms define the layout and presentation of business class instances for data entry, viewing, and editing. They control how fields are arranged, which actions are available, and how users interact with business objects. Forms can be used for create, update, and display operations, with support for conditional visibility, custom layouts, and action management.

## Basic Form Definition

```
<FormName> is a Form
    [is primary]
        [when <SimpleCondition>]
    [is drill target]
        [when <SimpleCondition>]
    [propagate as a drill]
    [use for (<DisplayType> | action [<StateName>.]<ActionName>)]
    …
    [title is <Message>]
    [suppress standard toolbar]
    [disable all actions]
    [called out actions only]
    [disable field options]
    [print form is <FormName>]
    [Actions]
        <CalledOutAction>
        …
    [(restrict | enable) action [<StateName>.]<ActionName>]
    …
    [when <Condition>]
    ( Layout
        <FormLayout>
        …
    | Manual Representation
        representation name is <Literal>
        Property Mapping
            <MessageProperty> = <RelatedValue>
            …)
```

## Syntax

```
FormDefinition ::=
    <FormName> is a Form
        [is primary]
            [when <SimpleCondition>]
        [is drill target]
            [when <SimpleCondition>]
        [propagate as a drill]
        [use for (<DisplayType> | action [<StateName>.]<ActionName>)]
        …
        [title is <Message>]
        [suppress standard toolbar]
        [disable all actions]
        [called out actions only]
        [disable field options]
        [print form is <FormName>]
        [Actions]
            <CalledOutAction>
            …
        [(restrict | enable) action [<StateName>.]<ActionName>]
        …
        [when <Condition>]
        ( Layout
            <FormLayout>
            …
        | Manual Representation
            representation name is <Literal>
            Property Mapping
                <MessageProperty> = <RelatedValue>
                …)
```

## Form Attributes

### Primary Form

```
is primary
    [when <SimpleCondition>]
```

Designates this form as the primary form for the business class. The primary form is used by default when displaying or editing instances.

| Attribute | Description |
|-----------|-------------|
| `is primary` | Makes this the default form |
| `when <SimpleCondition>` | Conditional primary designation |

**Notes:**
- Only one form can be primary at a time
- Conditional primary forms allow different defaults based on context
- If no primary form is defined, system uses default behavior

### Drill Target

```
is drill target
    [when <SimpleCondition>]
```

Designates this form as a drill target for navigation from other UI elements.

| Attribute | Description |
|-----------|-------------|
| `is drill target` | Makes form available as drill destination |
| `when <SimpleCondition>` | Conditional drill target designation |

### Drill Propagation

```
propagate as a drill
```

Propagates drill context when navigating from this form to other forms or lists.

### Use For

```
use for (<DisplayType> | action [<StateName>.]<ActionName>)
…
```

Specifies when this form should be used.

**Options:**
- `<DisplayType>` - Use for specific display types (e.g., Create, Update, Display)
- `action [<StateName>.]<ActionName>` - Use for specific actions

**Examples:**
```
use for Create
use for Update
use for action APPROVE
use for action PENDING.SUBMIT
```

### Title

```
title is <Message>
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `title is <Message>` | Form title | FormName |

The title can include field references and expressions to create dynamic titles.

**Example:**
```
title is "Customer: <CustomerName>"
title is "Order <Order> - <Status>"
```

## Toolbar and Actions

### Suppress Standard Toolbar

```
suppress standard toolbar
```

Hides the standard toolbar. By default, the standard toolbar shows unless CalledOutActions are defined.

### Disable All Actions

```
disable all actions
```

Disables all actions on the form, preventing any action execution.

### Called Out Actions Only

```
called out actions only
```

Shows only explicitly called out actions, hiding all other actions.

### Disable Field Options

```
disable field options
```

Disables field-level options and context menus.

### Called Out Actions

```
Actions
    <CalledOutAction>
    …
```

Explicitly defines which actions to display on the form.

**Features:**
- Controls action visibility
- Defines action order
- Customizes action presentation
- Supports action grouping

### Restrict or Enable Actions

```
(restrict | enable) action [<StateName>.]<ActionName>
…
[when <Condition>]
```

**Valid on configuration only.**

Controls action availability with optional conditions.

| Attribute | Description |
|-----------|-------------|
| `restrict action` | Prevents action execution |
| `enable action` | Allows action execution |
| `when <Condition>` | Conditional restriction/enablement |

**Examples:**
```
restrict action DELETE
    when Balance > 0

enable action APPROVE
    when actor has role "Manager"
```

## Print Form

```
print form is <FormName>
```

Specifies a different form to use when printing to PDF.

**Use Cases:**
- Simplified print layouts
- Print-optimized formatting
- Different field arrangements for printing
- Removal of interactive elements

## Form Layout

### Standard Layout

```
Layout
    <FormLayout>
    …
```

Defines the visual arrangement of fields and components on the form.

**FormLayout components include:**
- Form fields
- Form buttons
- Form text
- Check controls
- Manual representations
- Field groups
- Sections
- Tabs
- Grids

### Manual Representation

```
Manual Representation
    representation name is <Literal>
    Property Mapping
        <MessageProperty> = <RelatedValue>
        …
```

Uses a custom representation instead of standard layout.

**Features:**
- Custom UI components
- External representations
- Advanced visualizations
- Integration with third-party controls

**Property Mapping:**
Maps business class fields to representation properties.

## Form Types and Usage

### Create Form

Used when creating new instances.

```
CUSTOMERCREATE is a Form
    use for Create
    title is "New Customer"
    
    Layout
        CUSTOMER
        CUSTOMERNAME
        ADDRESS
        CITY
        STATE
        ZIP
```

### Update Form

Used when editing existing instances.

```
CUSTOMERUPDATE is a Form
    use for Update
    title is "Edit Customer: <CustomerName>"
    
    Layout
        CUSTOMER
            read only
        CUSTOMERNAME
        ADDRESS
        CITY
        STATE
        ZIP
        BALANCE
            read only
```

### Display Form

Used for read-only viewing.

```
CUSTOMERDISPLAY is a Form
    use for Display
    title is "Customer: <CustomerName>"
    
    Layout
        CUSTOMER
        CUSTOMERNAME
        ADDRESS
        CITY
        STATE
        ZIP
        BALANCE
        CREDITLIMIT
        LASTORDERDATE
```

### Action-Specific Form

Used for specific actions.

```
ORDERAPPROVAL is a Form
    use for action APPROVE
    title is "Approve Order <Order>"
    
    Layout
        ORDER
            read only
        CUSTOMER
            read only
        TOTAL
            read only
        APPROVALCOMMENTS
        APPROVALDATE
```

## Examples

### Example 1: Simple Primary Form

```
CUSTOMERFORM is a Form
    is primary
    title is "Customer"
    
    Layout
        CUSTOMER
        CUSTOMERNAME
        ADDRESS
        CITY
        STATE
        ZIP
        PHONE
        EMAIL
```

### Example 2: Conditional Primary Form

```
ORDERFORM is a Form
    is primary
        when Status = "Draft"
    title is "Order <Order>"
    
    Layout
        ORDER
        CUSTOMER
        ORDERDATE
        STATUS
        TOTAL

ORDERAPPROVEDFORM is a Form
    is primary
        when Status = "Approved"
    title is "Order <Order> - Approved"
    
    Layout
        ORDER
            read only
        CUSTOMER
            read only
        ORDERDATE
            read only
        STATUS
            read only
        APPROVALDATE
        APPROVEDBY
        TOTAL
```

### Example 3: Form with Called Out Actions

```
INVOICEFORM is a Form
    is primary
    title is "Invoice <Invoice>"
    called out actions only
    
    Actions
        SAVE
        SUBMIT
        CANCEL
        PRINT
    
    Layout
        INVOICE
        CUSTOMER
        INVOICEDATE
        DUEDATE
        TOTAL
        BALANCE
```

### Example 4: Form with Restricted Actions

```
PAYMENTFORM is a Form
    is primary
    title is "Payment <Payment>"
    
    restrict action DELETE
        when Status = "Posted"
    
    restrict action EDIT
        when Status = "Void"
    
    enable action VOID
        when Status = "Posted" and actor has role "Accounting"
    
    Layout
        PAYMENT
        CUSTOMER
        PAYMENTDATE
        AMOUNT
        PAYMENTMETHOD
        STATUS
```

### Example 5: Form with Drill Target

```
PRODUCTDETAIL is a Form
    is drill target
    title is "Product Details: <ProductName>"
    propagate as a drill
    
    Layout
        PRODUCT
        PRODUCTNAME
        DESCRIPTION
        CATEGORY
        PRICE
        COST
        QUANTITYONHAND
        REORDERPOINT
```

### Example 6: Form with Print Layout

```
ORDERFORM is a Form
    is primary
    title is "Order <Order>"
    print form is ORDERPRINTFORM
    
    Layout
        ORDER
        CUSTOMER
        ORDERDATE
        STATUS
        SHIPTOADDRESS
        BILLTOADDRESS
        TOTAL

ORDERPRINTFORM is a Form
    title is "Order <Order>"
    suppress standard toolbar
    
    Layout
        ORDER
        CUSTOMER
        ORDERDATE
        SHIPTOADDRESS
        TOTAL
```

### Example 7: Form with Suppressed Toolbar

```
QUICKENTRY is a Form
    title is "Quick Entry"
    suppress standard toolbar
    disable field options
    
    Actions
        SAVE
        CANCEL
    
    Layout
        PRODUCTCODE
        QUANTITY
        PRICE
```

### Example 8: Multi-Purpose Form

```
EMPLOYEEFORM is a Form
    is primary
    use for Create
    use for Update
    title is "Employee: <EmployeeName>"
    
    Layout
        EMPLOYEE
        EMPLOYEENAME
        DEPARTMENT
        TITLE
        HIREDATE
        SALARY
        EMAIL
        PHONE
```

### Example 9: Form with Manual Representation

```
DASHBOARDFORM is a Form
    is primary
    title is "Dashboard"
    
    Manual Representation
        representation name is "CustomDashboard"
        Property Mapping
            salesData = SalesAmount
            orderCount = OrderCount
            customerCount = CustomerCount
            topProducts = TopProductsList
```

### Example 10: Wizard-Style Form

```
ORDERWIZARD is a Form
    use for action CREATE
    title is "New Order Wizard"
    called out actions only
    
    Actions
        NEXT
        PREVIOUS
        FINISH
        CANCEL
    
    Layout
        STEP1
            CUSTOMER
            ORDERDATE
        STEP2
            PRODUCTLINES
        STEP3
            SHIPPINGINFO
        STEP4
            PAYMENTINFO
```

## Form Layout Components

Forms can contain various layout components:

### Fields
- Simple fields
- Group fields
- Array fields
- Derived fields
- Transient fields

### Controls
- Buttons
- Checkboxes
- Radio buttons
- Dropdowns
- Date pickers

### Containers
- Sections
- Tabs
- Panels
- Groups
- Grids

### Text
- Labels
- Instructions
- Help text
- Static text

### Special
- Manual representations
- Custom controls
- Embedded lists
- Charts

## Best Practices

### Form Design

1. **Logical Organization**
   - Group related fields
   - Use consistent layouts
   - Follow natural workflow
   - Consider user expectations

2. **Field Arrangement**
   - Most important fields first
   - Logical field order
   - Appropriate field sizes
   - Consistent spacing

3. **Visual Hierarchy**
   - Use sections and groups
   - Emphasize key information
   - Provide visual cues
   - Balance density and whitespace

### Primary Forms

1. **Selection Criteria**
   - Choose most common use case
   - Consider user workflow
   - Use conditions when appropriate
   - Test with various scenarios

2. **Conditional Primary**
   - Use for state-dependent layouts
   - Ensure conditions are mutually exclusive
   - Provide fallback form
   - Document conditions clearly

### Actions

1. **Action Management**
   - Show relevant actions only
   - Use called out actions for clarity
   - Order actions logically
   - Consider user permissions

2. **Action Restrictions**
   - Restrict based on business rules
   - Provide clear feedback
   - Test all conditions
   - Consider edge cases

### Titles

1. **Dynamic Titles**
   - Include key identifying information
   - Keep titles concise
   - Use field references
   - Test with various data

2. **Consistency**
   - Follow naming conventions
   - Use consistent patterns
   - Consider localization
   - Maintain clarity

### Print Forms

1. **Print Optimization**
   - Simplify layout for printing
   - Remove interactive elements
   - Optimize for paper size
   - Test print output

2. **Content Selection**
   - Include essential information
   - Remove unnecessary fields
   - Consider page breaks
   - Format for readability

### Performance

1. **Optimization**
   - Minimize field count
   - Use conditional visibility
   - Lazy load expensive data
   - Test with realistic data

2. **Responsiveness**
   - Consider mobile devices
   - Use responsive layouts
   - Test various screen sizes
   - Ensure touch-friendly controls

### Usability

1. **User Experience**
   - Clear field labels
   - Helpful instructions
   - Appropriate defaults
   - Validation feedback

2. **Accessibility**
   - Keyboard navigation
   - Screen reader support
   - Color contrast
   - Focus indicators

## Common Patterns

### Standard CRUD Pattern

```
<EntityName>CREATE is a Form
    use for Create
    title is "New <Entity>"
    Layout
        <fields>

<EntityName>UPDATE is a Form
    use for Update
    title is "Edit <Entity>"
    Layout
        <fields>

<EntityName>DISPLAY is a Form
    use for Display
    title is "<Entity> Details"
    Layout
        <fields>
```

### Approval Form Pattern

```
<EntityName>APPROVAL is a Form
    use for action APPROVE
    title is "Approve <Entity>"
    called out actions only
    
    Actions
        APPROVE
        REJECT
        CANCEL
    
    restrict action APPROVE
        when not actor has role "Approver"
    
    Layout
        <read-only fields>
        APPROVALCOMMENTS
```

### Wizard Pattern

```
<ProcessName>WIZARD is a Form
    use for action <ActionName>
    title is "<Process> Wizard"
    called out actions only
    
    Actions
        NEXT
        PREVIOUS
        FINISH
        CANCEL
    
    Layout
        <step sections>
```

### Dashboard Pattern

```
<EntityName>DASHBOARD is a Form
    is primary
    title is "<Entity> Dashboard"
    suppress standard toolbar
    
    Layout
        <summary fields>
        <charts>
        <lists>
```

## Integration with Other Components

### Forms and Lists

Forms are often used in conjunction with lists for detail views.

```
CUSTOMERLIST is a List
    form is CUSTOMERFORM
    
    Display Fields
        CUSTOMER
        CUSTOMERNAME
        CITY
        STATE

CUSTOMERFORM is a Form
    is primary
    title is "Customer: <CustomerName>"
    
    Layout
        CUSTOMER
        CUSTOMERNAME
        ADDRESS
        CITY
        STATE
```

### Forms and Actions

Forms can be associated with specific actions.

```
APPROVE is an Action
    form is APPROVALFORM

APPROVALFORM is a Form
    use for action APPROVE
    title is "Approval Form"
    
    Layout
        <approval fields>
```

### Forms and States

Forms can vary based on state.

```
ORDERFORM is a Form
    is primary
        when Status = "Draft"

ORDERAPPROVEDFORM is a Form
    is primary
        when Status = "Approved"
```

## Troubleshooting

### Issue: Form Not Displaying

**Possible Causes:**
- Form not marked as primary
- Conditional primary not satisfied
- Form not associated with correct display type
- Permission issues

**Solutions:**
- Verify primary designation
- Check conditional logic
- Review use for clauses
- Check user permissions

### Issue: Actions Not Available

**Possible Causes:**
- Actions restricted
- Toolbar suppressed
- Called out actions only mode
- Condition not satisfied

**Solutions:**
- Review action restrictions
- Check toolbar settings
- Verify called out actions
- Test conditions

### Issue: Fields Not Editable

**Possible Causes:**
- Read-only fields
- Form used for Display
- Field rules preventing edit
- Permission restrictions

**Solutions:**
- Check field attributes
- Verify form usage type
- Review field rules
- Check permissions

### Issue: Print Form Not Working

**Possible Causes:**
- Print form not defined
- Print form has errors
- Layout issues
- Missing fields

**Solutions:**
- Define print form
- Validate print form syntax
- Test print layout
- Verify field availability

## Related Concepts

- [Lists](lists.md) - List definitions
- [Card Views](card-views.md) - Card-based displays
- [Actions](actions.md) - Action definitions
- [Field Definition](field-definition.md) - Field structure
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Conditions](conditions.md) - Conditional logic
- [UI Definition](ui-definition.md) - UI components

## Summary

Forms define how business class instances are displayed and edited, providing flexible layouts, action management, and conditional behavior. They support various use cases including create, update, display, and action-specific forms, with features for toolbar customization, print optimization, and drill-down navigation. Proper form design enhances user experience and application usability.

**Key Features:**
- Primary form designation
- Conditional forms
- Action management
- Custom layouts
- Print forms
- Drill targets
- Manual representations
- Toolbar control

**Best For:**
- Data entry
- Record editing
- Detail views
- Approval workflows
- Wizard processes
- Dashboard displays
- Print layouts
