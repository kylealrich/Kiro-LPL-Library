# Pages

## Overview

Pages provide top-level UI containers that organize multiple panels into cohesive interfaces. Unlike Composite Forms which are scoped to a specific business class, Pages are independent entities that can aggregate content from multiple business classes. Pages support various panel types including dashboard panels, multi-pane panels, multi-list panels, and columnar panels.

## Page Files

Page definition files must have an extension of `.page` and are found in `com/lawson/forms/<ModuleName>`.

**Important:** Page names exist in the same namespace as BusinessClass names and BusinessTask names.

## Pages vs Composite Forms

### Similarities
- Both are made up of panels
- Both provide multi-panel interfaces
- Both support various layout options

### Key Differences

| Aspect | Composite Form | Page |
|--------|----------------|------|
| **Scope** | Defined within BusinessClass/Task/Field | Independent entity at same level as BusinessClass |
| **Context** | Single business class context | Each panel designates its own context |
| **References** | All references within business class context | Each panel has independent context |
| **Multi-Pane Panels** | Not supported | Supported |
| **Ontology** | Business class scoped | Independent scoped |

**Note:** The multi-pane restriction on Composite Forms is not ontologically required; it is a simplification.

## Page Types

### Splash Page

```
<PageName> is a SplashPage
```

Landing page or dashboard-style page with inline panels (no tabs).

### Standard Page

```
<PageName> is a Page
```

Standard page with tabbed or organized panels.

## Basic Syntax

### Splash Page

```
<PageName> is a SplashPage
    [title is <Message>]
    [allow anonymous access]
    [show panel control on left]
    [Context Form]
        Layout
            <FormLayout>
            …
    <PanelName> is a Panel
    …
        [title is <Message>]
        [mouse over text is <Message>]
        <MenuPanel>
        [show in column(1|2|3|4|5|6|7|8|9|10|11|12)]
```

**Note:** Multiple panels flow on the page inline rather than have tabs.

### Standard Page

```
<PageName> is a Page
    [title is <Message>]
    [allow anonymous access]
    [show panel control on left]
    [is report [for <WebAppName>[,<WebAppName>]…]
    [category is <Message>]
    [orientation is (portrait | landscape)]
    [font offset is <Number>]
    [cart form is <BusinessClass>.<FormName>]
    [background is (<ImageName> | theme color)]
    [span header]
    [foreground is (dark | light)]
    [Context Form]
        [background is (<ImageName> | theme color)]
        [span header]
        [foreground is (dark | light)]
        Layout
            <FormLayout>
            …
    <PanelName> is a Panel
    …
        [title is <Message>]
        [mouse over text is <Message>]
        <PanelDefinition>
```

## Page Attributes

### Title

```
title is <Message>
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `title is <Message>` | Page title | PageName |

### Anonymous Access

```
allow anonymous access
```

Allows anonymous users to access the page without authentication.

### Panel Control

```
show panel control on left
```

Displays panel navigation control on the left side.

### Report Configuration

```
is report [for <WebAppName>[,<WebAppName>]…]
category is <Message>
orientation is (portrait | landscape)
font offset is <Number>
```

Configures the page as a report.

| Attribute | Description |
|-----------|-------------|
| `is report` | Designates as report |
| `for <WebAppName>` | Specific web applications |
| `category is <Message>` | Report category |
| `orientation is` | Page orientation |
| `font offset is <Number>` | Font size adjustment |

### Cart Form

```
cart form is <BusinessClass>.<FormName>
```

FormName must be a CompositeForm that implements CartView. Enables shopping cart functionality.

### Background and Foreground

```
background is (<ImageName> | theme color)
span header
foreground is (dark | light)
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `background is` | Background image or theme color | None |
| `span header` | Spans header across page | Not spanned |
| `foreground is` | Text color | dark |

## Context Form

```
Context Form
    [background is (<ImageName> | theme color)]
    [span header]
    [foreground is (dark | light)]
    Layout
        <FormLayout>
        …
```

Provides context fields that are available across all panels and panes.

**Valid Fields:** The set of all Context fields defined on all Panels and panes.

**Features:**
- Shared context across panels
- Custom background and foreground
- Standard form layout
- Available to all panel content

## Panel Types

### Standard Panel

```
<PanelName> is a Panel
…
    [title is <Message>]
    [mouse over text is <Message>]
    <PanelDefinition>
```

Basic panel with single content area.

### Dashboard Panel

```
<PanelName> is a DashBoardPanel
…
    [title is <Message>]
    [mouse over text is <Message>]
    [show (3 | 4) columns]
    [use 12 column grid]
    [background is (<ImageName> | theme color)]
    [print as dashboard]
    [compress columns]
    <PaneName> is a Pane
    …
        [title is <Message>]
        [(double width | triple width | quad width)]
        [span (1|2|3|4|5|6|7|8|9|10|11|12) columns]
        [double height]
        [show in column(1|2|3|4|5|6|7|8|9|10|11|12)]
        <PanelDefinition>
```

Dashboard-style panel with multiple panes in grid layout.

**Attributes:**
- **show (3 | 4) columns**: Number of columns (default: 3)
- **use 12 column grid**: Use 12-column grid system
- **background**: Background image or theme color
- **print as dashboard**: Optimize for dashboard printing
- **compress columns**: Compress column spacing

**Pane Sizing:**
- **double/triple/quad width**: Width multipliers
- **span columns**: Specific column span (1-12)
- **double height**: Double height
- **show in column**: Position in specific column (1-12)

### Multi-Pane Panel

```
<PanelName> is a MultiPanePanel
…
    [title is <Message>]
    [mouse over text is <Message>]
    [vertical split is <Number>/<Number>]
    pane <PaneNumber>
    …
        <PanelDefinition>
```

Panel with multiple panes in split layout.

**Pane Numbers:**
- **1**: Upper left
- **2**: Lower left
- **3**: Upper right
- **4**: Lower right

**Vertical Split:**
Default is 50/50. Specifies the split ratio between top and bottom.

**Pane Interaction:**
Accomplished through inferencing how actions relate to each other through the LPL business class action specification.

### Multi-List Panel

```
<PanelName> is a MultiListPanel
…
    [title is <Message>]
    [mouse over text is <Message>]
    [(valid | visible) when <SimpleCondition>]
    <ListPanel>
    …
```

Panel containing multiple lists.

### Columnar Panel

```
<PanelName> is a ColumnarPanel
…
    [title is <Message>]
    [mouse over text is <Message>]
    [left column]
        <PanelDefinition>
        …
    main column
        <PanelDefinition>
        …
    [right column]
        <PanelDefinition>
        …
```

Panel with columnar layout (left, main, right).

**Note:** Left and right columns can have multiple panels if each one is a FixedSizeList.

## Panel Definition

Panel definitions specify the content of panels and panes. See [Composite Forms](composite-forms.md) for detailed panel definition syntax, as it is shared between Pages and Composite Forms.

## Pane Numbers

### Standard Pane Numbers

```
PaneNumber ::= (1 | 2 | 3 | 4)
```

- **1**: Upper left
- **2**: Lower left
- **3**: Upper right
- **4**: Lower right

The current metaphor for MultiPanePanel panes is a four-quadrant layout.

### Work View Pane Numbers

```
WorkViewPaneNumber ::= (1 | 2 | 3)
```

WorkViewPanes are columnar. There can be only up to 3 WorkViewPanes.

## Examples

### Example 1: Simple Splash Page

```
HOMEPAGE is a SplashPage
    title is "Home"
    
    WELCOME is a Panel
        title is "Welcome"
        WELCOMEFORM
    
    QUICKLINKS is a Panel
        title is "Quick Links"
        QUICKLINKLIST
    
    RECENTACTIVITY is a Panel
        title is "Recent Activity"
        ACTIVITYLIST
```

### Example 2: Dashboard Page

```
DASHBOARD is a Page
    title is "Dashboard"
    
    Context Form
        Layout
            two column
                COMPANY
                FISCALPERIOD
    
    METRICS is a DashBoardPanel
        title is "Key Metrics"
        show 3 columns
        
        SALES is a Pane
            title is "Sales"
            SALESCHART
        
        ORDERS is a Pane
            title is "Orders"
            ORDERCHART
        
        REVENUE is a Pane
            title is "Revenue"
            REVENUECHART
    
    ALERTS is a Panel
        title is "Alerts"
        ALERTLIST
```

### Example 3: Multi-Pane Panel Page

```
WORKVIEW is a Page
    title is "Work View"
    
    WORKSPACE is a MultiPanePanel
        title is "Workspace"
        vertical split is 60/40
        
        pane 1
            TASKLIST
        
        pane 2
            NOTESFORM
        
        pane 3
            TASKDETAIL
        
        pane 4
            ATTACHMENTLIST
```

### Example 4: Columnar Panel Page

```
PROJECTVIEW is a Page
    title is "Project View"
    
    PROJECTLAYOUT is a ColumnarPanel
        title is "Project Layout"
        
        left column
            PROJECTLIST
        
        main column
            PROJECTDETAIL
        
        right column
            PROJECTTASKS
```

### Example 5: Page with Context Form

```
SALESANALYSIS is a Page
    title is "Sales Analysis"
    
    Context Form
        background is theme color
        Layout
            three column
                REGION
                SALESPERSON
                DATERANGE
    
    SUMMARY is a Panel
        title is "Summary"
        SALESSUMMARY
    
    DETAILS is a Panel
        title is "Details"
        SALESDETAILLIST
    
    TRENDS is a Panel
        title is "Trends"
        SALESTRENDCHART
```

### Example 6: Report Page

```
FINANCIALREPORT is a Page
    title is "Financial Report"
    is report
    category is "Financial Reports"
    orientation is landscape
    
    Context Form
        Layout
            two column
                COMPANY
                FISCALPERIOD
    
    SUMMARY is a Panel
        title is "Summary"
        FINANCIALSUMMARY
    
    DETAILS is a Panel
        title is "Details"
        FINANCIALDETAILLIST
```

### Example 7: Shopping Cart Page

```
SHOPPINGCART is a Page
    title is "Shopping Cart"
    cart form is Order.ORDERCART
    
    CART is a Panel
        title is "Cart"
        CARTFORM
```

### Example 8: Anonymous Access Page

```
PUBLICINFO is a SplashPage
    title is "Public Information"
    allow anonymous access
    
    INFO is a Panel
        title is "Information"
        PUBLICINFOFORM
    
    CONTACT is a Panel
        title is "Contact Us"
        CONTACTFORM
```

### Example 9: Page with Panel Control

```
ADMINPANEL is a Page
    title is "Administration"
    show panel control on left
    
    USERS is a Panel
        title is "Users"
        USERLIST
    
    ROLES is a Panel
        title is "Roles"
        ROLELIST
    
    PERMISSIONS is a Panel
        title is "Permissions"
        PERMISSIONLIST
    
    SETTINGS is a Panel
        title is "Settings"
        SETTINGSFORM
```

### Example 10: Complex Dashboard Page

```
EXECUTIVE is a Page
    title is "Executive Dashboard"
    background is theme color
    span header
    foreground is light
    
    Context Form
        background is theme color
        Layout
            single column
                FISCALPERIOD
    
    OVERVIEW is a DashBoardPanel
        title is "Overview"
        show 4 columns
        use 12 column grid
        
        REVENUE is a Pane
            title is "Revenue"
            span 6 columns
            REVENUECHART
        
        PROFIT is a Pane
            title is "Profit"
            span 6 columns
            PROFITCHART
        
        ORDERS is a Pane
            title is "Orders"
            span 3 columns
            ORDERSUMMARY
        
        CUSTOMERS is a Pane
            title is "Customers"
            span 3 columns
            CUSTOMERSUMMARY
        
        PRODUCTS is a Pane
            title is "Products"
            span 3 columns
            PRODUCTSUMMARY
        
        REGIONS is a Pane
            title is "Regions"
            span 3 columns
            REGIONSUMMARY
    
    DETAILS is a MultiListPanel
        title is "Details"
        
        SALESLIST
            visible when ShowSales = true
        
        ORDERLIST
            visible when ShowOrders = true
```

## Use Cases

### Dashboards
- Executive dashboards
- Operational dashboards
- Analytics dashboards
- KPI displays

### Work Views
- Task management
- Project views
- Document management
- Workflow interfaces

### Landing Pages
- Home pages
- Portal pages
- Welcome screens
- Navigation hubs

### Reports
- Financial reports
- Sales reports
- Operational reports
- Custom reports

### Multi-Context Views
- Cross-entity views
- Aggregated data
- Comparative analysis
- Consolidated displays

## Best Practices

### Page Design

1. **Purpose and Scope**
   - Define clear page purpose
   - Determine required contexts
   - Plan panel organization
   - Consider user workflow

2. **Panel Selection**
   - Choose appropriate panel types
   - Use dashboard panels for metrics
   - Use multi-pane for related content
   - Use columnar for master-detail

3. **Context Forms**
   - Provide necessary context
   - Keep concise
   - Use clear labels
   - Update dynamically

### Layout Organization

1. **Panel Arrangement**
   - Logical panel order
   - Group related content
   - Balance information density
   - Consider screen size

2. **Dashboard Layouts**
   - Use appropriate columns
   - Balance pane sizes
   - Consider responsive design
   - Test various resolutions

3. **Multi-Pane Layouts**
   - Plan pane interaction
   - Set appropriate splits
   - Consider content size
   - Test usability

### Context Management

1. **Context Fields**
   - Define shared context
   - Use across panels
   - Update consistently
   - Validate appropriately

2. **Panel Contexts**
   - Each panel has own context
   - Plan context relationships
   - Handle context changes
   - Test interactions

### Performance

1. **Optimization**
   - Lazy load panels
   - Minimize initial load
   - Cache appropriately
   - Test with realistic data

2. **Panel Loading**
   - Load visible panels first
   - Defer hidden panels
   - Provide feedback
   - Handle errors

### Navigation

1. **Panel Navigation**
   - Clear panel titles
   - Logical tab order
   - Enable quick access
   - Provide breadcrumbs

2. **Panel Control**
   - Use when many panels
   - Position appropriately
   - Test usability
   - Consider mobile

### Accessibility

1. **Keyboard Navigation**
   - Tab through panels
   - Keyboard shortcuts
   - Focus management
   - Test thoroughly

2. **Screen Readers**
   - Proper labels
   - Panel descriptions
   - Content structure
   - Test with tools

## Common Patterns

### Dashboard Pattern

```
<PageName>DASHBOARD is a Page
    title is "<Page> Dashboard"
    
    Context Form
        Layout
            <context fields>
    
    METRICS is a DashBoardPanel
        show 3 columns
        <metric panes>
    
    DETAILS is a Panel
        <detail content>
```

### Work View Pattern

```
<PageName>VIEW is a Page
    title is "<Page> View"
    
    WORKSPACE is a MultiPanePanel
        vertical split is 60/40
        pane 1
            <list>
        pane 3
            <detail>
```

### Master-Detail Pattern

```
<PageName>DETAIL is a Page
    title is "<Page> Detail"
    
    LAYOUT is a ColumnarPanel
        left column
            <master list>
        main column
            <detail form>
        right column
            <related content>
```

### Landing Page Pattern

```
<PageName>HOME is a SplashPage
    title is "Home"
    
    <Panel1> is a Panel
        <content1>
    
    <Panel2> is a Panel
        <content2>
```

## Comparison with Composite Forms

| Feature | Page | Composite Form |
|---------|------|----------------|
| Scope | Independent entity | Business class scoped |
| Context | Each panel has own context | Single business class context |
| Multi-Pane Panels | Supported | Not supported |
| Namespace | Same as BusinessClass | Within BusinessClass |
| Use Case | Cross-entity views | Single entity views |
| Flexibility | High | Medium |

## Troubleshooting

### Issue: Panels Not Displaying

**Possible Causes:**
- Context not set
- Panel definition errors
- Visibility conditions
- Permission issues

**Solutions:**
- Set panel context
- Validate panel syntax
- Check conditions
- Review permissions

### Issue: Context Not Working

**Possible Causes:**
- Context form errors
- Field not in context
- Context not propagating
- Panel context mismatch

**Solutions:**
- Fix context form
- Add field to context
- Check propagation
- Align panel contexts

### Issue: Multi-Pane Layout Problems

**Possible Causes:**
- Pane numbers wrong
- Split ratio incorrect
- Content too large
- Interaction issues

**Solutions:**
- Verify pane numbers
- Adjust split ratio
- Resize content
- Test interactions

### Issue: Performance Problems

**Possible Causes:**
- Too many panels
- Heavy panel content
- No lazy loading
- Large datasets

**Solutions:**
- Reduce panel count
- Optimize content
- Implement lazy loading
- Limit data

### Issue: Navigation Not Working

**Possible Causes:**
- Panel control not enabled
- Tab order wrong
- Panel titles missing
- Configuration errors

**Solutions:**
- Enable panel control
- Fix tab order
- Add panel titles
- Validate configuration

## Related Concepts

- [Composite Forms](composite-forms.md) - Multi-panel forms
- [Forms](forms.md) - Standard form definitions
- [Lists](lists.md) - List definitions
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Navigations](navigations.md) - Navigation definitions
- [UI Definition](ui-definition.md) - UI components

## Summary

Pages provide top-level UI containers that organize multiple panels into cohesive interfaces. Unlike Composite Forms which are scoped to a specific business class, Pages are independent entities that can aggregate content from multiple business classes. With support for various panel types including dashboard panels, multi-pane panels, multi-list panels, and columnar panels, Pages enable sophisticated cross-entity views and complex user interfaces.

**Key Features:**
- Independent entity scope
- Multiple panel types
- Context forms
- Multi-pane panels
- Dashboard layouts
- Columnar layouts
- Panel control
- Anonymous access
- Report configuration
- Cart integration

**Best For:**
- Dashboards
- Work views
- Landing pages
- Reports
- Multi-context views
- Cross-entity displays
- Portal interfaces
- Complex workflows

**Advantages:**
- Independent scope
- Flexible context
- Multi-pane support
- Cross-entity views
- Rich panel types
- Sophisticated layouts
- High flexibility
- Powerful aggregation

**Key Differences from Composite Forms:**
- Independent entity vs business class scoped
- Each panel has own context vs single context
- Multi-pane panels supported vs not supported
- Cross-entity views vs single entity views
