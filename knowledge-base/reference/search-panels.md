# Search Panels

## Overview

Search Panels provide integrated search and list display within panels on Pages and Composite Forms. They combine a search form with a results list, enabling users to search for records and view results in a single interface. Search Panels support context management, action integration, and automatic context propagation to create actions.

## Basic Syntax

```
SearchPanel ::=
    search form is <BusinessClass>.<FormName>
    [list is (<ListName> | inline)]
    [<ListDefinition>]
    [Context]
        <FieldName>
        …
    [Actions]
        ( [action is] [<StateName>.]<ActionName>
          …
          [<Label>]
        | form is <RelatedLink>.<FormName>
          …
          [<Label>])
```

## Search Panel Components

### Search Form

```
search form is <BusinessClass>.<FormName>
```

Specifies the business class and search form to use.

**Requirements:**
- BusinessClass must be specified
- FormName must be a SearchForm for that business class

**Example:**
```
search form is Customer.CUSTOMERSEARCH
```

### List

```
list is (<ListName> | inline)
```

Specifies which list to display search results. Defaults to primary list on resultant business class.

**Options:**
- **<ListName>**: Named list definition
- **inline**: Inline list definition

### Inline List Definition

```
<ListDefinition>
```

**Valid on inline List only.** Defines the results list inline.

**Example:**
```
list is inline

Display Fields
    CUSTOMER
    CUSTOMERNAME
    CITY
    STATE
    BALANCE
```

## Context

```
Context
    <FieldName>
    …
```

Defines context fields for the search panel.

**Context Field Behavior:**
- Provides context to search form
- Provides context to results list
- **Automatically propagates to Create actions**

**Important:** A context field will implicitly be used as a 'fixed' context to any Create action that is executed. If Context of 'Actor' is specified and a Create action is executed, then the Actor field in the business class will be populated with the Actor context.

**Example:**
```
Context
    COMPANY
    DEPARTMENT
    FISCALPERIOD
```

## Actions

```
Actions
    ( [action is] [<StateName>.]<ActionName>
      …
      [<Label>]
    | form is <RelatedLink>.<FormName>
      …
      [<Label>])
```

Defines actions available in the search panel.

### Action Types

#### Standard Actions

```
[action is] [<StateName>.]<ActionName>
    …
    [<Label>]
```

Standard business class actions.

**Example:**
```
Actions
    CREATE
    EDIT
    DELETE
```

#### Form Actions

```
form is <RelatedLink>.<FormName>
    …
    [<Label>]
```

Form-based actions. RelatedLink can only be one of the context fields.

**Example:**
```
Actions
    form is Customer.CUSTOMERDETAIL
        label is "View Details"
```

## Context Propagation to Create Actions

A key feature of Search Panels is automatic context propagation to Create actions.

### How It Works

1. Context fields are defined in the Search Panel
2. User executes a Create action
3. Context field values are automatically populated in the new record

### Example

```
SearchPanel
    search form is Task.TASKSEARCH
    list is TASKLIST
    
    Context
        ASSIGNEDTO
        PROJECT
        PRIORITY
    
    Actions
        CREATE
```

When the user clicks CREATE:
- ASSIGNEDTO field is automatically set from context
- PROJECT field is automatically set from context
- PRIORITY field is automatically set from context

### Special Context: Actor

If Context includes 'Actor' and a Create action is executed:
- The Actor field in the business class is populated with the current actor context
- Useful for automatically assigning created records to the current user

**Example:**
```
Context
    ACTOR
    DEPARTMENT

Actions
    CREATE
```

When CREATE is executed:
- ACTOR field is set to current user
- DEPARTMENT field is set from context

## Examples

### Example 1: Simple Search Panel

```
search form is Customer.CUSTOMERSEARCH
list is CUSTOMERLIST

Actions
    CREATE
    EDIT
    DELETE
```

### Example 2: Search Panel with Context

```
search form is Order.ORDERSEARCH
list is ORDERLIST

Context
    CUSTOMER
    ORDERSTATUS
    FISCALPERIOD

Actions
    CREATE
    EDIT
    DELETE
```

### Example 3: Search Panel with Inline List

```
search form is Product.PRODUCTSEARCH
list is inline

Display Fields
    PRODUCTCODE
    PRODUCTNAME
    CATEGORY
    PRICE
    QUANTITYONHAND

Context
    CATEGORY
    WAREHOUSE

Actions
    CREATE
    EDIT
```

### Example 4: Search Panel with Actor Context

```
search form is Task.TASKSEARCH
list is TASKLIST

Context
    ACTOR
    PROJECT
    PRIORITY

Actions
    CREATE
        label is "Create Task"
    COMPLETE
    REASSIGN
```

### Example 5: Search Panel with Form Actions

```
search form is Customer.CUSTOMERSEARCH
list is CUSTOMERLIST

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

### Example 6: Department-Specific Search Panel

```
search form is Employee.EMPLOYEESEARCH
list is EMPLOYEELIST

Context
    DEPARTMENT
    LOCATION
    EMPLOYEESTATUS

Actions
    CREATE
        label is "Add Employee"
    EDIT
    TERMINATE
```

### Example 7: Project Task Search Panel

```
search form is Task.TASKSEARCH
list is TASKLIST

Context
    PROJECT
    ASSIGNEDTO
    TASKSTATUS

Actions
    CREATE
        label is "New Task"
    COMPLETE
    REASSIGN
    DELETE
```

### Example 8: Inventory Search Panel

```
search form is Product.PRODUCTSEARCH
list is PRODUCTLIST

Context
    WAREHOUSE
    CATEGORY
    SUPPLIER

Actions
    CREATE
        label is "Add Product"
    EDIT
    ADJUSTINVENTORY
```

### Example 9: Document Search Panel

```
search form is Document.DOCUMENTSEARCH
list is DOCUMENTLIST

Context
    ACTOR
    DOCUMENTTYPE
    CATEGORY

Actions
    CREATE
        label is "Upload Document"
    EDIT
    DOWNLOAD
    DELETE
```

### Example 10: Support Ticket Search Panel

```
search form is Ticket.TICKETSEARCH
list is TICKETLIST

Context
    ASSIGNEDTO
    PRIORITY
    STATUS
    TEAM

Actions
    CREATE
        label is "Create Ticket"
    ASSIGN
    RESOLVE
    CLOSE
```

### Example 11: Sales Opportunity Search Panel

```
search form is Opportunity.OPPORTUNITYSEARCH
list is OPPORTUNITYLIST

Context
    ACTOR
    SALESPERSON
    STAGE
    REGION

Actions
    CREATE
        label is "New Opportunity"
    EDIT
    CONVERT
    CLOSE
```

### Example 12: Asset Search Panel

```
search form is Asset.ASSETSEARCH
list is ASSETLIST

Context
    LOCATION
    DEPARTMENT
    ASSETTYPE
    STATUS

Actions
    CREATE
        label is "Register Asset"
    EDIT
    TRANSFER
    RETIRE
```

### Example 13: Multi-Context Search Panel

```
search form is Order.ORDERSEARCH
list is ORDERLIST

Context
    COMPANY
    CUSTOMER
    SALESPERSON
    ORDERSTATUS
    FISCALPERIOD

Actions
    CREATE
        label is "New Order"
    EDIT
    APPROVE
    SHIP
    CANCEL
```

### Example 14: Search Panel with Multiple Form Actions

```
search form is Customer.CUSTOMERSEARCH
list is CUSTOMERLIST

Context
    REGION
    CUSTOMERTYPE
    ACCOUNTMANAGER

Actions
    CREATE
        label is "New Customer"
    form is Customer.CUSTOMERDETAIL
        label is "Details"
    form is Customer.CUSTOMERORDERS
        label is "Orders"
    form is Customer.CUSTOMERINVOICES
        label is "Invoices"
    form is Customer.CUSTOMERCONTACTS
        label is "Contacts"
```

### Example 15: Search Panel with State-Based Actions

```
search form is Request.REQUESTSEARCH
list is REQUESTLIST

Context
    ACTOR
    REQUESTTYPE
    PRIORITY

Actions
    CREATE
        label is "New Request"
    DRAFT.SUBMIT
        label is "Submit"
    PENDING.APPROVE
        label is "Approve"
    PENDING.REJECT
        label is "Reject"
    APPROVED.COMPLETE
        label is "Complete"
```

## Use Cases

### Record Search and Management
- Search for records
- View search results
- Create new records
- Manage existing records

### Context-Driven Workflows
- Department-specific views
- User-specific tasks
- Project-based work
- Filtered operations

### Integrated Search and Action
- Search and create
- Search and edit
- Search and process
- Search and navigate

### User Assignment
- Auto-assign to current user
- Department assignment
- Team assignment
- Role-based assignment

### Filtered Views
- Pre-filtered searches
- Context-based filtering
- Role-based filtering
- Permission-based filtering

## Best Practices

### Search Form Design

1. **Form Selection**
   - Use appropriate search form
   - Include relevant search fields
   - Provide range fields
   - Test with users

2. **Search Criteria**
   - Balance simplicity and power
   - Provide common searches
   - Enable advanced options
   - Set sensible defaults

### List Configuration

1. **List Selection**
   - Use appropriate list
   - Show relevant columns
   - Enable sorting
   - Support pagination

2. **Inline Lists**
   - Use for simple cases
   - Keep focused
   - Test with data
   - Consider reusability

### Context Management

1. **Context Fields**
   - Define necessary context
   - Use meaningful fields
   - Validate availability
   - Handle missing context

2. **Context Propagation**
   - Understand auto-propagation
   - Test Create actions
   - Verify field population
   - Document behavior

3. **Actor Context**
   - Use for user assignment
   - Test with various users
   - Validate permissions
   - Audit assignments

### Actions

1. **Action Selection**
   - Provide relevant actions
   - Use clear labels
   - Consider workflow
   - Test thoroughly

2. **Create Actions**
   - Leverage context propagation
   - Set appropriate defaults
   - Validate required fields
   - Test creation flow

3. **Form Actions**
   - Use context fields only
   - Provide clear labels
   - Test navigation
   - Validate forms

### Performance

1. **Optimization**
   - Optimize search queries
   - Use indexes
   - Limit result sets
   - Cache appropriately

2. **Large Datasets**
   - Implement pagination
   - Provide filtering
   - Optimize queries
   - Monitor performance

### User Experience

1. **Search Experience**
   - Fast search execution
   - Clear results display
   - Easy refinement
   - Helpful feedback

2. **Action Accessibility**
   - Clear action buttons
   - Appropriate placement
   - Keyboard shortcuts
   - Touch-friendly

### Testing

1. **Functional Testing**
   - Test search functionality
   - Test result display
   - Test actions
   - Test context propagation

2. **Integration Testing**
   - Test with various contexts
   - Test Create actions
   - Test form actions
   - Verify workflows

## Common Patterns

### Basic Search Pattern

```
search form is <BusinessClass>.<SearchForm>
list is <ListName>

Actions
    CREATE
    EDIT
    DELETE
```

### Context-Driven Pattern

```
search form is <BusinessClass>.<SearchForm>
list is <ListName>

Context
    <ContextField1>
    <ContextField2>

Actions
    CREATE
    <OtherActions>
```

### User Assignment Pattern

```
search form is <BusinessClass>.<SearchForm>
list is <ListName>

Context
    ACTOR
    <OtherContextFields>

Actions
    CREATE
    <OtherActions>
```

### Form Action Pattern

```
search form is <BusinessClass>.<SearchForm>
list is <ListName>

Context
    <ContextFields>

Actions
    CREATE
    form is <ContextField>.<FormName>
        label is "<Label>"
```

## Troubleshooting

### Issue: Search Not Working

**Possible Causes:**
- Search form not found
- Business class incorrect
- Form definition errors
- Query issues

**Solutions:**
- Verify search form exists
- Check business class name
- Validate form definition
- Review query logic

### Issue: Results Not Displaying

**Possible Causes:**
- List not found
- List definition errors
- No matching records
- Context filtering too restrictive

**Solutions:**
- Verify list exists
- Check list definition
- Test with known data
- Review context filters

### Issue: Context Not Propagating

**Possible Causes:**
- Context fields not defined
- Field names don't match
- Create action not configured
- Business class field missing

**Solutions:**
- Define context fields
- Match field names exactly
- Configure Create action
- Add field to business class

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

### Issue: Form Actions Not Working

**Possible Causes:**
- RelatedLink not context field
- Form not found
- Navigation errors
- Permission issues

**Solutions:**
- Use context field only
- Verify form exists
- Test navigation
- Check permissions

## Related Concepts

- [Search Forms](search-forms.md) - Search form definitions
- [Lists](lists.md) - List definitions
- [Panel Definitions](panel-definitions.md) - Panel types
- [List Panels](list-panels.md) - List panel definitions
- [Pages](pages.md) - Page definitions
- [Composite Forms](composite-forms.md) - Multi-panel forms
- [Actions](actions.md) - Action definitions
- [Business Class Definition](business-class-definition.md) - Business class structure

## Summary

Search Panels provide integrated search and list display within panels on Pages and Composite Forms. By combining a search form with a results list, Search Panels enable efficient record searching and management. With automatic context propagation to Create actions, context-based filtering, and flexible action integration, Search Panels streamline common workflows and improve user productivity.

**Key Features:**
- Integrated search and results
- Search form integration
- Results list display
- Context management
- Automatic context propagation
- Create action auto-population
- Actor context support
- Action integration
- Form actions
- Inline list support

**Context Propagation:**
- Context fields auto-populate Create actions
- Actor context assigns to current user
- Simplifies data entry
- Ensures consistency
- Reduces errors

**Best For:**
- Record search and management
- Context-driven workflows
- Integrated search and action
- User assignment
- Filtered views
- Department-specific operations
- Project-based work
- Task management

**Advantages:**
- Integrated interface
- Automatic context handling
- Simplified workflows
- Reduced data entry
- Consistent assignment
- Flexible actions
- Easy configuration
- User-friendly

**Important Notes:**
- Context fields automatically propagate to Create actions
- Actor context assigns to current user
- Form actions can only use context fields for RelatedLink
- Test context propagation thoroughly
- Validate field name matching
- Consider user experience
- Monitor performance with large datasets
