# Card Views

## Overview

Card Views define how business class instances are displayed in a card-based layout. They provide a visual, compact representation of data with support for images, text, buttons, and structured sections. Card Views are commonly used in gallery views, dashboards, and responsive layouts.

## Card View Types

### Standard Card View

```
<CardViewName> is a CardView
    [(show | hide) labels]
    [raise <AlertType> alert when <SimpleCondition>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
    [title is <Message>]
    [no wrap]
    [left column is ( <RelatedValue>
                    | <Icon>
                    | representative image
                    | random background )]
    [foreground text is <Message>]
    [display as (portrait | photo | full) image]
    [missing image is (<ImageName> | random background)]
    [foreground text is <Message>]
    main column
        (<FormField> | <FormText> | <ManualRepresentation>)
        …
        [visible when <SimpleCondition>]
    [button row]
        <FormButton>
        …
    [right column]
        [(left | right) align]
        (<FormField> | <FormText> | <ManualRepresentation>)
        …
        [visible when <SimpleCondition>]
    [button row]
        <FormButton>
        …
    [button row]
        <FormButton>
        …
```

### Sectioned Card View

```
<CardViewName> is a SectionedCardView
    [Image Section]
        image is <RelatedValue>
        <FormButton>
        …
        [use <#> lines]
        [tag is <RelatedValue>]
        [visible when <SimpleCondition>]
        [color of <Color> [when <SimpleCondition>]]
        …
    [Title Section]
        title is <Message>
        [use <#> lines]
        [link is <UILink>]
    [Row Section]
        …
        (<FormField> | <FormText> | <FormButton> | <ManualRepresentation>)
        …
    [Fields Section]
        …
        (<FormField> | <FormText> | <ManualRepresentation>)
        …
    [Action Section]
        [<CheckControl>]
        [<FormButton>]
        …
```

## Standard Card View

### Basic Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `show labels` / `hide labels` | Controls field label visibility | hide labels |
| `raise <AlertType> alert when <SimpleCondition>` | Conditional alert display | None |
| `mouse over text is <Message>` | Tooltip text | None |
| `link is <UILink>` | Clickable link for entire card | None |
| `title is <Message>` | Card title | None |
| `no wrap` | Prevents title wrapping | wrap (default) |

### Left Column

The left column typically displays an image or icon.

```
left column is ( <RelatedValue>
               | <Icon>
               | representative image
               | random background )
```

#### Left Column Options

| Option | Description |
|--------|-------------|
| `<RelatedValue>` | Field containing image data (must be an image type) |
| `<Icon>` | Static icon reference |
| `representative image` | Uses business class representative image |
| `random background` | Generates random background color |

#### Image Display Options

```
[foreground text is <Message>]
[display as (portrait | photo | full) image]
[missing image is (<ImageName> | random background)]
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `foreground text is <Message>` | Text overlay on image | None |
| `display as portrait` | Portrait-oriented display | portrait |
| `display as photo` | Photo display mode | - |
| `display as full` | Full-size image display | - |
| `missing image is <ImageName>` | Fallback image when missing | None |
| `missing image is random background` | Random color when missing | None |

### Main Column

The main column contains the primary content of the card.

```
main column
    (<FormField> | <FormText> | <ManualRepresentation>)
    …
    [visible when <SimpleCondition>]
```

- Contains form fields, text, or manual representations
- Supports conditional visibility
- Primary content area of the card

### Right Column

The right column provides additional information, typically right-aligned.

```
[right column]
    [(left | right) align]
    (<FormField> | <FormText> | <ManualRepresentation>)
    …
    [visible when <SimpleCondition>]
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `left align` | Left-align content | - |
| `right align` | Right-align content | right align |
| `visible when <SimpleCondition>` | Conditional visibility | Always visible |

### Button Rows

```
[button row]
    <FormButton>
    …
```

**Note:** `button row` is deprecated. Multiple button rows can be defined.

- Provides action buttons on the card
- Can have multiple button rows
- Buttons are typically right-aligned

## Sectioned Card View

Sectioned Card Views provide a more structured layout with predefined sections.

### Image Section

```
[Image Section]
    image is <RelatedValue>
    <FormButton>
    …
    [use <#> lines]
    [tag is <RelatedValue>]
    [visible when <SimpleCondition>]
    [color of <Color> [when <SimpleCondition>]]
    …
```

#### Image Section Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `image is <RelatedValue>` | Image field or value | None |
| `<FormButton>` | Buttons overlaid on image | None |
| `use <#> lines` | Height in lines | 4 lines |
| `tag is <RelatedValue>` | Tag/badge display | None |
| `visible when <SimpleCondition>` | Conditional visibility | Always visible |
| `color of <Color> when <SimpleCondition>` | Conditional color overlay | None |

**Features:**
- Displays image at top of card
- Supports overlay buttons
- Can display tags/badges
- Conditional color overlays
- Configurable height

### Title Section

```
[Title Section]
    title is <Message>
    [use <#> lines]
    [link is <UILink>]
```

#### Title Section Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `title is <Message>` | Card title text | None |
| `use <#> lines` | Height in lines | 1 line |
| `link is <UILink>` | Clickable link | None |

**Features:**
- Displays prominent title
- Configurable height
- Optional clickable link
- Typically below image section

### Row Section

```
[Row Section]
    …
    (<FormField> | <FormText> | <FormButton> | <ManualRepresentation>)
    …
```

**Important:** Must fit on one row; overflow is not supported.

**Features:**
- Horizontal layout of fields
- Single-line display
- Supports fields, text, buttons, and manual representations
- Multiple row sections can be defined
- No wrapping or overflow

### Fields Section

```
[Fields Section]
    …
    (<FormField> | <FormText> | <ManualRepresentation>)
    …
```

**Features:**
- Vertical layout of fields
- Standard field display
- Supports fields, text, and manual representations
- Multiple fields sections can be defined
- Primary content area

### Action Section

```
[Action Section]
    [<CheckControl>]
    [<FormButton>]
    …
```

**Important:** Must fit on one row; overflow is not supported.

**Features:**
- Action buttons at bottom of card
- Optional check control (left-justified)
- Buttons are right-justified
- Single-line display
- No wrapping or overflow

## Examples

### Example 1: Simple Card View

```
CUSTOMERCARD is a CardView
    show labels
    title is "<CustomerName>"
    
    left column is representative image
        display as portrait image
        missing image is DefaultCustomerIcon
    
    main column
        CUSTOMER
        CUSTOMERNAME
        CITY
        STATE
    
    right column
        BALANCE
        CREDITLIMIT
```

### Example 2: Card View with Alerts

```
ORDERCARD is a CardView
    hide labels
    title is "Order <Order>"
    raise Warning alert when Status = "Pending"
    raise Error alert when OrderDate < current date - 30 days
    mouse over text is "Click to view order details"
    link is OrderDetail
    
    left column is OrderStatusIcon
    
    main column
        ORDER
        CUSTOMER
        ORDERDATE
        TOTAL
    
    right column
        right align
        STATUS
        SHIPDATE
    
    button row
        APPROVE
        REJECT
```

### Example 3: Card View with Conditional Visibility

```
PRODUCTCARD is a CardView
    show labels
    title is "<ProductName>"
    no wrap
    
    left column is ProductImage
        display as photo image
        missing image is random background
        foreground text is "<ProductCode>"
    
    main column
        PRODUCTCODE
        PRODUCTNAME
        CATEGORY
        PRICE
            visible when actor has role "Sales"
        COST
            visible when actor has role "Manager"
    
    right column
        QUANTITYONHAND
        REORDERPOINT
```

### Example 4: Simple Sectioned Card View

```
EMPLOYEECARD is a SectionedCardView
    Image Section
        image is EmployeePhoto
        use 5 lines
        tag is Department
        visible when EmployeePhoto entered
        color of Blue when Status = "Active"
        color of Gray when Status = "Inactive"
    
    Title Section
        title is "<EmployeeName>"
        use 2 lines
        link is EmployeeDetail
    
    Row Section
        EMPLOYEE
        DEPARTMENT
        TITLE
    
    Fields Section
        EMAIL
        PHONE
        HIREDATE
    
    Action Section
        EDIT
        VIEW_DETAILS
```

### Example 5: Product Gallery Card

```
PRODUCTGALLERY is a SectionedCardView
    Image Section
        image is ProductImage
        use 6 lines
        tag is Category
        color of Green when OnSale = true
        color of Red when QuantityOnHand = 0
        QUICKVIEW
    
    Title Section
        title is "<ProductName>"
        use 1 line
        link is ProductDetail
    
    Row Section
        PRODUCTCODE
        CATEGORY
    
    Fields Section
        PRICE
        QUANTITYONHAND
        RATING
    
    Action Section
        ADDTOCART
        ADDTOWISHLIST
```

### Example 6: Dashboard Card

```
SALESDASHCARD is a SectionedCardView
    Title Section
        title is "Sales Summary"
        use 1 line
    
    Row Section
        TOTALORDERS
        TOTALSALES
        AVGORDERVALUE
    
    Fields Section
        TOPSALESPERSON
        TOPPRODUCT
        TOPREGION
    
    Action Section
        VIEWDETAILS
        EXPORTDATA
```

### Example 7: Contact Card

```
CONTACTCARD is a SectionedCardView
    Image Section
        image is ContactPhoto
        use 4 lines
        missing image is random background
        foreground text is "<Initials>"
        visible when ContactPhoto entered
    
    Title Section
        title is "<FirstName> <LastName>"
        use 2 lines
        link is ContactDetail
    
    Row Section
        COMPANY
        JOBTITLE
    
    Fields Section
        EMAIL
        PHONE
        MOBILE
        ADDRESS
    
    Action Section
        SENDEMAIL
        CALL
        SCHEDULE
```

### Example 8: Task Card with Conditional Actions

```
TASKCARD is a SectionedCardView
    Image Section
        image is TaskTypeIcon
        use 3 lines
        tag is Priority
        color of Red when Priority = "High"
        color of Yellow when Priority = "Medium"
        color of Green when Priority = "Low"
    
    Title Section
        title is "<TaskName>"
        use 2 lines
    
    Row Section
        TASKID
        ASSIGNEDTO
        DUEDATE
    
    Fields Section
        DESCRIPTION
        STATUS
        PERCENTCOMPLETE
    
    Action Section
        COMPLETE
            visible when Status != "Completed"
        REOPEN
            visible when Status = "Completed"
        EDIT
```

### Example 9: Notification Card

```
NOTIFICATIONCARD is a SectionedCardView
    Image Section
        image is NotificationIcon
        use 2 lines
        color of Blue when NotificationType = "Info"
        color of Yellow when NotificationType = "Warning"
        color of Red when NotificationType = "Error"
    
    Title Section
        title is "<NotificationTitle>"
        use 2 lines
    
    Fields Section
        NOTIFICATIONMESSAGE
        NOTIFICATIONDATE
        NOTIFICATIONTIME
    
    Action Section
        MARKASREAD
        DISMISS
```

### Example 10: Event Card

```
EVENTCARD is a SectionedCardView
    Image Section
        image is EventImage
        use 5 lines
        tag is EventType
        REGISTER
    
    Title Section
        title is "<EventName>"
        use 2 lines
        link is EventDetail
    
    Row Section
        EVENTDATE
        EVENTTIME
        LOCATION
    
    Fields Section
        DESCRIPTION
        ORGANIZER
        ATTENDEECOUNT
        CAPACITY
    
    Action Section
        REGISTER
            visible when RegistrationStatus = "Open"
        CANCEL
            visible when RegistrationStatus = "Registered"
        VIEWDETAILS
```

## Use Cases

### Gallery Views
- Product catalogs
- Image galleries
- Portfolio displays
- Media libraries

### Dashboards
- KPI summaries
- Metric displays
- Status overviews
- Quick stats

### Lists
- Contact lists
- Employee directories
- Customer lists
- Order summaries

### Mobile Interfaces
- Responsive layouts
- Touch-friendly displays
- Compact information
- Quick actions

### Notifications
- Alert displays
- Message cards
- Activity feeds
- Status updates

## Best Practices

### Design

1. **Keep It Simple**
   - Limit information to essentials
   - Avoid overcrowding
   - Use white space effectively
   - Prioritize readability

2. **Visual Hierarchy**
   - Use title section for primary information
   - Place important data in main/fields sections
   - Use images to draw attention
   - Group related information

3. **Consistency**
   - Use consistent card layouts across similar data
   - Maintain uniform sizing
   - Apply consistent color schemes
   - Use standard button placements

### Images

1. **Image Selection**
   - Use representative images when available
   - Provide fallback images
   - Consider random backgrounds for variety
   - Optimize image sizes

2. **Image Display**
   - Choose appropriate display mode (portrait/photo/full)
   - Use foreground text sparingly
   - Consider missing image scenarios
   - Test with various image sizes

### Content

1. **Field Selection**
   - Show most important fields first
   - Limit number of fields
   - Use conditional visibility wisely
   - Consider mobile display

2. **Labels**
   - Hide labels when context is clear
   - Show labels for clarity when needed
   - Use consistent label styles
   - Keep labels concise

### Actions

1. **Button Placement**
   - Place primary actions prominently
   - Group related actions
   - Use action section for main actions
   - Consider button row deprecation

2. **Conditional Actions**
   - Show relevant actions only
   - Use visibility conditions
   - Provide clear action labels
   - Test all action states

### Performance

1. **Optimization**
   - Use conditional visibility to reduce rendering
   - Optimize image loading
   - Limit number of cards displayed
   - Consider lazy loading

2. **Responsive Design**
   - Test on various screen sizes
   - Use responsive card views in lists
   - Consider mobile-first design
   - Ensure touch-friendly sizing

### Alerts and Notifications

1. **Alert Usage**
   - Use alerts for important status
   - Choose appropriate alert types
   - Test alert conditions
   - Avoid alert overload

2. **Visual Feedback**
   - Use color coding meaningfully
   - Provide mouse over text
   - Use tags for quick identification
   - Consider accessibility

## Standard Card View vs Sectioned Card View

### When to Use Standard Card View

- Simple layouts with 2-3 columns
- Flexible field arrangement
- Legacy compatibility
- Custom layouts

### When to Use Sectioned Card View

- Structured, consistent layouts
- Image-heavy displays
- Gallery views
- Modern, responsive designs
- Dashboard cards
- Mobile-optimized interfaces

### Key Differences

| Feature | Standard Card View | Sectioned Card View |
|---------|-------------------|---------------------|
| Layout | Column-based | Section-based |
| Structure | Flexible | Predefined sections |
| Image handling | Left column only | Dedicated image section |
| Title | Optional, in header | Dedicated title section |
| Rows | Not supported | Dedicated row sections |
| Actions | Button rows (deprecated) | Dedicated action section |
| Complexity | Simpler | More structured |
| Use case | General purpose | Gallery/dashboard |

## Related Concepts

- [Lists](lists.md) - List definitions using card views
- [UI Definition](ui-definition.md) - UI components
- [Field Definition](field-definition.md) - Field structure
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Conditions](conditions.md) - Conditional visibility

## Summary

Card Views provide flexible, visual representations of business class instances suitable for galleries, dashboards, and responsive layouts. Standard Card Views offer column-based flexibility, while Sectioned Card Views provide structured, modern layouts with dedicated sections for images, titles, rows, fields, and actions. Proper card view design enhances user experience and information accessibility.
