# Search Forms

## Overview

Search Forms provide a specialized interface for defining search criteria to filter lists and query data. They offer a focused layout for search fields, enabling users to quickly find specific records by entering search parameters. Search Forms are typically used in conjunction with lists to provide filtering capabilities.

## Basic Syntax

```
<FormName> is a SearchForm
    [title is <Message>]
    Layout
        <FormLayout>
        …
```

## Definition

```
SearchFormDefinition ::=
    <FormName> is a SearchForm
        [title is <Message>]
        Layout
            <FormLayout>
            …
```

## Search Form Attributes

### Title

```
title is <Message>
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `title is <Message>` | Search form title | FormName |

The title can include static text or dynamic expressions.

## Layout

```
Layout
    <FormLayout>
    …
```

Defines the visual arrangement of search fields and controls.

**Layout Options:**
- All standard form layout directives
- Single column, multi-column layouts
- Sections for grouping
- Form fields, buttons, controls
- Conditional visibility

## Purpose

Search Forms are used to:
- Define search criteria
- Filter list results
- Provide advanced search capabilities
- Enable quick record lookup
- Support complex queries

## Characteristics

### Focused Interface
- Contains only search-relevant fields
- Simplified layout
- Clear field labels
- Logical field grouping

### Integration with Lists
- Typically used with lists
- Filters list results
- Can be inline or separate
- Supports dynamic filtering

### User Experience
- Quick access to search
- Clear search criteria
- Easy to use
- Responsive design

## Examples

### Example 1: Simple Customer Search

```
CUSTOMERSEARCH is a SearchForm
    title is "Search Customers"
    
    Layout
        two column
            CUSTOMERNAME
                label is "Customer Name"
            CITY
                label is "City"
            STATE
                label is "State"
            CUSTOMERTYPE
                label is "Customer Type"
```

### Example 2: Order Search with Date Range

```
ORDERSEARCH is a SearchForm
    title is "Search Orders"
    
    Layout
        two column
            ORDERNUMBER
                label is "Order Number"
            CUSTOMER
                label is "Customer"
            ORDERDATEFROM
                label is "Order Date From"
            ORDERDATETO
                label is "Order Date To"
            STATUS
                label is "Status"
            TOTALFROM
                label is "Total From"
            TOTALTO
                label is "Total To"
```

### Example 3: Product Search with Sections

```
PRODUCTSEARCH is a SearchForm
    title is "Search Products"
    
    Layout
        section of BASICCRITERIA
            label is "Basic Criteria"
            two column
                PRODUCTCODE
                    label is "Product Code"
                PRODUCTNAME
                    label is "Product Name"
                CATEGORY
                    label is "Category"
                ACTIVE
                    label is "Active Only"
                    display as switch
        
        section of PRICECRITERIA
            label is "Price Criteria"
            two column
                PRICEFROM
                    label is "Price From"
                PRICETO
                    label is "Price To"
        
        section of INVENTORYCRITERIA
            label is "Inventory Criteria"
            two column
                QUANTITYFROM
                    label is "Quantity From"
                QUANTITYTO
                    label is "Quantity To"
                WAREHOUSE
                    label is "Warehouse"
```

### Example 4: Employee Search

```
EMPLOYEESEARCH is a SearchForm
    title is "Search Employees"
    
    Layout
        two column
            EMPLOYEENAME
                label is "Employee Name"
            DEPARTMENT
                label is "Department"
            TITLE
                label is "Title"
            HIREDATEFROM
                label is "Hire Date From"
            HIREDATETO
                label is "Hire Date To"
            ACTIVE
                label is "Active Only"
                display as switch
                initial value is true
```

### Example 5: Invoice Search with Advanced Criteria

```
INVOICESEARCH is a SearchForm
    title is "Search Invoices"
    
    Layout
        section of BASICINFO
            label is "Basic Information"
            two column
                INVOICENUMBER
                    label is "Invoice Number"
                CUSTOMER
                    label is "Customer"
                INVOICEDATEFROM
                    label is "Invoice Date From"
                INVOICEDATETO
                    label is "Invoice Date To"
        
        section of AMOUNTS
            label is "Amount Criteria"
            two column
                AMOUNTFROM
                    label is "Amount From"
                AMOUNTTO
                    label is "Amount To"
                BALANCEFROM
                    label is "Balance From"
                BALANCETO
                    label is "Balance To"
        
        section of STATUS
            label is "Status"
            single column
                INVOICESTATUS
                    label is "Invoice Status"
                PAYMENTSTATUS
                    label is "Payment Status"
                OVERDUE
                    label is "Overdue Only"
                    display as switch
```

### Example 6: Transaction Search

```
TRANSACTIONSEARCH is a SearchForm
    title is "Search Transactions"
    
    Layout
        two column
            TRANSACTIONID
                label is "Transaction ID"
            ACCOUNT
                label is "Account"
            TRANSACTIONDATEFROM
                label is "Date From"
            TRANSACTIONDATETO
                label is "Date To"
            TRANSACTIONTYPE
                label is "Transaction Type"
            AMOUNTFROM
                label is "Amount From"
            AMOUNTTO
                label is "Amount To"
            DESCRIPTION
                label is "Description"
                use text area of 2 lines
```

### Example 7: Asset Search

```
ASSETSEARCH is a SearchForm
    title is "Search Assets"
    
    Layout
        section of IDENTIFICATION
            label is "Identification"
            two column
                ASSETNUMBER
                    label is "Asset Number"
                ASSETNAME
                    label is "Asset Name"
                SERIALNUMBER
                    label is "Serial Number"
        
        section of CLASSIFICATION
            label is "Classification"
            two column
                ASSETTYPE
                    label is "Asset Type"
                CATEGORY
                    label is "Category"
                LOCATION
                    label is "Location"
                DEPARTMENT
                    label is "Department"
        
        section of DATES
            label is "Date Criteria"
            two column
                PURCHASEDATEFROM
                    label is "Purchase Date From"
                PURCHASEDATETO
                    label is "Purchase Date To"
                STATUS
                    label is "Status"
```

### Example 8: Project Search

```
PROJECTSEARCH is a SearchForm
    title is "Search Projects"
    
    Layout
        two column
            PROJECTNAME
                label is "Project Name"
            PROJECTMANAGER
                label is "Project Manager"
            CLIENT
                label is "Client"
            PROJECTSTATUS
                label is "Status"
            STARTDATEFROM
                label is "Start Date From"
            STARTDATETO
                label is "Start Date To"
            ENDDATEFROM
                label is "End Date From"
            ENDDATETO
                label is "End Date To"
            BUDGETFROM
                label is "Budget From"
            BUDGETTO
                label is "Budget To"
```

### Example 9: Document Search

```
DOCUMENTSEARCH is a SearchForm
    title is "Search Documents"
    
    Layout
        section of BASICINFO
            label is "Basic Information"
            two column
                DOCUMENTTITLE
                    label is "Document Title"
                DOCUMENTTYPE
                    label is "Document Type"
                AUTHOR
                    label is "Author"
                KEYWORDS
                    label is "Keywords"
        
        section of DATES
            label is "Date Criteria"
            two column
                CREATEDDATEFROM
                    label is "Created From"
                CREATEDDATETO
                    label is "Created To"
                MODIFIEDDATEFROM
                    label is "Modified From"
                MODIFIEDDATETO
                    label is "Modified To"
        
        section of CLASSIFICATION
            label is "Classification"
            single column
                CATEGORY
                    label is "Category"
                TAGS
                    label is "Tags"
                    multi-select field is TAGS
                STATUS
                    label is "Status"
```

### Example 10: Support Ticket Search

```
TICKETSEARCH is a SearchForm
    title is "Search Support Tickets"
    
    Layout
        section of TICKETINFO
            label is "Ticket Information"
            two column
                TICKETNUMBER
                    label is "Ticket Number"
                CUSTOMER
                    label is "Customer"
                SUBJECT
                    label is "Subject"
                DESCRIPTION
                    label is "Description"
                    use text area of 2 lines
        
        section of ASSIGNMENT
            label is "Assignment"
            two column
                ASSIGNEDTO
                    label is "Assigned To"
                TEAM
                    label is "Team"
                PRIORITY
                    label is "Priority"
                STATUS
                    label is "Status"
        
        section of DATES
            label is "Date Criteria"
            two column
                CREATEDDATEFROM
                    label is "Created From"
                CREATEDDATETO
                    label is "Created To"
                DUEDATEFROM
                    label is "Due From"
                DUEDATETO
                    label is "Due To"
                RESOLVEDFROM
                    label is "Resolved From"
                RESOLVEDTO
                    label is "Resolved To"
```

## Integration with Lists

Search Forms are typically used with lists to provide filtering capabilities.

### Inline Search Form

```
CUSTOMERLIST is a List
    title is "Customer List"
    search form is inline
    
    Layout
        two column
            CUSTOMERNAME
            CITY
            STATE
    
    Display Fields
        CUSTOMER
        CUSTOMERNAME
        CITY
        STATE
        BALANCE
```

### Named Search Form

```
ORDERLIST is a List
    title is "Order List"
    search form is ORDERSEARCH
    
    Display Fields
        ORDER
        CUSTOMER
        ORDERDATE
        STATUS
        TOTAL

ORDERSEARCH is a SearchForm
    title is "Search Orders"
    
    Layout
        two column
            ORDERNUMBER
            CUSTOMER
            ORDERDATEFROM
            ORDERDATETO
            STATUS
```

### Search Form Display Options

```
PRODUCTLIST is a List
    title is "Product List"
    search form is PRODUCTSEARCH
    initially display search
    show search form on left
    
    Display Fields
        PRODUCT
        PRODUCTNAME
        CATEGORY
        PRICE
        QUANTITYONHAND
```

## Use Cases

### Record Lookup
- Find specific records quickly
- Search by key fields
- Partial matching
- Wildcard searches

### Advanced Filtering
- Multiple criteria
- Date ranges
- Amount ranges
- Complex conditions

### Data Analysis
- Filter for reporting
- Subset selection
- Trend analysis
- Comparative queries

### User Workflows
- Quick access to data
- Streamlined processes
- Efficient navigation
- Reduced clicks

## Best Practices

### Form Design

1. **Field Selection**
   - Include commonly searched fields
   - Provide range fields for dates/amounts
   - Use appropriate field types
   - Consider user needs

2. **Layout Organization**
   - Group related fields
   - Use logical field order
   - Balance density and whitespace
   - Consider mobile devices

3. **Field Labels**
   - Use clear, descriptive labels
   - Be consistent
   - Avoid jargon
   - Keep concise

4. **Default Values**
   - Set sensible defaults
   - Consider common searches
   - Use current date when appropriate
   - Test with users

### Search Criteria

1. **Range Fields**
   - Provide From/To fields for dates
   - Provide From/To fields for amounts
   - Validate ranges
   - Handle open-ended ranges

2. **Boolean Filters**
   - Use switches for yes/no
   - Provide "Active Only" options
   - Consider tri-state (yes/no/all)
   - Set appropriate defaults

3. **Multi-Select**
   - Use for categories
   - Enable multiple selections
   - Provide clear display
   - Test with many options

### User Experience

1. **Simplicity**
   - Start with basic search
   - Provide advanced options
   - Don't overwhelm users
   - Progressive disclosure

2. **Performance**
   - Optimize queries
   - Use indexes
   - Limit result sets
   - Provide feedback

3. **Feedback**
   - Show search criteria
   - Display result count
   - Indicate no results
   - Provide clear messages

4. **Persistence**
   - Remember search criteria
   - Allow saving searches
   - Provide recent searches
   - Enable quick re-search

### Validation

1. **Field Validation**
   - Validate data types
   - Check date ranges
   - Verify amount ranges
   - Provide clear errors

2. **Logical Validation**
   - Ensure From <= To
   - Check required combinations
   - Validate dependencies
   - Test edge cases

### Accessibility

1. **Keyboard Navigation**
   - Tab order logical
   - Enter to search
   - Escape to clear
   - Shortcuts available

2. **Screen Readers**
   - Proper labels
   - Field descriptions
   - Error announcements
   - Result feedback

## Common Patterns

### Basic Search Pattern

```
<EntityName>SEARCH is a SearchForm
    title is "Search <Entity>"
    
    Layout
        two column
            <key search fields>
```

### Advanced Search Pattern

```
<EntityName>SEARCH is a SearchForm
    title is "Search <Entity>"
    
    Layout
        section of BASICCRITERIA
            label is "Basic Criteria"
            two column
                <basic fields>
        
        section of ADVANCEDCRITERIA
            label is "Advanced Criteria"
            two column
                <advanced fields>
```

### Date Range Search Pattern

```
<EntityName>SEARCH is a SearchForm
    title is "Search <Entity>"
    
    Layout
        two column
            <entity fields>
            DATEFROM
                label is "Date From"
            DATETO
                label is "Date To"
```

### Amount Range Search Pattern

```
<EntityName>SEARCH is a SearchForm
    title is "Search <Entity>"
    
    Layout
        two column
            <entity fields>
            AMOUNTFROM
                label is "Amount From"
            AMOUNTTO
                label is "Amount To"
```

## Comparison with Other Forms

| Feature | Search Form | Standard Form | Action Form |
|---------|-------------|---------------|-------------|
| Purpose | Search criteria | Data entry/display | Action execution |
| Fields | Search parameters | All fields | Action parameters |
| Buttons | Search/Clear | Save/Cancel | OK/Cancel |
| Validation | Minimal | Full | Parameter-based |
| Integration | With lists | Standalone | With actions |
| Use Case | Filtering | CRUD operations | Action execution |

## Troubleshooting

### Issue: Search Not Working

**Possible Causes:**
- Field mapping incorrect
- Query errors
- Index missing
- Performance issues

**Solutions:**
- Verify field names
- Check query syntax
- Add database indexes
- Optimize queries

### Issue: No Results Found

**Possible Causes:**
- Criteria too restrictive
- Data doesn't exist
- Field values incorrect
- Case sensitivity

**Solutions:**
- Relax criteria
- Verify data exists
- Check field values
- Review case handling

### Issue: Slow Search

**Possible Causes:**
- Missing indexes
- Too much data
- Complex queries
- Inefficient filters

**Solutions:**
- Add indexes
- Limit result sets
- Optimize queries
- Simplify criteria

### Issue: Fields Not Displaying

**Possible Causes:**
- Layout errors
- Field definition errors
- Conditional visibility
- Permission issues

**Solutions:**
- Check layout syntax
- Verify field definitions
- Review conditions
- Check permissions

### Issue: Range Validation Failing

**Possible Causes:**
- From > To
- Invalid dates
- Type mismatches
- Null handling

**Solutions:**
- Validate ranges
- Check date formats
- Verify data types
- Handle nulls

## Related Concepts

- [Lists](lists.md) - List definitions
- [Forms](forms.md) - Standard form definitions
- [Form Base Definitions](form-base-definitions.md) - Form components
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Field Definition](field-definition.md) - Field structure
- [Conditions](conditions.md) - Conditional logic
- [UI Definition](ui-definition.md) - UI components

## Summary

Search Forms provide a focused interface for defining search criteria to filter lists and query data. With support for all standard form layout options, Search Forms enable simple to complex search scenarios including range searches, multi-criteria filtering, and advanced queries. Proper design of Search Forms enhances user productivity by enabling quick, efficient record lookup and data filtering.

**Key Features:**
- Focused search interface
- All form layout options
- Integration with lists
- Range field support
- Section grouping
- Conditional visibility
- Multi-select support
- Simple configuration

**Best For:**
- Record lookup
- List filtering
- Advanced search
- Data queries
- User workflows
- Quick access
- Complex criteria
- Range searches

**Advantages:**
- Simple definition
- Flexible layout
- Easy integration
- User-friendly
- Efficient filtering
- Clear purpose
- Minimal complexity
- Standard patterns

**Design Principles:**
- Keep it simple
- Group logically
- Provide ranges
- Set defaults
- Validate appropriately
- Optimize performance
- Test with users
- Consider accessibility
