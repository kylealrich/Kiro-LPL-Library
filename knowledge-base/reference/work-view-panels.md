# Work View Panels

## Overview

Work View Panels provide specialized columnar layouts for displaying lists, detail fields, and link displays in coordinated multi-pane views. They are designed for work-focused interfaces where users need to view lists alongside related details or links. Work View Panels support up to 3 columnar panes and enable master-detail workflows with synchronized pane interactions.

## Basic Syntax

```
WorkViewPanel ::=
    ( business class is ( <BusinessClass>[.<RelatedLink>]
                        | pane<PaneNumber>.<RelatedLink>
                        | <Agent>.<RelatedLink>)
      [title is <Message>]
      [show <#> lines]
      [list is (<ListName> | inline)]
      [<ListDefinition>]
      [sort order is (<SetName> | primary)]
      [Context]
          <FieldName>
          …
      [Instance Selection]
          where <Condition>
    | detail fields view of pane<PaneNumber>
    | link display view of pane<PaneNumber>)
```

## Work View Pane Numbers

```
WorkViewPaneNumber ::= (1 | 2 | 3)
```

WorkViewPanes are columnar. There can be only up to **3 WorkViewPanes**.

## Panel Types

### List View Panel

```
business class is ( <BusinessClass>[.<RelatedLink>]
                  | pane<PaneNumber>.<RelatedLink>
                  | <Agent>.<RelatedLink>)
[title is <Message>]
[show <#> lines]
[list is (<ListName> | inline)]
[<ListDefinition>]
[sort order is (<SetName> | primary)]
[Context]
    <FieldName>
    …
[Instance Selection]
    where <Condition>
```

Displays a list of business class instances.

#### List View Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `business class is` | Business class to display | Required |
| `title is <Message>` | Panel title | Title of ListName |
| `show <#> lines` | Number of lines to display | 15 lines |
| `list is` | List definition to use | Primary list |
| `sort order is` | Sort order for list | Default sort |

#### Business Class Sources

- **<BusinessClass>**: Direct business class
- **<BusinessClass>.<RelatedLink>**: Related business class
- **pane<PaneNumber>.<RelatedLink>**: Related from another pane
- **<Agent>.<RelatedLink>**: Agent-referenced business class

#### Context

```
Context
    <FieldName>
    …
```

Defines context fields for the list.

#### Instance Selection

```
Instance Selection
    where <Condition>
```

**Additive to the list definition's Instance Selection.** Provides additional filtering beyond the list's built-in filtering.

#### Inline List

```
list is inline
<ListDefinition>
```

**Valid on inline List only.** Defines the list inline.

### Detail Fields View

```
detail fields view of pane<PaneNumber>
```

Displays detail fields from the selected record in another pane.

**Features:**
- Shows detailed information
- Synchronized with pane selection
- Automatic field display
- Read-only or editable fields

### Link Display View

```
link display view of pane<PaneNumber>
```

Displays links related to the selected record in another pane.

**Features:**
- Shows related links
- Navigation options
- Related records
- Quick access

## Examples

### Example 1: Simple Work View

```
TASKWORKVIEW is a Page
    title is "Task Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Task
            list is TASKLIST
            show 20 lines
            
            Context
                ASSIGNEDTO
                STATUS
        
        main column
            detail fields view of pane 1
```

### Example 2: Three-Pane Work View

```
ORDERWORKVIEW is a Page
    title is "Order Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Customer
            list is CUSTOMERLIST
            show 15 lines
        
        main column
            business class is pane 1.OrderSet
            list is ORDERLIST
            show 15 lines
        
        right column
            detail fields view of pane 2
```

### Example 3: Work View with Instance Selection

```
PROJECTWORKVIEW is a Page
    title is "Project Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Project
            list is PROJECTLIST
            show 20 lines
            
            Context
                PROJECTMANAGER
                STATUS
            
            Instance Selection
                where Status = "Active"
        
        main column
            business class is pane 1.TaskSet
            list is TASKLIST
            show 15 lines
            
            Instance Selection
                where TaskStatus != "Completed"
```

### Example 4: Work View with Link Display

```
CUSTOMERWORKVIEW is a Page
    title is "Customer Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Customer
            list is CUSTOMERLIST
            show 20 lines
        
        main column
            business class is pane 1.OrderSet
            list is ORDERLIST
            show 15 lines
        
        right column
            link display view of pane 2
```

### Example 5: Work View with Inline List

```
EMPLOYEEWORKVIEW is a Page
    title is "Employee Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Employee
            list is inline
            show 20 lines
            
            Display Fields
                EMPLOYEE
                EMPLOYEENAME
                DEPARTMENT
                TITLE
        
        main column
            detail fields view of pane 1
```

### Example 6: Work View with Sort Order

```
SALESWORKVIEW is a Page
    title is "Sales Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Salesperson
            list is SALESPERSONLIST
            show 15 lines
            sort order is primary
        
        main column
            business class is pane 1.OpportunitySet
            list is OPPORTUNITYLIST
            show 15 lines
            sort order is BYSTAGE
```

### Example 7: Agent-Referenced Work View

```
MYWORKVIEW is a Page
    title is "My Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is CurrentUser.AssignedProjects
            list is PROJECTLIST
            show 20 lines
        
        main column
            business class is pane 1.TaskSet
            list is TASKLIST
            show 15 lines
            
            Instance Selection
                where AssignedTo = actor
```

### Example 8: Filtered Work View

```
SUPPORTWORKVIEW is a Page
    title is "Support Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Ticket
            list is TICKETLIST
            show 20 lines
            
            Context
                ASSIGNEDTO
                PRIORITY
                STATUS
            
            Instance Selection
                where Status != "Closed"
        
        main column
            detail fields view of pane 1
```

### Example 9: Hierarchical Work View

```
ACCOUNTWORKVIEW is a Page
    title is "Account Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Account
            list is ACCOUNTLIST
            show 20 lines
            
            Instance Selection
                where ParentAccount = blank
        
        main column
            business class is pane 1.SubAccountSet
            list is SUBACCOUNTLIST
            show 15 lines
        
        right column
            detail fields view of pane 2
```

### Example 10: Document Work View

```
DOCUMENTWORKVIEW is a Page
    title is "Document Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is DocumentFolder
            list is FOLDERLIST
            show 15 lines
        
        main column
            business class is pane 1.DocumentSet
            list is DOCUMENTLIST
            show 20 lines
            
            Context
                DOCUMENTTYPE
                STATUS
        
        right column
            detail fields view of pane 2
```

### Example 11: Work View with Multiple Contexts

```
INVENTORYWORKVIEW is a Page
    title is "Inventory Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Warehouse
            list is WAREHOUSELIST
            show 10 lines
        
        main column
            business class is pane 1.ProductSet
            list is PRODUCTLIST
            show 20 lines
            
            Context
                CATEGORY
                SUPPLIER
            
            Instance Selection
                where QuantityOnHand > 0
        
        right column
            detail fields view of pane 2
```

### Example 12: Work View with Custom Titles

```
ASSETWORKVIEW is a Page
    title is "Asset Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Location
            list is LOCATIONLIST
            title is "Locations"
            show 15 lines
        
        main column
            business class is pane 1.AssetSet
            list is ASSETLIST
            title is "Assets at Location"
            show 20 lines
        
        right column
            detail fields view of pane 2
```

### Example 13: Work View with Related Links

```
PROJECTMANAGEMENTVIEW is a Page
    title is "Project Management"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Project
            list is PROJECTLIST
            show 15 lines
            
            Context
                PROJECTMANAGER
        
        main column
            business class is pane 1.TaskSet
            list is TASKLIST
            show 15 lines
        
        right column
            link display view of pane 2
```

### Example 14: Work View with Inline and Named Lists

```
TEAMWORKVIEW is a Page
    title is "Team Work View"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Team
            list is inline
            show 10 lines
            
            Display Fields
                TEAM
                TEAMNAME
                TEAMLEAD
                MEMBERCOUNT
        
        main column
            business class is pane 1.MemberSet
            list is MEMBERLIST
            show 20 lines
        
        right column
            detail fields view of pane 2
```

### Example 15: Complex Work View

```
SALESMANAGEMENTVIEW is a Page
    title is "Sales Management"
    
    WORKSPACE is a ColumnarPanel
        left column
            business class is Region
            list is REGIONLIST
            show 10 lines
            sort order is primary
        
        main column
            business class is pane 1.SalespersonSet
            list is SALESPERSONLIST
            title is "Sales Team"
            show 15 lines
            
            Context
                REGION
            
            Instance Selection
                where Active = true
        
        right column
            business class is pane 2.OpportunitySet
            list is OPPORTUNITYLIST
            title is "Opportunities"
            show 20 lines
            
            Instance Selection
                where Stage != "Closed Won" and Stage != "Closed Lost"
```

## Use Cases

### Master-Detail Views
- List with detail display
- Hierarchical data
- Related records
- Coordinated views

### Work Management
- Task management
- Project tracking
- Ticket handling
- Document management

### Data Exploration
- Browse and explore
- Drill-down navigation
- Related data viewing
- Quick access

### Hierarchical Navigation
- Parent-child relationships
- Organizational structures
- Category browsing
- Nested data

### Multi-Level Filtering
- Progressive filtering
- Context-based filtering
- Related data filtering
- Additive selection

## Best Practices

### Panel Organization

1. **Columnar Layout**
   - Left: Master list
   - Main: Related list or details
   - Right: Details or links
   - Logical flow

2. **Pane Count**
   - Maximum 3 panes
   - Use 2 for simple views
   - Use 3 for complex views
   - Test usability

3. **Pane Sizing**
   - Balance pane widths
   - Consider content
   - Test with data
   - Responsive design

### List Configuration

1. **Line Count**
   - Default: 15 lines
   - Adjust based on content
   - Consider screen size
   - Test with users

2. **List Selection**
   - Use appropriate lists
   - Consider inline for simple cases
   - Reuse lists when possible
   - Test with data

3. **Sort Order**
   - Set logical sort
   - Use named sets
   - Consider user needs
   - Test sorting

### Context Management

1. **Context Fields**
   - Define necessary context
   - Use across panes
   - Validate availability
   - Handle missing context

2. **Context Propagation**
   - Understand flow
   - Test propagation
   - Verify values
   - Document behavior

### Instance Selection

1. **Additive Filtering**
   - Understand additive nature
   - Combine with list filtering
   - Test combined filters
   - Document logic

2. **Filter Design**
   - Keep filters simple
   - Use meaningful conditions
   - Test with data
   - Consider performance

### Pane Coordination

1. **Pane References**
   - Use correct pane numbers
   - Coordinate selections
   - Test interactions
   - Handle empty panes

2. **Detail Views**
   - Use for detailed information
   - Coordinate with selection
   - Test display
   - Handle no selection

3. **Link Views**
   - Use for navigation
   - Provide related links
   - Test navigation
   - Validate links

### Performance

1. **Optimization**
   - Limit initial data load
   - Use context filtering
   - Optimize queries
   - Cache appropriately

2. **Large Datasets**
   - Use pagination
   - Implement filtering
   - Optimize queries
   - Monitor performance

### User Experience

1. **Navigation**
   - Clear pane purpose
   - Intuitive flow
   - Easy selection
   - Quick access

2. **Feedback**
   - Show selection state
   - Indicate loading
   - Handle errors
   - Provide messages

### Testing

1. **Functional Testing**
   - Test pane coordination
   - Test selection
   - Test filtering
   - Verify display

2. **Integration Testing**
   - Test pane interactions
   - Test context flow
   - Test navigation
   - Verify behavior

## Common Patterns

### Master-Detail Pattern

```
left column
    business class is <MasterClass>
    list is <MasterList>
    show 20 lines

main column
    detail fields view of pane 1
```

### Master-Related-Detail Pattern

```
left column
    business class is <MasterClass>
    list is <MasterList>
    show 15 lines

main column
    business class is pane 1.<RelatedLink>
    list is <RelatedList>
    show 15 lines

right column
    detail fields view of pane 2
```

### Hierarchical Pattern

```
left column
    business class is <ParentClass>
    list is <ParentList>
    show 15 lines

main column
    business class is pane 1.<ChildLink>
    list is <ChildList>
    show 20 lines
```

### Filtered Work View Pattern

```
left column
    business class is <BusinessClass>
    list is <ListName>
    show 20 lines
    
    Context
        <ContextFields>
    
    Instance Selection
        where <Condition>

main column
    detail fields view of pane 1
```

## Troubleshooting

### Issue: Panes Not Coordinating

**Possible Causes:**
- Wrong pane number
- Selection not made
- Relation not valid
- Context mismatch

**Solutions:**
- Verify pane numbers
- Select record in pane
- Check relation definition
- Align contexts

### Issue: Detail View Not Displaying

**Possible Causes:**
- No selection in source pane
- Pane number incorrect
- Fields not available
- Display errors

**Solutions:**
- Select record in source pane
- Verify pane number
- Check field availability
- Review display configuration

### Issue: Instance Selection Not Working

**Possible Causes:**
- Condition syntax error
- Field not available
- Context not set
- Logic error

**Solutions:**
- Fix condition syntax
- Verify field availability
- Set required context
- Review logic

### Issue: Too Many Panes

**Possible Causes:**
- More than 3 panes defined
- Configuration error
- Layout issues

**Solutions:**
- Limit to 3 panes
- Review configuration
- Simplify layout
- Test with 2-3 panes

### Issue: Performance Problems

**Possible Causes:**
- Too much data
- No filtering
- Complex queries
- Missing indexes

**Solutions:**
- Limit data load
- Apply filtering
- Optimize queries
- Add indexes

## Related Concepts

- [Panel Definitions](panel-definitions.md) - Panel types
- [List Panels](list-panels.md) - List panel definitions
- [Pages](pages.md) - Page definitions
- [Composite Forms](composite-forms.md) - Multi-panel forms
- [Lists](lists.md) - List definitions
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Relations](relations.md) - Relationships
- [Conditions](conditions.md) - Conditional logic

## Summary

Work View Panels provide specialized columnar layouts for displaying lists, detail fields, and link displays in coordinated multi-pane views. Designed for work-focused interfaces, Work View Panels support up to 3 columnar panes and enable master-detail workflows with synchronized pane interactions. With support for context management, instance selection, and various business class sources, Work View Panels streamline data exploration and management tasks.

**Key Features:**
- Columnar layout (up to 3 panes)
- List view panels
- Detail fields view
- Link display view
- Pane coordination
- Context management
- Instance selection (additive)
- Multiple business class sources
- Inline lists
- Sort order control

**Panel Types:**
- List view panel
- Detail fields view
- Link display view

**Business Class Sources:**
- Direct business class
- Related business class
- Pane-referenced business class
- Agent-referenced business class

**Best For:**
- Master-detail views
- Work management
- Data exploration
- Hierarchical navigation
- Multi-level filtering
- Task management
- Project tracking
- Document management

**Advantages:**
- Coordinated panes
- Synchronized selection
- Flexible layout
- Context support
- Additive filtering
- Multiple views
- Easy navigation
- Work-focused design

**Important Notes:**
- Maximum 3 WorkViewPanes (columnar)
- Instance Selection is additive to list definition
- Detail fields view synchronized with pane selection
- Link display view shows related links
- Test pane coordination thoroughly
- Consider performance with large datasets
- Validate pane references
- Handle empty pane selections
