# Lists

## Overview

Lists define how collections of business class instances are displayed and interacted with in the user interface. They support various view types including standard lists, cube views, gallery views, tree views, org charts, and dashboards. Lists can include search forms, filters, charts, detail sections, and actions.

## Basic List Definition

```
<ListName> is (a List | a[n] <ListName> List | an AuditList)
    [is primary]
    [is drill target [for <FullFieldName>]]
    …
    [propagate as a drill]
    [title is <Message>]
    [is report [for <WebAppName>[,<WebAppName>]…]
    [category is <Message>]
    [show <#> lines]
    [show result set size]
    [default Alpha filter operator is (contains | starts with | equals)]
    [keyword search field is <RelatedField>]
    [label is <Message>]
    [(always | initially) display]
    [disable translation filtering]
    [(always | initially) display search]
    [search form is (<FormName> | inline)]
    [search field is <FullFieldName>]
        …
        [label is <Message>]
    [use as filter]
    [always display]
    [show search form on left]
    [disable translation filtering]
    [Layout]
        <FormLayout>
        …
    [Required Search Parameters]
        <RelatedField>
        …
        [initial value is <RelatedValue>]
```

## List Types

### Standard List
```
<ListName> is a List
```
Basic list display with rows and columns.

### Named List Type
```
<ListName> is a[n] <ListName> List
```
Creates a list based on another list definition.

### Audit List
```
<ListName> is an AuditList
```
Special list for displaying audit trail information.

## Basic Attributes

| Attribute | Description |
|-----------|-------------|
| `is primary` | Designates this as the primary list for the business class |
| `is drill target [for <FullFieldName>]` | Makes this list a drill target |
| `propagate as a drill` | Propagates drill context |
| `title is <Message>` | List title (defaults to ListName) |
| `is report [for <WebAppName>]` | Designates as a report for specific web apps |
| `category is <Message>` | Report category |
| `show <#> lines` | Number of lines to display |
| `show result set size` | Displays total record count |
| `label is <Message>` | Display label |
| `always display` / `initially display` | Display behavior |
| `disable translation filtering` | Disables translation filtering |

### Filter and Search Attributes

| Attribute | Description |
|-----------|-------------|
| `default Alpha filter operator is (contains \| starts with \| equals)` | Default filter behavior (default: contains) |
| `keyword search field is <RelatedField>` | Field for keyword search |
| `always display search` / `initially display search` | Search form display behavior |
| `search form is (<FormName> \| inline)` | Search form definition |
| `search field is <FullFieldName>` | Individual search fields |
| `use as filter` | Use search as filter |
| `show search form on left` | Position search form on left side |

## List Implementations

### Long Running List
```
implements LongRunningList
```
For lists that may take significant time to load.

### Inline Create
```
implements InlineCreate
    [allow form create]
    [copy mutable fields only]
```
Enables inline record creation. By default copies all persistent fields; `copy mutable fields only` restricts to mutable fields.

### Fixed Size List
```
implements FixedSizeList
    show <#> lines
```
Displays a fixed number of lines (default: 10).

### Representative Image View
```
implements RepresentativeImageView
```
Displays representative images. Business class must have a representative image defined.

### Audit Compare
```
implements AuditCompare
    [show changed values on right]
```
Valid only for AuditList. Enables audit comparison view.

### Gallery View
```
implements GalleryView
    card is <CardViewName>
    [allow horizontal paging]
```
Displays items in a gallery layout using cards. Horizontal paging valid only on MultiList and SplashPage.

### Tree View
```
implements TreeView
    [show parent]
    [node icon is <RelatedField>]
    [max levels is <RelatedValue>]
    [show <RelatedValue> levels]
    [has children when <SimpleCondition>]
    [disable create within [when <SimpleCondition>]]
```
Displays hierarchical data as a tree. Node icon must be a StateField with icon definition.

### Org Chart View
```
implements OrgChartView
    card view is <CardViewName>
    [legend state is <RelatedField>]
    [has children when <SimpleCondition>]
    [disable create within [when <SimpleCondition>]]
```
Displays organizational hierarchy as an org chart.

### Columnar View
```
implements ColumnarView
    [union with <BusinessClass>]
    …
    [show charts only]
```
Displays data in columnar format. Can union with other business classes.

### Dashboard View
```
implements DashBoardView
    [show (3 | 4) columns]
    [use 12 column grid]
    [print as dashboard]
    [compress columns]
```
Valid only when using a CardView without Display Fields and with Detail Sections. Default: 3 columns.

### Horizontal Scrolling
```
implements HorizontalScrolling
    [freeze first [<RelatedValue>] column[s]]
```
Enables horizontal scrolling with optional frozen columns.

### Attachment List
```
implements AttachmentList
    attachment is <FullFieldName>
    [disable download]
```
Displays attachments with optional download restriction.

## Cube View

```
implements CubeView
    [suppress blank rows]
    [suppress current period]
    [period dimension is <DimensionField>]
    [show total node]
        [label is <Message>]
    [show <RelatedValue> levels]
    
    [Context]
        <FieldName>
        …
    
    [Row Dimensions]
        <DimensionField>
        …
        [caption is (representative text | <Message>)]
        [is fixed]
        [Periods]
            <PeriodOperator>
            …
            [label is <Message>]
    
    [Pivot Views]
        <PivotViewName>
        …
        [label is <Message>]
        [is default]
        Row Dimensions
            <DimensionField>
            …
            [caption is (representative text | <Message>)]
            [is fixed]
            [Periods]
                <PeriodOperator>
                …
                [label is <Message>]
    
    [Column Views]
        <ColumnViewName>
        …
        [label is <Message>]
        [is default]
        Display Fields
            <ListDisplayField>
            …
    
    [Period Views]
        <PeriodViewName>
        …
        [label is <Message>]
        ( all periods in (week | month | quarter | year)
        | Periods
            <PeriodOperator>
            …
            [label is <Message>])
```

### Cube View Attributes

- **suppress blank rows**: Hides rows with no data
- **suppress current period**: Excludes current period (default includes it)
- **period dimension is**: Specifies period dimension (default: first period in dimensions)
- **show total node**: Displays total aggregation
- **show levels**: Number of hierarchy levels to display

### Context
Values can come from:
- Context session key
- `actor.agent(BusClass).FieldName` lookup
- Current context before navigation
- Direct entry in Filter or Search Form

### Row Dimensions
Must have either Row Dimensions or Pivot Views defined. Fixed dimensions must start at the top and be contiguous.

### Pivot Views
Alternative dimensional arrangements for the same data.

### Column Views
Different column arrangements for display.

### Period Views
Valid only if the cube has a period dimension. Defines period groupings.

## List Behavior

### Auto Refresh
```
auto refresh using <RelatedField>
    [raise <AlertType> alert]
```
RelatedField must be a TimeStamp field.

### Mouse Over and Links
```
mouse over text is <Message>
link is <UILink>
```

### Selection Behavior
```
is default select
is default TreeView select
show as simple list
```

### Sort and Display Options
```
show in descending order
show in relevance order
show totals only
suppress sub-totals
    [sub-total text is <Message>]
    [grand total text is <Message>]
display negative amounts using parens
display negative amounts in <Color>
display amounts in (thousands | millions)
show in title
disable select in webui
(multiple | single | disable) select
```

Default selection mode is multiple.

### Messages and Icons
```
no data message is <Message>
description is <Message>
icon is <Icon>
<FormButton>
```

### Row and Column Formatting
```
row separator is (horizontal rule | blank line)
column header is <Message>
```

Default: no separator. Column header valid only when using Display Fields.

### Sort Order
```
sort order is (<SetName> | primary)
sort field is <RelatedField>
    …
    [is default [descending]]
    [label is <Message>]
```

- `sort order` valid only on non-ColumnarView
- `sort field` valid only on ColumnarView

### Responsive Design
```
responsive card view is <CardViewName>
```

Valid only if `card view is` is defined.

### Subtotals
```
Subtotal By
    <RelatedField>
    …
    [page break]
```

Default: all fields in sort order except last one. Fields must be in sort order.

### CSV and Print Fields
```
csv fields are <ListName>
print fields are <ListName>
```

Allows different list definitions for export/print.

## PDF Generation and Formatting

```
print form is <FormName>
total form is <FormName>
    [when printing detail sections]
orientation is (portrait | landscape)
font offset is <Number>
show grid lines
suppress header
suppress footer
first page header is <FormName>
page header is <FormName>
page footer is <FormName>
```

- **first page header**: Prints only on first page
- **page header**: Will not print on first page

## Context Field Values

```
Context Field Values
    <FullFieldName> value is <RelatedValue>
    …
```

Sets context field values for the list.

## Display Fields

```
Display Fields
    ( <ListDisplayField>
    | group label is <Message>
      <ListDisplayField>
      …)
    …
```

Defines which fields to display and how to group them.

## Summary Total Fields

```
Summary Total Fields
    <FormField>
    …
```

Fields to display as summary totals.

## Instance Selection

```
Instance Selection
    [include [only] deleted records]
    where <Condition>
```

Filters which instances appear in the list.

## Additional Folder Items

```
Additional Folder Items
    <KeyField>
    …
```

When a record is copied to a folder, related records can also be copied.

## Forms and Actions

### Form Definition
```
[<DisplayType>] form is (<FormName> | inline)
    [when <SimpleCondition>]
    [<FormDefinition>]
```

Form to use on Open. Primary form is used by default. FormDefinition valid on inline Form only.

### Drill List
```
drill list is <DrillListName>
```

### Actions
```
create action is [<StateName>.]<ActionName>
    …
    [invoked.<FullFieldName> = <RelatedValue>]
    …
open action is ([<StateName>.]<ActionName> | restricted)
    …
(restrict | enable) action [<StateName>.]<ActionName>
    …
    [when <Condition>]
restrict action (AuditCreate | AuditUpdate)
    …
```

- By default, list items can always be opened
- Use `restricted` keyword to prevent opening
- `restrict action` not valid on AuditList
- `restrict action AuditCreate/AuditUpdate` valid on AuditList only

### Toolbar and Actions
```
suppress standard toolbar
disable all actions
called out actions only
disable field options
```

Standard toolbar shows by default unless CalledOutActions are defined.

### Called Out Actions
```
Actions
    <CalledOutAction>
    …
```

## Quick Entry Fields

```
Quick Entry Fields
    (<FormField> | <FormButton> | <CheckControl> | <FormText> | <ManualRepresentation>)
    …
```

Fields for quick data entry.

## Detail Fields

```
Detail Fields [(Below | On Side)]
    (<FormField> | <FormButton> | <CheckControl> | <FormText> | <ManualRepresentation>)
    …
    [(valid | visible) when <SimpleCondition>]
```

Default position: Below.

## Detail Sections

```
Detail Sections
    [show single panel tab]
    [print width is <Percent>]
    [(indent | right align | center | left align)]
    ( <DetailSection>
    | <DetailSectionName> is a (DashBoardView | MultiListView)
      [mouse over text is <Message>]
      [title is <Message>]
      [(valid | visible) when <SimpleCondition>]
      [show (3 | 4) columns]
      [use 12 column grid]
      [print as dashboard]
      [compress columns]
      Detail Sections
          <DetailSection>
          …)
    …
```

Default alignment: indent.

## Detail Section Definition

```
DetailSection ::=
    [<DetailSectionName>]
    [mouse over text is <Message>]
    [title is <Message>]
    [implements ManualLoad]
        [message is <Message>]
        [description is <Message>]
        [icon is <Icon>]
        load button text is <Message>
    [(valid | visible) when <SimpleCondition>]
    [(valid | visible) for dimension <DimensionField>]
    [(double width | triple width | quad width)]
    [span (1|2|3|4|5|6|7|8|9|10|11|12) columns]
    [double height]
    [show in column(1|2|3|4|5|6|7|8|9|10|11|12)]
    [show <#> lines]
    (<Form> | <List> | link is <HttpURL> | card view is <CardViewName>)
    [<FormDefinition>]
    [allow nested detail sections]
    [overview navigation is <NavigationName>]
    [label is <Message>]
    [page break]
```

### Detail Section Attributes

- **double/triple/quad width**: Valid only on DashBoardView
- **span columns**: Valid only on DashBoardView with 12 column grid
- **double height**: Valid only on DashBoardView
- **show lines**: Valid only on List in DashBoardView (default: 5 lines)
- **allow nested detail sections**: Valid only on list section
- **page break**: Used only when printing to PDF

## Charts

### Bar Chart
```
<ChartName> is a BarChart
    [title is <Message>]
    [is default]
    [chart (list | elements | periods)]
    [show fixed scale [of <RelatedValue>]]
    [(show | hide) legend]
    [(show | hide) labels]
    [show bars (vertically | horizontally)]
    [show elements(side-by-side | stacked)]
    element name is <Message>
    element value is <RelatedValue>
    …
    [title is <Message>]
```

- Default chart mode: chart list
- Default bars: vertically
- Default elements: side-by-side
- Default legend: show

### Pie Chart
```
<ChartName> is a PieChart
    [title is <Message>]
    [is default]
    [chart (list | elements | periods)]
    [show fixed scale [of <RelatedValue>]]
    [(show | hide) legend]
    [(show | hide) labels]
    [show as donut]
    [center field is <RelatedField>]
    [(label is (<RelatedField> | <Message>) | no label)]
    [<TextStyle>]
    …
    [<FieldStyle>]
    …
    element name is <Message>
    element value is <RelatedValue>
    …
    [title is <Message>]
```

- Default legend: hide
- Default labels: show

### Line Chart
```
<ChartName> is a LineChart
    [title is <Message>]
    [is default]
    [chart (list | elements | periods)]
    [show fixed scale [of <RelatedValue>]]
    [(show | hide) legend]
    [(show | hide) labels]
    [show area]
    element name is <Message>
    element value is <RelatedValue>
    …
    [title is <Message>]
```

### Gap Chart
```
<ChartName> is a GapChart
    [title is <Message>]
    [is default]
    [chart (list | elements | periods)]
    [show fixed scale [of <RelatedValue>]]
    [(show | hide) legend]
    [(show | hide) labels]
    element name is <Message>
    element value is <RelatedValue>
    …
    [title is <Message>]
```

### Calendar Chart
```
<ChartName> is a CalendarChart
    [title is <Message>]
    [is default]
    [show date of <RelatedValue>]
    [show (day | week | month)]
    [legend state is <RelatedField>]
    date [range] is <RelatedValue> [to <RelatedValue>]
    [text is <Message>]
    [mouse over text is <Message>]
    [raise <AlertType> alert when <SimpleCondition>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
```

- RelatedValue must be a Date (default: current date)
- Default view: month
- Date range accepts Date or TimeStamp

### Scatter Chart
```
<ChartName> is a ScatterChart
    [title is <Message>]
    [is default]
    [point size is <RelatedValue>]
    [legend state is <RelatedField>]
    x-axis value is <RelatedValue>
    …
    [title is <Message>]
    [show fixed [integer] scale [of <RelatedValue>]]
    [minimum value is <RelatedValue>]
    y-axis value is <RelatedValue>
    …
    [title is <Message>]
    [show fixed [integer] scale [of <RelatedValue>]]
    [minimum value is <RelatedValue>]
```

### Aggregation Table
```
<ChartName> is an AggregationTable
    [title is <Message>]
    [is default]
    [print form is <FormName>]
    [total form is <FormName>]
        [when printing detail sections]
    [visible when <SimpleCondition>]
    [suppress blank rows]
    [show totals (first | last)]
    [Subtotal By]
        <RelatedField>
        …
        [page break]
    [Dimensions]
        <RelatedField>
        …
        [is default sort order [descending]]
        [(ascending | descending)]
        [label is <Message>]
        [link is <UILink>]
        [column width is <Number>]
        [caption is (representative text | <Message>)]
        [dimension valid when <SimpleCondition>]
    Measures
        ((sum | avg | min | max) <RelatedField> | instance count)
        …
        [is default sort order [descending]]
        [label is <Message>]
        [column width is <Number>]
        [link is <UILink>]
        [link is <AggregationDrillLink>.<ListName>]
        [add filter [using (and | or) ]condition]
            where <Condition>
        [display <Number> decimals]
        [currency symbol is <RelatedValue>]
        [first line and totals only]
        [show percent symbol]
            [first line and totals only]
        [display blank when zero]
        [display negative amount using (minus | parens)]
        [column visible when <SimpleCondition>]
```

#### Aggregation Table Notes

- Default totals position: first
- **dimension valid when**: Allows dimension to be ignored based on context. SimpleCondition must be based on incoming context fields. If indeterminate, dimension is considered invalid.
- **column visible when**: Hides entire column based on context. SimpleCondition must be based on incoming context fields. If indeterminate, column will not display.
- **add filter**: Default conjunction is 'and'

### Quadrant Chart
```
<ChartName> is a QuadrantChart
    [title is <Message>]
    [row title is <Message>]
    [column title is <Message>]
    [is default]
    [show unplaced elements]
        [header is <Message>]
    [implements DragAndDrop]
        action is [<StateName>.]<ActionName>
        [invoked.<FullFieldName> = [drop target.]<RelatedValue>]
        …
    rows are <RelatedLink>
        [header is <Message>]
        [mouse over text is <Message>]
    columns are <RelatedLink>
        [header is <Message>]
        [mouse over text is <Message>]
    quadrants are <RelatedLink>
        [header is <Message>]
        [mouse over text is <Message>]
    quadrant value is <RelatedValue>
    row position is <RelatedValue>
    column position is <RelatedValue>
    [raise <AlertType> alert when <SimpleCondition>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
```

- **show unplaced elements**: Displays list of elements without valid quadrant address
- Field context for headers is target business class of RelatedLink

## List Display Field

```
ListDisplayField ::=
    (<FormField> | <FormButton> | <CheckControl> | <FormText> | <ManualRepresentation>)
    …
    [is default sort order [descending]]
    [sort order is (<SetName> | primary)]
        [is default [descending]]
        [group by <FullFieldName>]
    [Subtotal By]
        <RelatedField>
        …
        [page break]
    [column width is <Number>]
    [total]
    [visible when <SimpleCondition>]
    [column visible when <SimpleCondition>]
    [suppress value retrieval when not visible]
    [link is <AggregationDrillLink>.<ListName>]
    [suppress when in dimension <DimensionField>]
    [disable copy]
```

### List Display Field Attributes

- **is default sort order**: Valid in ColumnarView only
- **group by**: FullFieldName must be a field in SetName
- **Subtotal By**: Default is all fields in sort order except last one; must be field in sort order
- **column visible when**: SimpleCondition must be based on incoming context fields. If indeterminate, column will not display.
- **suppress value retrieval when not visible**: Performance optimization
- **suppress when in dimension**: Valid in CubeView only
- **disable copy**: Valid for InlineCreate only

## Examples

### Simple List

```
CUSTOMERLIST is a List
    title is "Customer List"
    is primary
    show 25 lines
    show result set size
    default Alpha filter operator is starts with
    
    Display Fields
        CUSTOMER
        CUSTOMERNAME
        CITY
        STATE
        BALANCE
            total
    
    sort order is primary
```

### List with Search Form

```
ORDERLIST is a List
    title is "Order Search"
    initially display search
    search form is inline
    
    Layout
        CUSTOMER
        ORDERDATE
        STATUS
    
    Display Fields
        ORDER
        CUSTOMER
        ORDERDATE
        STATUS
        TOTAL
            total
    
    Instance Selection
        where Status != "Cancelled"
```

### Tree View List

```
ACCOUNTHIERARCHY is a List
    title is "Account Hierarchy"
    implements TreeView
    show parent
    max levels is 5
    has children when ParentAccount entered
    
    Display Fields
        ACCOUNT
        ACCOUNTNAME
        ACCOUNTTYPE
        BALANCE
```

### Dashboard View List

```
CUSTOMERDASHBOARD is a List
    title is "Customer Dashboard"
    implements DashBoardView
    show 3 columns
    
    Detail Sections
        CUSTOMERINFO
            title is "Customer Information"
            form is CUSTOMERFORM
        
        RECENTORDERS
            title is "Recent Orders"
            double width
            list is ORDERLIST
            show 10 lines
        
        SALESTREND
            title is "Sales Trend"
            card view is SALESCHART
```

### Cube View List

```
SALESANALYSIS is a List
    title is "Sales Analysis"
    implements CubeView
    suppress blank rows
    period dimension is FISCALPERIOD
    
    Context
        COMPANY
        FISCALYEAR
    
    Row Dimensions
        PRODUCT
            caption is representative text
        REGION
            caption is "Sales Region"
    
    Display Fields
        SALESAMOUNT
            total
        COSTAMOUNT
            total
        MARGIN
            total
```

### List with Charts

```
SALESREPORT is a List
    title is "Sales Report"
    
    Display Fields
        PRODUCT
        SALESAMOUNT
            total
        QUANTITY
            total
    
    Charts
        SALESCHART is a BarChart
            title is "Sales by Product"
            is default
            show bars vertically
            element name is "Sales"
            element value is SalesAmount
        
        PIECHART is a PieChart
            title is "Sales Distribution"
            show as donut
            element name is Product
            element value is SalesAmount
```

### Aggregation Table

```
SALESAGGREGATION is a List
    title is "Sales Aggregation"
    
    Charts
        SALESAGG is an AggregationTable
            title is "Sales Summary"
            is default
            suppress blank rows
            show totals first
            
            Dimensions
                REGION
                    is default sort order
                    caption is "Sales Region"
                PRODUCT
                    caption is representative text
            
            Measures
                sum SALESAMOUNT
                    label is "Total Sales"
                    display 2 decimals
                    currency symbol is "$"
                avg SALESAMOUNT
                    label is "Average Sale"
                    display 2 decimals
                instance count
                    label is "Number of Orders"
```

## Best Practices

### List Design
- Use descriptive list names and titles
- Choose appropriate view type for data
- Set reasonable default line counts
- Enable result set size for large lists
- Provide meaningful no data messages

### Search and Filter
- Use appropriate default filter operators
- Provide search forms for complex filtering
- Consider initially vs always display
- Use required search parameters for large datasets
- Enable keyword search for text-heavy data

### Display Fields
- Show most important fields first
- Use column widths appropriately
- Group related fields with group labels
- Add totals where meaningful
- Consider responsive card views

### Performance
- Use instance selection to limit results
- Implement manual load for expensive sections
- Suppress value retrieval when not visible
- Use fixed size lists appropriately
- Consider long running list for slow queries

### Actions
- Provide appropriate create/open actions
- Use restrict/enable judiciously
- Consider when conditions for actions
- Provide called out actions for common tasks
- Disable actions when not applicable

### Charts
- Choose appropriate chart types
- Provide meaningful titles and labels
- Set one chart as default
- Use legends appropriately
- Consider fixed scales for consistency

### Detail Sections
- Organize logically
- Use manual load for expensive content
- Provide visibility conditions
- Consider dashboard layouts
- Use appropriate widths and heights

## Related Concepts

- [Business Class Definition](business-class-definition.md) - Business class structure
- [Field Definition](field-definition.md) - Field structure
- [Actions](actions.md) - Action definitions
- [Conditions](conditions.md) - Boolean expressions
- [Relations](relations.md) - Relationships between classes
- [UI Definition](ui-definition.md) - UI components

## Summary

Lists provide comprehensive display and interaction capabilities for business class instances, supporting multiple view types, search and filter options, charts, detail sections, and actions. Proper list design enhances user experience and application performance.
