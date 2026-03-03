# List Panels

## Overview

List Panels display lists of business class instances within panels on Pages and Composite Forms. They provide flexible configuration for business class selection, context management, list customization, and action integration. List Panels support various data sources including direct business classes, related links, pane references, and agent references.

## Basic Syntax

```
ListPanel ::=
    business class is ( <BusinessClass>[(<AsOfOperator> | .<RelatedLink>)]
                      | pane<PaneNumber>.<RelatedLink>
                      | <Agent>.<RelatedLink>)
    [visible when <SimpleCondition>]
    [panel text is <Message>]
    [show <#> lines]
    [list is (<ListName> | inline)]
    [title is <Message>]
    [display (list | <ChartName>)]
    [Row Dimensions]
        <DimensionField>
        …
    [overview navigation is <NavigationName>]
    [label is <Message>]
    [<ListDefinition>]
    [helper list is <RelatedLink>.(<ListName> | primary)]
    …
    [valid when <SimpleCondition>]
    [title is <Message>]
    [<Action>]
        [invoked.<FullFieldName> = <RelatedValue>]
    [Context]
        <FieldName>
        …
    [Actions]
        ( <CalledOutAction>
        | form is <RelatedLink>.<FormName>
          [<Label>])
        …
```

## Business Class Specification

### Direct Business Class

```
business class is <BusinessClass>[(<AsOfOperator> | .<RelatedLink>)]
```

Specifies the business class directly, optionally with temporal or relational context.

**Options:**
- **<BusinessClass>**: Direct business class reference
- **<BusinessClass> <AsOfOperator>**: Temporal context (as of date)
- **<BusinessClass>.<RelatedLink>**: Related business class via relation

**Examples:**
```
business class is Customer
business class is Order as of current date
business class is Customer.OrderSet
```

### Pane Reference

```
business class is pane<PaneNumber>.<RelatedLink>
```

References a related business class from another pane in a Multi-Pane Panel.

**Pane Numbers:**
- **1**: Upper left
- **2**: Lower left
- **3**: Upper right
- **4**: Lower right

**Example:**
```
business class is pane1.OrderLines
```

### Agent Reference

```
business class is <Agent>.<RelatedLink>
```

References a business class via an agent.

**Example:**
```
business class is CurrentUser.AssignedTasks
```

## List Panel Attributes

### Visibility (MultiListPanel Only)

```
visible when <SimpleCondition>
```

**Valid on MultiListPanel only.** Controls when the list panel is visible.

### Panel Text (MultiListPanel Only)

```
panel text is <Message>
```

**Valid on MultiListPanel only.** Text displayed for the panel.

### Show Lines (MultiListPanel Only)

```
show <#> lines
```

**Valid on MultiListPanel only.** Number of lines to display.

### List Selection

```
list is (<ListName> | inline)
```

Specifies which list to use. Defaults to primary list on resultant business class.

**Options:**
- **<ListName>**: Named list definition
- **inline**: Inline list definition

### Title

```
title is <Message>
```

Defaults to the title of <ListName>.

### Display Mode

```
display (list | <ChartName>)
```

Overrides the list default display mode.

**Options:**
- **list**: Display as list
- **<ChartName>**: Display as chart

### Row Dimensions (CubeView Only)

```
Row Dimensions
    <DimensionField>
    …
```

**Valid on CubeView only.** Overrides the CubeView Row Dimension.

### Overview Navigation (DashBoardPanel Only)

```
overview navigation is <NavigationName>
```

**Valid on DashBoardPanel only.** Navigation target for overview.

### Label

```
label is <Message>
```

Label for the list panel.

### Inline List Definition

```
<ListDefinition>
```

**Valid on inline List only.** Defines the list inline.

## Helper Lists

```
helper list is <RelatedLink>.(<ListName> | primary)
…
[valid when <SimpleCondition>]
[title is <Message>]
```

**Valid on relative pane only.** RelatedLink is relative to pane<PaneNumber>.

Helper lists provide additional related lists that assist with the main list.

**Features:**
- Multiple helper lists
- Conditional validity
- Custom titles
- Relative to pane

## Actions

### Action Invocation

```
<Action>
    [invoked.<FullFieldName> = <RelatedValue>]
```

Specifies an action to invoke with optional parameter values.

**Example:**
```
CREATE
    invoked.Customer = CurrentCustomer
    invoked.OrderDate = current date
```

### Called Out Actions

```
Actions
    ( <CalledOutAction>
    | form is <RelatedLink>.<FormName>
      [<Label>])
    …
```

Explicitly defines which actions to display.

**Options:**
- **<CalledOutAction>**: Standard called out action
- **form is <RelatedLink>.<FormName>**: Form-based action (RelatedLink can only be one of the context fields)

## Context

```
Context
    <FieldName>
    …
```

Defines context fields for the list.

**Context Field Sources:**
- Context session key
- `actor.agent(BusClass).FieldName` lookup
- Current context before Navigation brings up the page

**Requirements:**
- Must be an ontological context field of the business class
- If the list is a CubeView, can also be a dimension

**Example:**
```
Context
    COMPANY
    FISCALPERIOD
    DEPARTMENT
```

## Examples

### Example 1: Simple List Panel

```
business class is Customer
list is CUSTOMERLIST
title is "Customers"
```

### Example 2: List Panel with Context

```
business class is Order
list is ORDERLIST
title is "Orders"

Context
    CUSTOMER
    ORDERSTATUS
```

### Example 3: Related List Panel

```
business class is Customer.OrderSet
list is ORDERLIST
title is "Customer Orders"
```

### Example 4: Temporal List Panel

```
business class is Account as of current date
list is ACCOUNTLIST
title is "Current Accounts"
```

### Example 5: Pane-Referenced List Panel

```
business class is pane1.OrderLines
list is ORDERLINELIST
title is "Order Lines"
```

### Example 6: List Panel with Actions

```
business class is Task
list is TASKLIST
title is "Tasks"

Actions
    CREATE
        invoked.AssignedTo = actor
        invoked.CreatedDate = current date
    COMPLETE
    DELETE
```

### Example 7: Inline List Panel

```
business class is Product
list is inline
title is "Products"

Display Fields
    PRODUCTCODE
    PRODUCTNAME
    CATEGORY
    PRICE
    QUANTITYONHAND
```

### Example 8: CubeView List Panel

```
business class is Sales
list is SALESCUBE
title is "Sales Analysis"

Row Dimensions
    REGION
    PRODUCT
    SALESPERSON

Context
    FISCALPERIOD
    COMPANY
```

### Example 9: List Panel with Helper Lists

```
business class is pane1.Customer
list is CUSTOMERLIST
title is "Customers"

helper list is pane1.Customer.OrderSet.ORDERLIST
    title is "Customer Orders"
    valid when Customer entered

helper list is pane1.Customer.ContactSet.CONTACTLIST
    title is "Customer Contacts"
    valid when Customer entered
```

### Example 10: MultiListPanel with Multiple Lists

```
REPORTS is a MultiListPanel
    title is "Reports"
    
    business class is SalesReport
    list is SALESREPORTLIST
    visible when actor.context.Role = "Sales"
    panel text is "Sales Reports"
    show 10 lines
    
    business class is FinanceReport
    list is FINANCEREPORTLIST
    visible when actor.context.Role = "Finance"
    panel text is "Finance Reports"
    show 10 lines
```

### Example 11: Dashboard List Panel

```
business class is Order
list is RECENTORDERLIST
title is "Recent Orders"
overview navigation is ORDERDETAIL
label is "View All Orders"
show 5 lines

Context
    CUSTOMER
    ORDERSTATUS
```

### Example 12: Agent-Referenced List Panel

```
business class is CurrentUser.AssignedTasks
list is TASKLIST
title is "My Tasks"

Actions
    COMPLETE
    REASSIGN
```

### Example 13: List Panel with Chart Display

```
business class is Sales
list is SALESLIST
title is "Sales"
display SALESCHART

Context
    FISCALPERIOD
    REGION
```

### Example 14: Conditional Helper Lists

```
business class is pane1.Project
list is PROJECTLIST
title is "Projects"

helper list is pane1.Project.TaskSet.TASKLIST
    title is "Project Tasks"
    valid when Project entered

helper list is pane1.Project.MilestoneSet.MILESTONELIST
    title is "Project Milestones"
    valid when Project entered and ShowMilestones = true

helper list is pane1.Project.ResourceSet.RESOURCELIST
    title is "Project Resources"
    valid when Project entered
```

### Example 15: List Panel with Form Actions

```
business class is Customer
list is CUSTOMERLIST
title is "Customers"

Context
    REGION
    CUSTOMERTYPE

Actions
    CREATE
    form is Customer.CUSTOMERDETAIL
        label is "View Details"
    form is Customer.CUSTOMERORDERS
        label is "View Orders"
```

## Use Cases

### Master-Detail Views
- Display related records
- Pane-referenced lists
- Helper lists
- Coordinated views

### Dashboard Panels
- Summary lists
- Recent activity
- Quick access
- Overview navigation

### Multi-List Panels
- Multiple list views
- Conditional lists
- Role-based lists
- Tabbed lists

### Context-Driven Lists
- Filtered by context
- Department-specific
- User-specific
- Date-filtered

### Action Integration
- List with actions
- Form-based actions
- Context-aware actions
- Invoked parameters

## Best Practices

### Business Class Selection

1. **Direct References**
   - Use for primary lists
   - Clear and simple
   - Easy to understand
   - Good performance

2. **Related References**
   - Use for related data
   - Maintain relationships
   - Logical navigation
   - Test thoroughly

3. **Pane References**
   - Use in Multi-Pane Panels
   - Coordinate panes
   - Maintain context
   - Test interactions

### Context Management

1. **Context Fields**
   - Define necessary context
   - Use ontological fields
   - Validate availability
   - Handle missing context

2. **Context Sources**
   - Session keys for preferences
   - Actor context for user data
   - Navigation context for flow
   - Test all sources

3. **CubeView Context**
   - Include dimensions
   - Set appropriate defaults
   - Test with various contexts
   - Validate results

### List Configuration

1. **List Selection**
   - Use appropriate list
   - Consider inline for simple cases
   - Reuse lists when possible
   - Test with data

2. **Display Mode**
   - Choose list or chart
   - Match user needs
   - Test both modes
   - Consider performance

3. **Line Count**
   - Set appropriate count
   - Balance visibility and performance
   - Test with various data
   - Consider screen size

### Helper Lists

1. **Helper List Design**
   - Provide related information
   - Use conditional validity
   - Set clear titles
   - Test relationships

2. **Performance**
   - Limit helper list count
   - Use conditional loading
   - Optimize queries
   - Test with data

### Actions

1. **Action Selection**
   - Provide relevant actions
   - Use called out actions
   - Consider form actions
   - Test thoroughly

2. **Action Parameters**
   - Set appropriate defaults
   - Use invoked parameters
   - Validate values
   - Test execution

3. **Form Actions**
   - Use context fields only
   - Provide clear labels
   - Test navigation
   - Validate forms

### Performance

1. **Optimization**
   - Limit initial data load
   - Use context filtering
   - Optimize queries
   - Cache appropriately

2. **Large Datasets**
   - Use pagination
   - Implement search
   - Filter effectively
   - Monitor performance

### Testing

1. **Functional Testing**
   - Test all configurations
   - Test with various data
   - Test context changes
   - Verify actions

2. **Integration Testing**
   - Test pane interactions
   - Test helper lists
   - Test navigation
   - Verify context flow

## Common Patterns

### Master-Detail Pattern

```
business class is pane1.Customer
list is CUSTOMERLIST
title is "Customers"

helper list is pane1.Customer.OrderSet.ORDERLIST
    title is "Customer Orders"
    valid when Customer entered
```

### Dashboard Pattern

```
business class is Order
list is RECENTORDERLIST
title is "Recent Orders"
overview navigation is ORDERLIST
show 5 lines

Context
    CUSTOMER
```

### Context-Filtered Pattern

```
business class is Task
list is TASKLIST
title is "Tasks"

Context
    ASSIGNEDTO
    STATUS
    DUEDATE
```

### Action-Enabled Pattern

```
business class is Document
list is DOCUMENTLIST
title is "Documents"

Actions
    CREATE
    EDIT
    DELETE
    form is Document.DOCUMENTDETAIL
        label is "View Details"
```

## Troubleshooting

### Issue: List Not Displaying

**Possible Causes:**
- Business class not found
- Context not set
- Visibility condition false
- List definition errors

**Solutions:**
- Verify business class
- Set required context
- Check visibility condition
- Validate list definition

### Issue: Context Not Working

**Possible Causes:**
- Context fields not defined
- Context not available
- Wrong field names
- Context not propagating

**Solutions:**
- Define context fields
- Verify context availability
- Check field names
- Test context flow

### Issue: Pane Reference Not Working

**Possible Causes:**
- Wrong pane number
- Relation not valid
- Pane not populated
- Context mismatch

**Solutions:**
- Verify pane number
- Check relation definition
- Ensure pane has data
- Align contexts

### Issue: Helper Lists Not Showing

**Possible Causes:**
- Validity condition false
- Relation empty
- Context missing
- Configuration errors

**Solutions:**
- Check validity conditions
- Verify relation has data
- Set required context
- Validate configuration

### Issue: Actions Not Available

**Possible Causes:**
- Actions not defined
- Permissions missing
- Context not set
- Action restrictions

**Solutions:**
- Define actions
- Check permissions
- Set required context
- Review restrictions

### Issue: Performance Problems

**Possible Causes:**
- Too much data
- No context filtering
- Complex queries
- Missing indexes

**Solutions:**
- Limit data load
- Apply context filters
- Optimize queries
- Add indexes

## Related Concepts

- [Lists](lists.md) - List definitions
- [Panel Definitions](panel-definitions.md) - Panel types
- [Pages](pages.md) - Page definitions
- [Composite Forms](composite-forms.md) - Multi-panel forms
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Relations](relations.md) - Relationships
- [Actions](actions.md) - Action definitions
- [Conditions](conditions.md) - Conditional logic

## Summary

List Panels display lists of business class instances within panels on Pages and Composite Forms. With flexible configuration for business class selection, context management, list customization, and action integration, List Panels provide powerful data display capabilities. Support for related links, pane references, helper lists, and various display modes enables sophisticated list-based interfaces.

**Key Features:**
- Multiple business class sources
- Context management
- Related links
- Pane references
- Agent references
- Temporal context (as of)
- Helper lists
- Action integration
- Inline lists
- Chart display
- CubeView support
- Overview navigation

**Business Class Sources:**
- Direct business class
- Related business class
- Pane-referenced business class
- Agent-referenced business class
- Temporal business class

**Best For:**
- Master-detail views
- Dashboard panels
- Multi-list panels
- Context-driven lists
- Action integration
- Related data display
- Coordinated views
- Filtered lists

**Advantages:**
- Flexible data sources
- Rich context support
- Helper list capability
- Action integration
- Multiple display modes
- Pane coordination
- Conditional display
- Performance optimization

**Important Notes:**
- Context fields must be ontological (or dimensions for CubeView)
- Helper lists valid on relative pane only
- Form actions can only use context fields for RelatedLink
- Pane references enable coordinated multi-pane views
- Test thoroughly with various contexts and data
