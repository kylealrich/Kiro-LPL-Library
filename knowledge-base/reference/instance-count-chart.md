# Instance Count Chart

## Overview

Instance Count Charts display the count of business class instances grouped by specific field values. They provide a simple way to visualize the distribution of records across different categories or dimensions. Instance Count Charts are useful for dashboards, reports, and analytical views where understanding record distribution is important.

## Syntax

```
<ChartName> is an InstanceCountChart
    [title is <Message>]
    Chart Fields
        <RelatedValue>
        …
        [label is <Message>]
        [link is <UILink>]
```

## Definition

### Basic Structure

```
InstanceCountChartDefinition ::=
    <ChartName> is an InstanceCountChart
        [title is <Message>]
        Chart Fields
            <RelatedValue>
            …
            [label is <Message>]
            [link is <UILink>]
```

## Attributes

### Chart Name

```
<ChartName> is an InstanceCountChart
```

The unique identifier for the chart. Must follow LPL naming conventions (uppercase alphanumeric).

### Title

```
[title is <Message>]
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `title is <Message>` | Display title for the chart | ChartName |

If not specified, the chart name is used as the title.

### Chart Fields

```
Chart Fields
    <RelatedValue>
    …
    [label is <Message>]
    [link is <UILink>]
```

Defines the fields to group by when counting instances.

#### Chart Field Attributes

| Attribute | Description | Required |
|-----------|-------------|----------|
| `<RelatedValue>` | Field or value to group by | Yes |
| `label is <Message>` | Custom label for the field | No |
| `link is <UILink>` | Clickable link for drill-down | No |

**Features:**
- Multiple fields can be specified
- Each field creates a grouping dimension
- Instance counts are calculated for each unique value
- Optional labels override default field labels
- Optional links enable drill-down navigation

## How It Works

1. **Grouping**: Records are grouped by the specified chart field values
2. **Counting**: The number of instances in each group is calculated
3. **Display**: Results are displayed as a chart showing counts per group
4. **Interaction**: Optional links allow users to drill into specific groups

## Examples

### Example 1: Simple Instance Count

```
ORDERSBYSTATUS is an InstanceCountChart
    title is "Orders by Status"
    Chart Fields
        STATUS
```

Displays the count of orders grouped by their status (e.g., Pending: 15, Approved: 23, Shipped: 42).

### Example 2: Instance Count with Custom Label

```
CUSTOMERSBYREGION is an InstanceCountChart
    title is "Customer Distribution by Region"
    Chart Fields
        REGION
            label is "Sales Region"
```

Shows customer counts by region with a custom label for the grouping field.

### Example 3: Instance Count with Drill-Down

```
PRODUCTSBYCAT is an InstanceCountChart
    title is "Products by Category"
    Chart Fields
        CATEGORY
            label is "Product Category"
            link is ProductListByCategory
```

Displays product counts by category with clickable links to view the products in each category.

### Example 4: Multiple Grouping Fields

```
SALESANALYSIS is an InstanceCountChart
    title is "Sales Distribution"
    Chart Fields
        REGION
            label is "Sales Region"
            link is SalesByRegion
        PRODUCTTYPE
            label is "Product Type"
            link is SalesByType
        SALESPERSON
            label is "Sales Person"
            link is SalesBySalesPerson
```

Provides multiple grouping options for analyzing sales distribution.

### Example 5: Employee Distribution

```
EMPLOYEESBYdept is an InstanceCountChart
    title is "Employees by Department"
    Chart Fields
        DEPARTMENT
            label is "Department"
            link is EmployeeListByDept
```

Shows how many employees are in each department with drill-down capability.

### Example 6: Order Analysis

```
ORDERANALYSIS is an InstanceCountChart
    title is "Order Analysis"
    Chart Fields
        ORDERSTATUS
            label is "Status"
        ORDERTYPE
            label is "Type"
        PRIORITY
            label is "Priority Level"
```

Analyzes orders across multiple dimensions without drill-down links.

### Example 7: Support Ticket Distribution

```
TICKETSBYPRIORITY is an InstanceCountChart
    title is "Support Tickets by Priority"
    Chart Fields
        PRIORITY
            label is "Priority Level"
            link is TicketList
```

Displays support ticket counts grouped by priority with link to ticket list.

### Example 8: Inventory Status

```
INVENTORYSTATUS is an InstanceCountChart
    title is "Inventory Status Overview"
    Chart Fields
        STOCKSTATUS
            label is "Stock Status"
            link is ProductsByStatus
```

Shows inventory distribution across different stock statuses (In Stock, Low Stock, Out of Stock).

### Example 9: Project Status Dashboard

```
PROJECTSTATUS is an InstanceCountChart
    title is "Projects by Status"
    Chart Fields
        PROJECTSTATUS
            label is "Status"
            link is ProjectList
        PROJECTPHASE
            label is "Phase"
            link is ProjectsByPhase
```

Dashboard view of project distribution by status and phase.

### Example 10: Customer Segmentation

```
CUSTOMERSEGMENTS is an InstanceCountChart
    title is "Customer Segmentation"
    Chart Fields
        CUSTOMERSEGMENT
            label is "Segment"
            link is CustomersBySegment
        CUSTOMERTYPE
            label is "Type"
            link is CustomersByType
        ACCOUNTSTATUS
            label is "Account Status"
            link is CustomersByStatus
```

Analyzes customer distribution across multiple segmentation criteria.

## Use Cases

### Dashboard Metrics
- Quick overview of record distribution
- KPI visualization
- Status summaries
- Category breakdowns

### Analytical Reports
- Distribution analysis
- Trend identification
- Category comparison
- Volume metrics

### Operational Monitoring
- Status tracking
- Queue monitoring
- Workload distribution
- Resource allocation

### Business Intelligence
- Customer segmentation
- Product analysis
- Sales distribution
- Performance metrics

## Best Practices

### Chart Design

1. **Choose Meaningful Fields**
   - Select fields that provide valuable insights
   - Use fields with discrete, manageable values
   - Avoid fields with too many unique values
   - Consider business relevance

2. **Provide Clear Titles**
   - Use descriptive chart titles
   - Make purpose immediately clear
   - Follow consistent naming conventions
   - Consider user context

3. **Use Custom Labels**
   - Provide user-friendly labels
   - Clarify technical field names
   - Use business terminology
   - Keep labels concise

### Drill-Down Links

1. **Enable Navigation**
   - Provide links for detailed views
   - Link to relevant lists or reports
   - Ensure links pass appropriate context
   - Test navigation paths

2. **Consistent Linking**
   - Use consistent link patterns
   - Ensure all groups are linkable
   - Provide meaningful destinations
   - Consider user workflow

### Field Selection

1. **Appropriate Grouping**
   - Choose fields with reasonable cardinality
   - Avoid fields with too many unique values
   - Consider hierarchical groupings
   - Test with realistic data

2. **Multiple Dimensions**
   - Provide multiple grouping options
   - Allow users to choose perspective
   - Consider related dimensions
   - Balance complexity and utility

### Performance

1. **Optimize Queries**
   - Ensure fields are indexed
   - Consider data volume
   - Test with production-like data
   - Monitor query performance

2. **Limit Complexity**
   - Don't overload with too many fields
   - Consider separate charts for different analyses
   - Balance detail and performance
   - Use instance selection when appropriate

### User Experience

1. **Intuitive Display**
   - Present data clearly
   - Use appropriate chart types
   - Consider color coding
   - Ensure readability

2. **Contextual Information**
   - Provide helpful labels
   - Include relevant metadata
   - Consider tooltips
   - Show totals when useful

## Common Patterns

### Status Distribution Pattern
```
<ChartName> is an InstanceCountChart
    title is "<Entity> by Status"
    Chart Fields
        STATUS
            label is "Status"
            link is <EntityList>
```

### Category Analysis Pattern
```
<ChartName> is an InstanceCountChart
    title is "<Entity> by Category"
    Chart Fields
        CATEGORY
            label is "Category"
            link is <EntityListByCategory>
```

### Multi-Dimensional Analysis Pattern
```
<ChartName> is an InstanceCountChart
    title is "<Entity> Distribution"
    Chart Fields
        DIMENSION1
            label is "<Label1>"
            link is <List1>
        DIMENSION2
            label is "<Label2>"
            link is <List2>
        DIMENSION3
            label is "<Label3>"
            link is <List3>
```

### Simple Dashboard Metric Pattern
```
<ChartName> is an InstanceCountChart
    title is "<Metric Name>"
    Chart Fields
        GROUPINGFIELD
```

## Integration with Lists

Instance Count Charts are often used in conjunction with lists to provide both summary and detail views.

### Example Integration

```
ORDERDASHBOARD is a List
    title is "Order Dashboard"
    implements DashBoardView
    
    Detail Sections
        ORDERSUMMARY
            title is "Order Summary"
            Charts
                ORDERSBYSTATUS is an InstanceCountChart
                    title is "Orders by Status"
                    Chart Fields
                        STATUS
                            label is "Order Status"
                            link is OrderList
        
        ORDERLIST
            title is "Order Details"
            list is ORDERLIST
```

## Comparison with Other Chart Types

### Instance Count Chart vs Aggregation Table

| Feature | Instance Count Chart | Aggregation Table |
|---------|---------------------|-------------------|
| Purpose | Count instances | Aggregate measures |
| Complexity | Simple | Complex |
| Measures | Count only | Sum, avg, min, max, count |
| Dimensions | Single field | Multiple dimensions |
| Use case | Distribution | Analysis |

### Instance Count Chart vs Bar Chart

| Feature | Instance Count Chart | Bar Chart |
|---------|---------------------|-----------|
| Data source | Automatic counting | Explicit values |
| Configuration | Minimal | More detailed |
| Flexibility | Limited | High |
| Use case | Quick counts | Custom visualizations |

## Troubleshooting

### Issue: Too Many Groups

**Problem:** Chart displays too many groups, making it unreadable.

**Solutions:**
- Use fields with fewer unique values
- Apply instance selection to filter data
- Consider hierarchical grouping
- Use multiple charts for different perspectives

### Issue: Empty Chart

**Problem:** Chart displays no data.

**Solutions:**
- Verify instance selection criteria
- Check that records exist
- Ensure field has values
- Review context filters

### Issue: Slow Performance

**Problem:** Chart takes too long to load.

**Solutions:**
- Add indexes on grouping fields
- Apply instance selection to reduce dataset
- Consider caching strategies
- Optimize underlying queries

### Issue: Unclear Labels

**Problem:** Field names are not user-friendly.

**Solutions:**
- Use custom labels
- Provide descriptive titles
- Follow naming conventions
- Consider user perspective

## Related Concepts

- [Lists](lists.md) - List definitions and charts
- [Card Views](card-views.md) - Card-based displays
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Field Definition](field-definition.md) - Field structure
- [Conditions](conditions.md) - Conditional logic
- [UI Definition](ui-definition.md) - UI components

## Summary

Instance Count Charts provide a simple, effective way to visualize the distribution of business class instances across different field values. They are ideal for dashboards, reports, and analytical views where understanding record counts by category is important. With minimal configuration, they offer quick insights into data distribution with optional drill-down capabilities for detailed exploration.

**Key Features:**
- Automatic instance counting
- Grouping by field values
- Custom labels for clarity
- Drill-down links for navigation
- Multiple grouping dimensions
- Simple configuration
- Dashboard integration

**Best For:**
- Status distribution
- Category analysis
- Quick metrics
- Dashboard summaries
- Operational monitoring
- Distribution visualization
