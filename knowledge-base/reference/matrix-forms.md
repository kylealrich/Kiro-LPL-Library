# Matrix Forms

## Overview

Matrix Forms provide a grid-based interface for displaying and editing data in a tabular format with rows and columns. They are particularly useful for financial data, time-series information, and any scenario where data needs to be viewed and edited in a matrix layout. Matrix Forms support period filtering, row searching, and detailed cell editing.

## Basic Syntax

```
<MatrixForm> is a MatrixForm
    [title is <Message>]
    [display negative amounts using parens]
    [display amounts in (thousands | millions)]
    [show in title]
    [row search field is <FullFieldName>]
    [implements PeriodFilter]
        ( date is <FullFieldName>
        | (monthly | weekly | daily) period is <FullFieldName>
          year is <FullFieldName>)
        [show (day | week | month | quarter | year)]
        [current (date | period) is <RelatedValue>]
        current year is <RelatedValue>
    [context form is (<FormName> | inline)]
        [<FormDefinition>]
    Column
        Display Fields
            ( <FormField>…
            | <FormButton>…
            | <CheckControl>…
            | <ManualRepresentation>…)
    Cell
        [overview navigation is <NavigationName>]
        [label is <Message>]
        Display Fields
            ( <FormField>…
              [(valid | visible) when <SimpleCondition>]
            | <FormButton>…
            | <CheckControl>…
            | <ManualRepresentation>…)
        Detail Fields
            ( <FormField>…
            | <FormButton>…
            | <CheckControl>…
            | <ManualRepresentation>…)
```

## Definition

```
MatrixFormDefinition ::=
    <MatrixForm> is a MatrixForm
        [title is <Message>]
        [display negative amounts using parens]
        [display amounts in (thousands | millions)]
        [show in title]
        [row search field is <FullFieldName>]
        [implements PeriodFilter]
            ( date is <FullFieldName>
            | (monthly | weekly | daily) period is <FullFieldName>
              year is <FullFieldName>)
            [show (day | week | month | quarter | year)]
            [current (date | period) is <RelatedValue>]
            current year is <RelatedValue>
        context form is (<FormName> | inline)
            [<FormDefinition>]
        Column
            Display Fields
                ( <FormField>…
                | <FormButton>…
                | <CheckControl>…
                | <ManualRepresentation>…)
        Cell
            [overview navigation is <NavigationName>]
            [label is <Message>]
            Display Fields
                ( <FormField>…
                  [(valid | visible) when <SimpleCondition>]
                | <FormButton>…
                | <CheckControl>…
                | <ManualRepresentation>…)
            Detail Fields
                ( <FormField>…
                | <FormButton>…
                | <CheckControl>…
                | <ManualRepresentation>…)
```

## Matrix Form Attributes

### Title

```
title is <Message>
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `title is <Message>` | Matrix form title | MatrixName |

### Amount Display

```
display negative amounts using parens
display amounts in (thousands | millions)
show in title
```

| Attribute | Description |
|-----------|-------------|
| `display negative amounts using parens` | Shows negative amounts as (123.45) instead of -123.45 |
| `display amounts in thousands` | Divides amounts by 1,000 |
| `display amounts in millions` | Divides amounts by 1,000,000 |
| `show in title` | Shows the scaling in the title |

**Use Cases:**
- Financial reports
- Budget displays
- Large number presentations
- Standardized reporting

### Row Search Field

```
row search field is <FullFieldName>
```

Enables searching/filtering of rows. FullFieldName is from the context of the row's business class.

**Example:**
```
row search field is AccountNumber
row search field is CustomerName
```

## Period Filter

```
implements PeriodFilter
    ( date is <FullFieldName>
    | (monthly | weekly | daily) period is <FullFieldName>
      year is <FullFieldName>)
    [show (day | week | month | quarter | year)]
    [current (date | period) is <RelatedValue>]
    current year is <RelatedValue>
```

Enables period-based filtering for time-series data.

### Date-Based Filter

```
date is <FullFieldName>
```

FullFieldName is from the context of the column's business class. Uses a date field for filtering.

**Example:**
```
implements PeriodFilter
    date is TransactionDate
    show month
    current date is current date
    current year is current year
```

### Period-Based Filter

```
(monthly | weekly | daily) period is <FullFieldName>
year is <FullFieldName>
```

Uses period and year fields for filtering.

**Options:**
- **monthly period**: Monthly periods
- **weekly period**: Weekly periods
- **daily period**: Daily periods

**Example:**
```
implements PeriodFilter
    monthly period is FiscalPeriod
    year is FiscalYear
    show month
    current period is current period
    current year is current year
```

### Period Display

```
show (day | week | month | quarter | year)
```

Defaults depending on filter type. Controls the granularity of period display.

### Current Period/Date

```
current (date | period) is <RelatedValue>
current year is <RelatedValue>
```

RelatedValue is from the context of this business class. Sets the default current period/date and year.

## Context Form

```
context form is (<FormName> | inline)
    [<FormDefinition>]
```

Provides context fields above the matrix. FormDefinition valid on inline Form only.

**Use Cases:**
- Filter criteria
- Common parameters
- Context information
- Selection controls

## Column Definition

```
Column
    Display Fields
        ( <FormField>…
        | <FormButton>…
        | <CheckControl>…
        | <ManualRepresentation>…)
```

Defines what appears in the column headers.

**Components:**
- Form fields
- Form buttons
- Check controls
- Manual representations

**Purpose:**
- Column labels
- Column-level data
- Column actions
- Column formatting

## Cell Definition

```
Cell
    [overview navigation is <NavigationName>]
    [label is <Message>]
    Display Fields
        ( <FormField>…
          [(valid | visible) when <SimpleCondition>]
        | <FormButton>…
        | <CheckControl>…
        | <ManualRepresentation>…)
    Detail Fields
        ( <FormField>…
        | <FormButton>…
        | <CheckControl>…
        | <ManualRepresentation>…)
```

### Cell Attributes

| Attribute | Description |
|-----------|-------------|
| `overview navigation is <NavigationName>` | Navigation target for cell overview |
| `label is <Message>` | Cell label |

### Display Fields

Fields shown in the cell in the matrix view.

**Features:**
- Conditional visibility
- Multiple field types
- Compact display
- Quick editing

### Detail Fields

Fields shown when the cell is expanded or opened for detailed editing.

**Features:**
- Full field set
- Detailed editing
- Additional information
- Extended functionality

## Examples

### Example 1: Simple Financial Matrix

```
BUDGETMATRIX is a MatrixForm
    title is "Budget Matrix"
    display negative amounts using parens
    display amounts in thousands
    show in title
    
    context form is inline
        Layout
            two column
                DEPARTMENT
                FISCALYEAR
    
    Column
        Display Fields
            MONTH
    
    Cell
        Display Fields
            BUDGETAMOUNT
                display 2 decimals
        Detail Fields
            BUDGETAMOUNT
            ACTUALAMOUNT
            VARIANCE
            COMMENTS
```

### Example 2: Matrix with Period Filter

```
SALESMATRIX is a MatrixForm
    title is "Sales by Period"
    implements PeriodFilter
        monthly period is FiscalPeriod
        year is FiscalYear
        show month
        current period is current period
        current year is current year
    row search field is ProductName
    
    context form is SALESCONTEXT
    
    Column
        Display Fields
            PERIODNAME
    
    Cell
        Display Fields
            SALESAMOUNT
                display 2 decimals
                currency symbol is "$"
        Detail Fields
            SALESAMOUNT
            QUANTITY
            AVERAGEPRICE
            SALESPERSON
```

### Example 3: Account Balance Matrix

```
ACCOUNTBALANCE is a MatrixForm
    title is "Account Balances"
    display negative amounts using parens
    display amounts in millions
    show in title
    implements PeriodFilter
        date is BalanceDate
        show month
        current date is current date
        current year is current year
    row search field is AccountNumber
    
    context form is inline
        Layout
            single column
                COMPANY
                ACCOUNTTYPE
    
    Column
        Display Fields
            MONTHNAME
    
    Cell
        label is "Balance"
        overview navigation is ACCOUNTDETAIL
        Display Fields
            BALANCE
                display 2 decimals
                display negative amount using parens
        Detail Fields
            BALANCE
            BEGINNINGBALANCE
            ENDINGBALANCE
            NETCHANGE
            TRANSACTIONCOUNT
```

### Example 4: Resource Allocation Matrix

```
RESOURCEMATRIX is a MatrixForm
    title is "Resource Allocation"
    implements PeriodFilter
        weekly period is WeekNumber
        year is Year
        show week
        current period is current week
        current year is current year
    row search field is EmployeeName
    
    context form is inline
        Layout
            two column
                DEPARTMENT
                PROJECT
    
    Column
        Display Fields
            WEEKOF
                label is "Week Of"
    
    Cell
        Display Fields
            HOURSALLOCATED
                label is "Hours"
                display 1 decimals
        Detail Fields
            HOURSALLOCATED
            TASKASSIGNMENT
            NOTES
```

### Example 5: Inventory Matrix

```
INVENTORYMATRIX is a MatrixForm
    title is "Inventory Levels"
    implements PeriodFilter
        date is InventoryDate
        show day
        current date is current date
        current year is current year
    row search field is ProductCode
    
    context form is inline
        Layout
            two column
                WAREHOUSE
                CATEGORY
    
    Column
        Display Fields
            DATE
                display as gregorian date
    
    Cell
        Display Fields
            QUANTITYONHAND
                label is "Qty"
            REORDERLEVEL
                label is "Reorder"
                visible when QuantityOnHand <= ReorderLevel
        Detail Fields
            QUANTITYONHAND
            QUANTITYRESERVED
            QUANTITYAVAILABLE
            REORDERLEVEL
            REORDERQUANTITY
            LASTUPDATED
```

### Example 6: Project Timeline Matrix

```
PROJECTTIMELINE is a MatrixForm
    title is "Project Timeline"
    implements PeriodFilter
        monthly period is Month
        year is Year
        show month
        current period is current period
        current year is current year
    row search field is ProjectName
    
    context form is inline
        Layout
            single column
                PORTFOLIO
                PROJECTMANAGER
    
    Column
        Display Fields
            MONTHNAME
    
    Cell
        Display Fields
            STATUS
                display as tag
                color of Green when Status = "On Track"
                color of Yellow when Status = "At Risk"
                color of Red when Status = "Delayed"
            PERCENTCOMPLETE
                display as progress bar
        Detail Fields
            STATUS
            PERCENTCOMPLETE
            MILESTONES
            ISSUES
            NOTES
```

### Example 7: Expense Matrix

```
EXPENSEMATRIX is a MatrixForm
    title is "Expense Tracking"
    display negative amounts using parens
    implements PeriodFilter
        monthly period is ExpensePeriod
        year is ExpenseYear
        show month
        current period is current period
        current year is current year
    row search field is ExpenseCategory
    
    context form is inline
        Layout
            two column
                DEPARTMENT
                COSTCENTER
    
    Column
        Display Fields
            PERIODNAME
    
    Cell
        Display Fields
            EXPENSEAMOUNT
                display 2 decimals
                currency symbol is "$"
                display negative amount using parens
        Detail Fields
            EXPENSEAMOUNT
            BUDGETAMOUNT
            VARIANCE
            VARIANCEPERCENT
            APPROVEDBY
            COMMENTS
```

### Example 8: Capacity Planning Matrix

```
CAPACITYMATRIX is a MatrixForm
    title is "Capacity Planning"
    implements PeriodFilter
        weekly period is WeekNumber
        year is Year
        show week
        current period is current week
        current year is current year
    row search field is ResourceName
    
    context form is inline
        Layout
            two column
                DEPARTMENT
                SKILLSET
    
    Column
        Display Fields
            WEEKOF
    
    Cell
        Display Fields
            UTILIZATION
                display as meter chart
                meter range is 100
                Error alert range is from 90 to 100
                Warning alert range is from 75 to 89
        Detail Fields
            UTILIZATION
            AVAILABLEHOURS
            ALLOCATEDHOURS
            ASSIGNMENTS
```

### Example 9: Production Matrix

```
PRODUCTIONMATRIX is a MatrixForm
    title is "Production Schedule"
    implements PeriodFilter
        daily period is ProductionDay
        year is Year
        show day
        current period is current date
        current year is current year
    row search field is ProductLine
    
    context form is inline
        Layout
            two column
                FACILITY
                SHIFT
    
    Column
        Display Fields
            DATE
                display as gregorian date
    
    Cell
        Display Fields
            UNITSPRODUCED
                label is "Units"
            TARGETUNITS
                label is "Target"
                visible when UnitsProduced < TargetUnits
        Detail Fields
            UNITSPRODUCED
            TARGETUNITS
            VARIANCE
            DOWNTIME
            QUALITYISSUES
            NOTES
```

### Example 10: Attendance Matrix

```
ATTENDANCEMATRIX is a MatrixForm
    title is "Employee Attendance"
    implements PeriodFilter
        date is AttendanceDate
        show day
        current date is current date
        current year is current year
    row search field is EmployeeName
    
    context form is inline
        Layout
            two column
                DEPARTMENT
                LOCATION
    
    Column
        Display Fields
            DATE
                display as gregorian date
            DAYNAME
                label is "Day"
    
    Cell
        Display Fields
            ATTENDANCESTATUS
                display as tag
                color of Green when AttendanceStatus = "Present"
                color of Yellow when AttendanceStatus = "Late"
                color of Red when AttendanceStatus = "Absent"
        Detail Fields
            ATTENDANCESTATUS
            CHECKINTIME
            CHECKOUTTIME
            TOTALHOURS
            NOTES
```

## Use Cases

### Financial Reporting
- Budget vs. actual
- Account balances
- Expense tracking
- Revenue analysis
- Variance reporting

### Time-Series Data
- Sales by period
- Production schedules
- Inventory levels
- Resource allocation
- Capacity planning

### Project Management
- Project timelines
- Resource allocation
- Task assignments
- Progress tracking
- Milestone tracking

### Human Resources
- Attendance tracking
- Time sheets
- Leave management
- Performance metrics
- Headcount planning

### Operations
- Production schedules
- Capacity utilization
- Quality metrics
- Downtime tracking
- Efficiency analysis

## Best Practices

### Matrix Design

1. **Row and Column Selection**
   - Choose meaningful dimensions
   - Limit row count for performance
   - Use appropriate period granularity
   - Consider user workflow

2. **Cell Content**
   - Keep display fields minimal
   - Use detail fields for complexity
   - Provide clear labels
   - Use appropriate formatting

3. **Context Form**
   - Provide necessary filters
   - Keep concise
   - Use clear labels
   - Enable quick filtering

### Period Filtering

1. **Period Selection**
   - Choose appropriate granularity
   - Match business needs
   - Consider data volume
   - Test performance

2. **Default Periods**
   - Set sensible defaults
   - Use current period
   - Consider fiscal calendar
   - Test with users

### Display Options

1. **Amount Formatting**
   - Use thousands/millions for large numbers
   - Show scaling in title
   - Use parens for negatives
   - Be consistent

2. **Visual Indicators**
   - Use colors meaningfully
   - Provide status indicators
   - Use alerts appropriately
   - Consider accessibility

### Search and Navigation

1. **Row Search**
   - Choose searchable field
   - Enable quick filtering
   - Provide clear feedback
   - Test with large datasets

2. **Cell Navigation**
   - Provide overview navigation
   - Enable drill-down
   - Maintain context
   - Test navigation paths

### Performance

1. **Optimization**
   - Limit initial data load
   - Use period filtering
   - Implement row search
   - Cache appropriately

2. **Data Volume**
   - Test with realistic data
   - Monitor performance
   - Optimize queries
   - Consider pagination

### Usability

1. **User Experience**
   - Intuitive navigation
   - Clear labels
   - Helpful tooltips
   - Responsive design

2. **Editing**
   - Enable inline editing
   - Validate immediately
   - Provide feedback
   - Save appropriately

## Common Patterns

### Financial Matrix Pattern

```
<EntityName>MATRIX is a MatrixForm
    title is "<Entity> Matrix"
    display negative amounts using parens
    display amounts in thousands
    show in title
    implements PeriodFilter
        monthly period is FiscalPeriod
        year is FiscalYear
        show month
        current period is current period
        current year is current year
    
    context form is inline
        Layout
            <filter fields>
    
    Column
        Display Fields
            PERIODNAME
    
    Cell
        Display Fields
            AMOUNT
                display 2 decimals
        Detail Fields
            <detail fields>
```

### Time-Series Pattern

```
<EntityName>MATRIX is a MatrixForm
    title is "<Entity> by Period"
    implements PeriodFilter
        date is <DateField>
        show <granularity>
        current date is current date
        current year is current year
    row search field is <SearchField>
    
    Column
        Display Fields
            <period display>
    
    Cell
        Display Fields
            <key metrics>
        Detail Fields
            <detailed metrics>
```

### Resource Allocation Pattern

```
<ResourceName>MATRIX is a MatrixForm
    title is "<Resource> Allocation"
    implements PeriodFilter
        weekly period is WeekNumber
        year is Year
        show week
        current period is current week
        current year is current year
    row search field is <ResourceField>
    
    Column
        Display Fields
            WEEKOF
    
    Cell
        Display Fields
            ALLOCATION
        Detail Fields
            <allocation details>
```

## Integration with Other Components

### Matrix Forms and Lists

Matrix Forms can be accessed from lists for detailed matrix views.

```
ACCOUNTLIST is a List
    Display Fields
        ACCOUNT
        ACCOUNTNAME
    
    Detail Sections
        BALANCEMATRIX
            title is "Balance Matrix"
            form is ACCOUNTBALANCEMATRIX
```

### Matrix Forms and Actions

Actions can be called from matrix cells.

```
Cell
    Display Fields
        AMOUNT
    Detail Fields
        AMOUNT
        button of "Adjust"
            action is ADJUST
```

## Troubleshooting

### Issue: Matrix Not Loading

**Possible Causes:**
- Too much data
- Period filter not working
- Context form errors
- Query performance

**Solutions:**
- Implement period filter
- Add row search
- Fix context form
- Optimize queries

### Issue: Cells Not Editable

**Possible Causes:**
- Fields read-only
- Permissions
- Field rules
- Display only mode

**Solutions:**
- Check field attributes
- Review permissions
- Check field rules
- Enable editing

### Issue: Period Filter Not Working

**Possible Causes:**
- Incorrect field references
- Date/period mismatch
- Current period not set
- Filter configuration errors

**Solutions:**
- Verify field names
- Check date/period types
- Set current period
- Review filter syntax

### Issue: Performance Problems

**Possible Causes:**
- Too many rows
- Too many columns
- Complex calculations
- Missing indexes

**Solutions:**
- Limit row count
- Reduce period range
- Optimize calculations
- Add database indexes

### Issue: Display Fields Not Showing

**Possible Causes:**
- Conditional visibility false
- Field errors
- Data not available
- Layout issues

**Solutions:**
- Check conditions
- Validate field syntax
- Verify data exists
- Review layout

## Related Concepts

- [Forms](forms.md) - Standard form definitions
- [Lists](lists.md) - List definitions
- [Form Base Definitions](form-base-definitions.md) - Form components
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Field Definition](field-definition.md) - Field structure
- [Navigations](navigations.md) - Navigation definitions
- [UI Definition](ui-definition.md) - UI components

## Summary

Matrix Forms provide powerful grid-based interfaces for displaying and editing tabular data. With support for period filtering, row searching, context forms, and detailed cell editing, Matrix Forms are ideal for financial reporting, time-series data, resource allocation, and any scenario requiring a matrix layout. Proper configuration of columns, cells, and period filters enables efficient data entry and analysis in a compact, organized format.

**Key Features:**
- Grid-based layout
- Period filtering (date, monthly, weekly, daily)
- Row searching
- Context forms
- Column definitions
- Cell display and detail fields
- Amount formatting
- Conditional visibility
- Overview navigation
- Inline editing

**Best For:**
- Financial reporting
- Budget tracking
- Time-series data
- Resource allocation
- Project timelines
- Inventory management
- Capacity planning
- Attendance tracking
- Production schedules
- Expense tracking

**Advantages:**
- Compact data display
- Efficient editing
- Period-based filtering
- Quick navigation
- Clear visualization
- Flexible formatting
- Detailed drill-down
