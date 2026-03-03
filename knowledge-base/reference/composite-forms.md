# Composite Forms

## Overview

Composite Forms provide multi-panel interfaces for complex data entry and display scenarios. They come in two types: CompositeForm for flexible panel arrangements and WizardForm for sequential, step-by-step workflows. Composite Forms support various layouts including dashboard views, cart views, and panel navigation, making them ideal for complex business processes.

## Form Types

### Composite Form

```
<FormName> is a CompositeForm
```

Flexible multi-panel form with various layout options including dashboard views, cart views, and independent panels.

### Wizard Form

```
<FormName> is a WizardForm
```

Sequential multi-panel form for step-by-step workflows with navigation controls and progress tracking.

## Basic Syntax

```
CompositeFormDefinition ::=
    <FormName> is a (CompositeForm | WizardForm)
        [is primary]
            [when <SimpleCondition>]
        [is drill target]
            [when <SimpleCondition>]
        [summary form is <FormName>]
        [propagate as a drill]
        [use for (<DisplayType> | action [<StateName>.]<ActionName>)]
        …
        [title is <Message>]
        [print form is <FormName>]
        [background is (<RelatedValue> | theme color)]
        [span header]
        [foreground is (dark | light)]
        [context form is <FormName>]
            [show context form on left]
            [background is (<RelatedValue> | theme color)]
            [span header]
            [foreground is (dark | light)]
        [implements IndependentFormPanels]
        [implements DashBoardView]
            [show context form on top]
            [show (3 | 4) columns]
            [use 12 column grid]
            [background is (<RelatedValue> | theme color)]
            [print as dashboard]
            [compress columns]
        [implements CartView]
            cart is <RelatedLink>
            [show panel control on left]
        [show panel navigation]
            [linear when <SimpleCondition>]
            [last panel navigation is <UILink>]
            [next button title is <Message>]
        [show steps]
        [label is <Message>]
        [suppress standard toolbar]
        [disable all actions]
        [called out actions only]
        [disable field options]
        [orientation is (portrait | landscape)]
        [font offset is <Number>]
        [Actions]
            <CalledOutAction>
            …
        [(restrict | enable) action [<StateName>.]<ActionName>]
        …
        [when (<Condition>)]
        (<CompositeFormPanel> | <CompositeFormMultiListPanel>)
        …
```

## Form Attributes

### Primary Form

```
is primary
    [when <SimpleCondition>]
```

Designates this as the primary form for the business class.

### Drill Target

```
is drill target
    [when <SimpleCondition>]
```

Makes this form available as a drill target for navigation.

### Summary Form

```
summary form is <FormName>
```

**Valid on WizardForm only.** Acts as a 'commit' boundary, providing a summary before final submission.

### Propagate as Drill

```
propagate as a drill
```

Propagates drill context when navigating from this form.

### Use For

```
use for (<DisplayType> | action [<StateName>.]<ActionName>)
…
```

Specifies when this form should be used.

### Title

```
title is <Message>
```

Form title (defaults to FormName).

### Print Form

```
print form is <FormName>
```

Allows a different form to be used when printing to PDF.

### Background and Foreground

```
background is (<RelatedValue> | theme color)
span header
foreground is (dark | light)
```

- **background**: Sets background image or theme color (RelatedValue must be an image)
- **span header**: Spans the header across the form
- **foreground**: Text color (default: dark)

## Context Form

```
context form is <FormName>
    [show context form on left]
    [background is (<RelatedValue> | theme color)]
    [span header]
    [foreground is (dark | light)]
```

Allows common 'context' fields to be placed outside the panels. **Not valid on WizardForm.**

**Features:**
- Displays context information consistently
- Can be positioned on left or top
- Supports custom background and foreground
- Separate from panel content

## Form Implementations

### Independent Form Panels

```
implements IndependentFormPanels
```

Makes panels independent of each other, allowing separate save/validation.

### Dashboard View

```
implements DashBoardView
    [show context form on top]
    [show (3 | 4) columns]
    [use 12 column grid]
    [background is (<RelatedValue> | theme color)]
    [print as dashboard]
    [compress columns]
```

**Valid on CompositeForm only.**

Dashboard-style layout with configurable columns and grid.

**Attributes:**
- **show context form on top**: Positions context form at top
- **show (3 | 4) columns**: Number of columns (default: 3)
- **use 12 column grid**: Use 12-column grid system
- **background**: Background image or theme color
- **print as dashboard**: Optimize for dashboard printing
- **compress columns**: Compress column spacing

### Cart View

```
implements CartView
    cart is <RelatedLink>
    [show panel control on left]
```

**Valid on CompositeForm only.**

Shopping cart-style interface. RelatedLink must resolve to an instance of this BusinessClass.

**Features:**
- Cart-based navigation
- Item management
- Optional left panel control
- E-commerce workflows

### Panel Navigation

```
show panel navigation
    [linear when <SimpleCondition>]
    [last panel navigation is <UILink>]
    [next button title is <Message>]
```

Enables panel navigation controls.

**Attributes:**
- **linear when**: Makes navigation linear (similar to WizardForm) when condition is true
- **last panel navigation**: Navigation target after last panel
- **next button title**: Custom title for next button

### Show Steps

```
show steps
    [label is <Message>]
```

**Valid on WizardForm only.** Displays steps graphic showing progress through wizard.

## Toolbar and Actions

```
suppress standard toolbar
disable all actions
called out actions only
disable field options
```

Controls toolbar and action availability.

### Actions

```
Actions
    <CalledOutAction>
    …
```

Explicitly defines which actions to display.

### Restrict or Enable Actions

```
(restrict | enable) action [<StateName>.]<ActionName>
    …
[when (<Condition>)]
```

**Valid on configuration only.** Controls action availability with optional conditions.

## Print Settings

```
orientation is (portrait | landscape)
font offset is <Number>
```

PDF printing configuration.

## Composite Form Panel

Standard panel within a composite form.

### Syntax

```
CompositeFormPanel ::=
    <PanelName> is a DashBoardPanel
        …
        [show (3 | 4) columns]
        [use 12 column grid]
        [title is <Message>]
        [icon is <Icon>]
        [(valid | visible) when <SimpleCondition>]
        [mouse over text is <Message>]
        [background is (<RelatedValue> | theme color)]
        [print as dashboard]
        [compress columns]
        <CompositeFormPanel>
        …
    
    <PanelName> is a Panel
        …
        [title is <Message>]
        [icon is <Icon>]
        [(valid | visible) when <SimpleCondition>]
        [mouse over text is <Message>]
        [panel header is <Message>]
        [overview navigation is <NavigationName>]
        [label is <Message>]
        [(double width | triple width | quad width)]
        [span (1|2|3|4|5|6|7|8|9|10|11|12) columns]
        [double height]
        [show in column(1|2|3|4|5|6|7|8|9|10|11|12)]
        (<FormPanelDefinition> | <FormSubPanelDefinition>)
```

### Dashboard Panel

```
<PanelName> is a DashBoardPanel
```

Panel with dashboard-style layout containing nested panels.

**Attributes:**
- **show (3 | 4) columns**: Number of columns (default: 3)
- **use 12 column grid**: Use 12-column grid system
- **background**: Background image or theme color
- **print as dashboard**: Optimize for dashboard printing
- **compress columns**: Compress column spacing

### Standard Panel

```
<PanelName> is a Panel
```

Standard panel with content.

**Attributes:**
- **title**: Panel title
- **icon**: Panel icon
- **valid/visible when**: Conditional display
- **mouse over text**: Tooltip
- **panel header**: Header text
- **overview navigation**: Navigation for overview (valid in DashBoardView context only)
- **label**: Panel label

### Panel Sizing (Dashboard View)

```
(double width | triple width | quad width)
span (1|2|3|4|5|6|7|8|9|10|11|12) columns
double height
show in column(1|2|3|4|5|6|7|8|9|10|11|12)
```

Controls panel size and position in dashboard layouts.

## Form Panel Definition

Defines the content of a panel.

### Syntax

```
FormPanelDefinition ::=
    [panel text is <Message>]
    [acknowledge action is [<StateName>.]<ActionName>]
        [when <SimpleCondition>]
    [acknowledge completed when <SimpleCondition>]
    [visited action is [<StateName>.]<ActionName>]
    [in progress when <SimpleCondition>]
    [completed when <SimpleCondition>]
    [raise <AlertType> alert when <SimpleCondition>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
    (<Form> | <Action> | <List> | <Template> | <Navigation> | <URL>)
    [<FormDefinition>]
    [save on next]
    [<FormPanelActions>]
```

### Panel Content Types

- **Form**: Embedded form
- **Action**: Action execution
- **List**: Embedded list
- **Template**: Template display
- **Navigation**: Navigation target
- **URL**: External URL

FormDefinition valid on inline Form only.

### Panel Navigation Attributes

Valid when 'show panel navigation' is enabled:

| Attribute | Description |
|-----------|-------------|
| `acknowledge action` | Action to execute when panel is acknowledged |
| `acknowledge completed when` | Condition for acknowledgment completion |
| `visited action` | Action to execute when panel is visited |
| `in progress when` | Condition indicating panel is in progress |
| `completed when` | Condition indicating panel is completed |
| `raise alert when` | Conditional alert display |

### Save on Next

```
save on next
```

Valid on panel navigation and wizard. Saves data when navigating to next panel.

### Form Panel Actions

```
FormPanelActions ::=
    [Special Actions]
        <CalledOutAction>
        …
```

Allows actions from the primary business class to be called from this context.

## Form Sub Panel Definition

Defines dynamic sub-panels based on a related link.

### Syntax

```
FormSubPanelDefinition ::=
    [visited action is [<StateName>.]<ActionName>]
    [in progress when <SimpleCondition>]
    [completed when <SimpleCondition>]
    [raise <AlertType> alert when <SimpleCondition>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
    sub panels are <RelatedLink>
    title is <Message>
    [(valid | visible) when <Condition>]
    [mouse over text is <Message>]
    [display when <Condition>]
    …
    <FormPanelDefinition>
    <FormPanelDefinition>
```

**Important:** Last panel in set cannot have 'display when'; all others must.

**Features:**
- Dynamic panel generation
- Based on related records
- Conditional display
- Multiple panel definitions

## Composite Form Multi-List Panel

Panel containing multiple lists.

### Syntax

```
CompositeFormMultiListPanel ::=
    <PanelName> is a MultiListPanel
        …
        [title is <Message>]
        [(valid | visible) when <Condition>]
        [mouse over text is <Message>]
        (<MultiList> | <MultiListLayout>)
```

### Multi-List

```
MultiList ::=
    <List>
    …
    [visible when <SimpleCondition>]
    [panel text is <Message>]
    [show <#> lines]
    [<FormPanelActions>]
```

Default: 5 lines.

### Multi-List Layout

```
MultiListLayout ::=
    Layout
        <MultiListLayoutDirective>
        …
        (<MultiList> | <MultiListLayoutDirective>)
        …

MultiListLayoutDirective ::=
    ( single column
    | two column
    | column(1|2)
    | (valid | visible) when [<BusinessClass>.]<SimpleCondition>
    | locale of <RelatedValue>)
```

Must specify BusinessClass when in MenuLayout.

## Examples

### Example 1: Simple Composite Form

```
CUSTOMERDETAIL is a CompositeForm
    is primary
    title is "Customer Details"
    
    BASICINFO is a Panel
        title is "Basic Information"
        CUSTOMERFORM
    
    ORDERS is a Panel
        title is "Orders"
        ORDERLIST
    
    CONTACTS is a Panel
        title is "Contacts"
        CONTACTLIST
```

### Example 2: Wizard Form

```
ORDERWIZARD is a WizardForm
    title is "New Order Wizard"
    show steps
    summary form is ORDERSUMMARY
    
    CUSTOMER is a Panel
        title is "Customer"
        label is "Select Customer"
        CUSTOMERSELECTION
        save on next
    
    PRODUCTS is a Panel
        title is "Products"
        label is "Select Products"
        PRODUCTSELECTION
        save on next
    
    SHIPPING is a Panel
        title is "Shipping"
        label is "Shipping Information"
        SHIPPINGFORM
        save on next
    
    PAYMENT is a Panel
        title is "Payment"
        label is "Payment Information"
        PAYMENTFORM
```

### Example 3: Dashboard View Composite Form

```
DASHBOARD is a CompositeForm
    is primary
    title is "Dashboard"
    implements DashBoardView
    show 3 columns
    
    SUMMARY is a Panel
        title is "Summary"
        double width
        SUMMARYFORM
    
    RECENTORDERS is a Panel
        title is "Recent Orders"
        ORDERLIST
        show 5 lines
    
    ALERTS is a Panel
        title is "Alerts"
        ALERTLIST
    
    TASKS is a Panel
        title is "Tasks"
        TASKLIST
```

### Example 4: Composite Form with Context

```
PROJECTDETAIL is a CompositeForm
    is primary
    title is "Project Details"
    context form is PROJECTCONTEXT
    show context form on left
    
    OVERVIEW is a Panel
        title is "Overview"
        PROJECTOVERVIEW
    
    TASKS is a Panel
        title is "Tasks"
        TASKLIST
    
    TEAM is a Panel
        title is "Team"
        TEAMLIST
    
    DOCUMENTS is a Panel
        title is "Documents"
        DOCUMENTLIST
```

### Example 5: Cart View Composite Form

```
SHOPPINGCART is a CompositeForm
    title is "Shopping Cart"
    implements CartView
    cart is CartItems
    show panel control on left
    
    CARTITEMS is a Panel
        title is "Cart Items"
        CARTITEMLIST
    
    SHIPPING is a Panel
        title is "Shipping"
        SHIPPINGFORM
    
    PAYMENT is a Panel
        title is "Payment"
        PAYMENTFORM
    
    REVIEW is a Panel
        title is "Review"
        ORDERREVIEW
```

### Example 6: Panel Navigation

```
ONBOARDING is a CompositeForm
    title is "Employee Onboarding"
    show panel navigation
    next button title is "Continue"
    last panel navigation is EMPLOYEEDASHBOARD
    
    PERSONALINFO is a Panel
        title is "Personal Information"
        completed when PersonalInfoComplete = true
        PERSONALINFOFORM
        save on next
    
    EMPLOYMENT is a Panel
        title is "Employment Details"
        in progress when PersonalInfoComplete = true
        completed when EmploymentComplete = true
        EMPLOYMENTFORM
        save on next
    
    BENEFITS is a Panel
        title is "Benefits"
        in progress when EmploymentComplete = true
        completed when BenefitsComplete = true
        BENEFITSFORM
        save on next
    
    DOCUMENTS is a Panel
        title is "Documents"
        in progress when BenefitsComplete = true
        DOCUMENTSFORM
```

### Example 7: Sub Panels

```
PROJECTMANAGER is a CompositeForm
    title is "Project Manager"
    
    PROJECTS is a Panel
        title is "Projects"
        sub panels are ProjectSet
        title is "<ProjectName>"
        display when Status = "Active"
        display when Status = "Planning"
        PROJECTDETAILFORM
```

### Example 8: Multi-List Panel

```
REPORTING is a CompositeForm
    title is "Reporting Dashboard"
    
    REPORTS is a MultiListPanel
        title is "Reports"
        
        Layout
            two column
                SALESREPORTS
                    visible when actor has role "Sales"
                    panel text is "Sales Reports"
                    show 10 lines
                
                FINANCEREPORTS
                    visible when actor has role "Finance"
                    panel text is "Finance Reports"
                    show 10 lines
```

### Example 9: Dashboard Panel with Nested Panels

```
EXECUTIVE is a CompositeForm
    title is "Executive Dashboard"
    implements DashBoardView
    show 4 columns
    use 12 column grid
    
    METRICS is a DashBoardPanel
        title is "Key Metrics"
        show 2 columns
        span 12 columns
        
        REVENUE is a Panel
            title is "Revenue"
            REVENUECHART
        
        ORDERS is a Panel
            title is "Orders"
            ORDERCHART
    
    ALERTS is a Panel
        title is "Alerts"
        span 6 columns
        ALERTLIST
    
    TASKS is a Panel
        title is "Tasks"
        span 6 columns
        TASKLIST
```

### Example 10: Wizard with Conditional Linear Navigation

```
APPROVAL is a WizardForm
    title is "Approval Workflow"
    show steps
    show panel navigation
    linear when RequiresSequentialApproval = true
    
    LEVEL1 is a Panel
        title is "Level 1 Approval"
        label is "Manager Approval"
        completed when Level1Approved = true
        LEVEL1FORM
        save on next
    
    LEVEL2 is a Panel
        title is "Level 2 Approval"
        label is "Director Approval"
        visible when RequiresLevel2 = true
        completed when Level2Approved = true
        LEVEL2FORM
        save on next
    
    LEVEL3 is a Panel
        title is "Level 3 Approval"
        label is "Executive Approval"
        visible when RequiresLevel3 = true
        completed when Level3Approved = true
        LEVEL3FORM
```

## Use Cases

### Multi-Step Workflows
- Wizards
- Onboarding processes
- Configuration workflows
- Sequential data entry

### Dashboard Interfaces
- Executive dashboards
- Operational dashboards
- Monitoring interfaces
- KPI displays

### Master-Detail Views
- Customer details with orders
- Project details with tasks
- Product details with inventory
- Account details with transactions

### Shopping Cart
- E-commerce checkout
- Order configuration
- Service selection
- Quote building

### Complex Data Entry
- Multi-section forms
- Related data entry
- Hierarchical data
- Grouped information

## Best Practices

### Form Design

1. **Panel Organization**
   - Group related information
   - Use logical panel order
   - Provide clear panel titles
   - Consider user workflow

2. **Navigation**
   - Make navigation intuitive
   - Show progress clearly
   - Enable back navigation
   - Provide completion feedback

3. **Context Forms**
   - Use for common fields
   - Position appropriately
   - Keep concise
   - Update dynamically

### Wizard Forms

1. **Step Design**
   - One task per step
   - Clear step labels
   - Show progress
   - Enable review

2. **Validation**
   - Validate per step
   - Use save on next
   - Provide clear errors
   - Allow correction

3. **Summary**
   - Provide summary form
   - Show all entered data
   - Allow editing
   - Confirm before commit

### Dashboard Views

1. **Layout**
   - Use appropriate columns
   - Balance panel sizes
   - Consider screen size
   - Test responsive behavior

2. **Content**
   - Show key information
   - Use charts effectively
   - Provide drill-down
   - Update dynamically

### Panel Navigation

1. **Progress Tracking**
   - Show current panel
   - Indicate completion
   - Display alerts
   - Enable navigation

2. **Conditional Flow**
   - Use linear when appropriate
   - Skip irrelevant panels
   - Validate conditions
   - Test all paths

### Performance

1. **Optimization**
   - Lazy load panels
   - Minimize initial load
   - Cache appropriately
   - Test with realistic data

2. **Responsiveness**
   - Fast panel switching
   - Smooth navigation
   - Quick validation
   - Provide feedback

## Common Patterns

### Wizard Pattern

```
<ProcessName>WIZARD is a WizardForm
    title is "<Process> Wizard"
    show steps
    summary form is <SummaryForm>
    
    <Step1> is a Panel
        title is "<Step 1 Title>"
        label is "<Step 1 Label>"
        <Step1Form>
        save on next
    
    <Step2> is a Panel
        title is "<Step 2 Title>"
        label is "<Step 2 Label>"
        <Step2Form>
        save on next
```

### Dashboard Pattern

```
<EntityName>DASHBOARD is a CompositeForm
    title is "<Entity> Dashboard"
    implements DashBoardView
    show 3 columns
    
    <Panel1> is a Panel
        title is "<Panel 1 Title>"
        <Panel1Content>
    
    <Panel2> is a Panel
        title is "<Panel 2 Title>"
        <Panel2Content>
```

### Master-Detail Pattern

```
<EntityName>DETAIL is a CompositeForm
    is primary
    title is "<Entity> Details"
    context form is <ContextForm>
    
    <DetailPanel> is a Panel
        title is "Details"
        <DetailForm>
    
    <RelatedPanel> is a Panel
        title is "Related <Items>"
        <RelatedList>
```

### Cart Pattern

```
<ProcessName>CART is a CompositeForm
    title is "<Process>"
    implements CartView
    cart is <CartRelation>
    
    <ItemsPanel> is a Panel
        title is "Items"
        <ItemsList>
    
    <CheckoutPanel> is a Panel
        title is "Checkout"
        <CheckoutForm>
```

## Comparison of Form Types

| Feature | CompositeForm | WizardForm |
|---------|---------------|------------|
| Navigation | Flexible | Sequential |
| Panel Order | Any order | Fixed order |
| Progress Tracking | Optional | Built-in |
| Summary Form | Not applicable | Supported |
| Dashboard View | Supported | Not supported |
| Cart View | Supported | Not supported |
| Use Case | Complex views | Step-by-step |

## Troubleshooting

### Issue: Panels Not Displaying

**Possible Causes:**
- Conditional visibility false
- Panel definition errors
- Content not available
- Permission issues

**Solutions:**
- Check visibility conditions
- Validate panel syntax
- Verify content exists
- Review permissions

### Issue: Navigation Not Working

**Possible Causes:**
- Panel navigation not enabled
- Conditions not met
- Linear mode restricting
- Configuration errors

**Solutions:**
- Enable panel navigation
- Check completion conditions
- Review linear settings
- Validate configuration

### Issue: Save on Next Not Working

**Possible Causes:**
- Not in navigation mode
- Validation errors
- Field rules failing
- Action restrictions

**Solutions:**
- Enable panel navigation
- Fix validation errors
- Review field rules
- Check action permissions

### Issue: Dashboard Layout Problems

**Possible Causes:**
- Column configuration wrong
- Panel sizing incorrect
- Grid system issues
- Responsive problems

**Solutions:**
- Adjust column settings
- Fix panel spans
- Review grid configuration
- Test different screen sizes

### Issue: Context Form Not Showing

**Possible Causes:**
- Context form not defined
- Position not specified
- Form errors
- Visibility conditions

**Solutions:**
- Define context form
- Set position (left/top)
- Validate form syntax
- Check conditions

## Related Concepts

- [Forms](forms.md) - Standard form definitions
- [Action Forms](action-forms.md) - Action-specific forms
- [Form Base Definitions](form-base-definitions.md) - Form components
- [Lists](lists.md) - List definitions
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Actions](actions.md) - Action definitions
- [Navigations](navigations.md) - Navigation definitions
- [UI Definition](ui-definition.md) - UI components

## Summary

Composite Forms provide powerful multi-panel interfaces for complex data entry and display scenarios. CompositeForm offers flexible panel arrangements with support for dashboard views, cart views, and independent panels, while WizardForm provides sequential step-by-step workflows with progress tracking. With features like context forms, panel navigation, conditional visibility, and various layout options, Composite Forms enable sophisticated user interfaces for complex business processes.

**Key Features:**
- Multi-panel layouts
- Dashboard views
- Wizard workflows
- Cart views
- Panel navigation
- Context forms
- Conditional panels
- Progress tracking
- Sub-panels
- Multi-list panels

**Best For:**
- Multi-step workflows
- Dashboard interfaces
- Master-detail views
- Shopping carts
- Complex data entry
- Onboarding processes
- Configuration wizards
- Approval workflows
