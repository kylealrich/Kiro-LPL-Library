# Form Base Definitions

## Overview

Form Base Definitions specify the building blocks for creating form layouts in LPL. These include form buttons, form fields, layout directives, sections, and multi-select fields. Together, they provide comprehensive control over form appearance, behavior, and user interaction.

## Form Button

Form buttons provide interactive controls for actions, navigation, and data manipulation.

### Basic Syntax

```
FormButton ::=
    button of ( <Message> [with (<Icon> | <ActionIcon>)]
                [<TextStyle>…]
              | <Icon>
              | <ActionIcon>)
    [is primary]
    [(valid | visible) when <Condition>]
    [(link is <UILink>
     | sub form is (<FormName> | primary | inline)
       [<FormDefinition>]
     | <Form>
       [create action is [<StateName>.]<ActionName>]
       [open action is [<StateName>.]<ActionName>]
       …
       [<FormDefinition>]
     | <List>
     | form invoke is <FormInvokeName>
     | action is ([<RelatedLink>.][<StateName>.]<ActionName> | <StandardAction>)
     | show editor for <RelatedField>
       [using mime type <RelatedValue>]
     | show preview of <RelatedField>
     | view <RelatedField>
       [using mime type <RelatedValue>]
     | export <RelatedField>)]
    …
    [when <SimpleCondition>]
    [(restrict | enable) action [<StateName>.]<ActionName>]
    …
    [<Label>]
    [(left align | center | right align)]
    [(top align | middle | bottom align)]
    [align as label]
    [indent]
    [display as link]
    [raise <AlertType> alert when <SimpleCondition>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
    [hidden]
    [when button pressed]
        ( validate (this field | <RelatedField>)
        | <RelatedField> = <RelatedValue>
        | refresh <RelatedField>
        | refresh dependent panels)
        …
        [when <SimpleCondition>]
```

### Button Display

#### Text Button
```
button of <Message> [with (<Icon> | <ActionIcon>)]
    [<TextStyle>…]
```

Creates a button with text label, optional icon, and text styling.

**Example:**
```
button of "Save" with SaveIcon
button of "Submit Order"
```

#### Icon Button
```
button of <Icon>
button of <ActionIcon>
```

Creates a button with only an icon (no text).

**Example:**
```
button of EditIcon
button of DeleteIcon
```

### Button Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `is primary` | Designates as primary button | Not primary |
| `valid when <Condition>` | Button enabled only when condition true | Always valid |
| `visible when <Condition>` | Button visible only when condition true | Always visible |
| `<Label>` | Label text | No label |
| `left align` / `center` / `right align` | Horizontal alignment | left align (center in MatrixForm) |
| `top align` / `middle` / `bottom align` | Vertical alignment | top align |
| `align as label` | Align in label column | Standard alignment |
| `indent` | Indent the button | No indent |
| `display as link` | Display as hyperlink | Display as button |
| `hidden` | Hide the button | Visible |

### Button Actions

#### Link
```
link is <UILink>
```

Navigates to a URL or UI location.

#### Sub Form
```
sub form is (<FormName> | primary | inline)
    [<FormDefinition>]
```

Opens a sub-form. FormDefinition valid on inline Form only.

#### Form
```
<Form>
    [create action is [<StateName>.]<ActionName>]
    [open action is [<StateName>.]<ActionName>]
    …
    [<FormDefinition>]
```

Opens a form. Button is implicitly invalid if in create mode and target is afforded by current instance. FormDefinition valid on inline Form only.

#### List
```
<List>
```

Opens a list. Button is implicitly invalid if in create mode and target is afforded by current instance.

#### Form Invoke
```
form invoke is <FormInvokeName>
```

Invokes a form invoke definition.

#### Action
```
action is ([<RelatedLink>.][<StateName>.]<ActionName> | <StandardAction>)
```

Executes an action on the current instance or related instance.

**Example:**
```
action is APPROVE
action is Customer.UPDATE
action is PENDING.SUBMIT
```

#### Show Editor
```
show editor for <RelatedField>
    [using mime type <RelatedValue>]
```

Opens an editor for the field. Can be a BinaryDocument, Text, GroupField, or ArrayField.

#### Show Preview
```
show preview of <RelatedField>
```

Shows a preview of the field. Must be an Image.

#### View
```
view <RelatedField>
    [using mime type <RelatedValue>]
```

Views the field content. Must be a BinaryDocument or Text.

#### Export
```
export <RelatedField>
```

Exports the field content. Must be a BinaryDocument or Text.

### Button Behavior

#### When Button Pressed
```
when button pressed
    ( validate (this field | <RelatedField>)
    | <RelatedField> = <RelatedValue>
    | refresh <RelatedField>
    | refresh dependent panels)
    …
    [when <SimpleCondition>]
```

Defines actions to perform when button is pressed.

**Actions:**
- **validate**: Validates a field (RelatedField must be mutable on form or list)
- **assignment**: Sets field value (RelatedField must be mutable on form or list)
- **refresh**: Refreshes a field (RelatedField must be on form or list)
- **refresh dependent panels**: Refreshes all dependent panels

### Button Alerts and Feedback

```
raise <AlertType> alert when <SimpleCondition>
    …
mouse over text is <Message>
link is <UILink>
```

Provides visual feedback and tooltips.

### Multiple Targets

```
[when <SimpleCondition>]
```

Must have a condition if defining multiple targets. Allows conditional button behavior.

**Example:**
```
button of "Edit"
    action is EDIT
        when Status = "Draft"
    action is REVIEW
        when Status = "Pending"
```

### Action Restrictions

```
(restrict | enable) action [<StateName>.]<ActionName>
    …
```

Valid on form invoke only. Controls action availability.

## Form Field

Form fields display and allow editing of business class field values.

### Basic Syntax

```
FormField ::=
    (<RelatedField> | effective date | reason code | action comment)
    [(label is (<RelatedField> | <Message> | number) | no label)]
    [<TextStyle>…]
    [mouse over text is <Message>]
    [suppress label]
    [<FieldStyle>…]
    [align as label]
    [form is <FormName>]
    [indent]
    [display (only | as text)]
    [hidden entry]
    [allow update]
    [link is <UILink>]
    [compact format [(only | button of <Message>)]]
    [display as select]
    [select is [<RelatedLink>.]<ListName>]
    [default filter operator is (contains | starts with | equals)]
    [use text area [of <Literal> lines]]
    [show up to <Literal> characters]
    [keypad is (email | telephone | url)]
    [prompt for (image | video | audio | document)]
    [compare with <RelatedField>]
    [time zone is <RelatedField>]
    [limit to (5|10|15|20|30) minute increments]
    [display <Number> decimals]
    [currency symbol is <RelatedValue>]
        [first line and totals only]
    [show percent symbol]
        [first line and totals only]
    [display negative amount using (minus | parens)]
    [display as (hijri | gregorian) date]
    [display as [(portrait | photo | full)] image [up to <Literal> lines]]
    [missing image is (<ImageName> | random background)]
    [foreground text is <Message>]
    [image is <FullFieldName>]
    [title is <FullFieldName>]
    [display as switch]
    [display as tag]
        [color of <Color> [when <SimpleCondition>]]
        …
    [display as rating]
    [display as slider]
        range is from <RelatedValue> to <RelatedValue>
        [step is <RelatedValue>]
        [<AlertType> alert range is from <RelatedValue> to <RelatedValue>]
        …
        [mouse over text is <Message>]
        [link is <UILink>]
    [display as ((meter | thermometer) chart | progress bar) [up to <Literal> lines]]
        [meter range is <RelatedValue>]
        [<AlertType> alert range is from <RelatedValue> to <RelatedValue>]
        …
        [mouse over text is <Message>]
        [link is <UILink>]
    [raise <AlertType> alert when <SimpleCondition>]
    …
    [mouse over text is <Message>]
    [show as <StatusIcon> when <SimpleCondition>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
    [display when blank]
    [display zero]
    [display blank when zero]
    [display as duration]
    [display as byte size]
    [display using <JavaFormat>]
    [display as [horizontal] radio buttons]
    [display (date | time | [date and] hours and minutes)]
    [display with time zone]
    [ignore daylight savings time]
    [<States>]
    [display as required [when <SimpleCondition>]]
    [hidden]
    [required]
    [initial value is <RelatedValue>]
    [constraint <Condition>]
        [<Message>]
    [depends on <RelatedField>[, <RelatedField>]…]
    [derived value is <RelatedValue>]
    [when value changed]
        ( validate (this field | <RelatedField>)
        | <RelatedField> = <RelatedValue>
        | refresh <RelatedField>
        | refresh dependent panels)
        …
        [when <SimpleCondition>]
```

### Field Types

#### Standard Field
```
<RelatedField>
```

References a business class field.

#### Special Fields
```
effective date
reason code
action comment
```

- **effective date**: Date field that sets the 'as of' date context for any action executed from the form
- **reason code**: Standard reason code field
- **action comment**: Standard action comment field

### Field Labels

```
[(label is (<RelatedField> | <Message> | number) | no label)]
[suppress label]
```

| Option | Description | Default |
|--------|-------------|---------|
| `label is <RelatedField>` | Use another field's value as label | Field Label |
| `label is <Message>` | Custom label text | Field Label |
| `label is number` | Use number as label | Field Label |
| `no label` | No label displayed | Field Label |
| `suppress label` | Hide the label | Show label |

### Field Display Modes

#### Display Only
```
display (only | as text)
```

Default to 'only' on SummaryForm and ListDefinition. Makes field read-only.

- **display only**: Read-only display
- **display as text**: Display as plain text (valid only with link)

#### Hidden Entry
```
hidden entry
```

Same behavior as Password field but no encryption.

#### Allow Update
```
allow update
```

Default on non-related field within a normal Form. Allows field editing.

### Field Presentation

#### Link
```
link is <UILink>
```

Valid only when display only. Makes field clickable.

#### Compact Format
```
compact format [(only | button of <Message>)]
```

Displays field in compact format.

#### Display as Select
```
display as select
```

Overrides implements UserDefinedStates. Shows dropdown selection.

#### Select List
```
select is [<RelatedLink>.]<ListName>
```

Valid on key field only. Specifies list for selection.

#### Default Filter Operator
```
default filter operator is (contains | starts with | equals)
```

Valid only on alpha field types.

### Text Input

#### Text Area
```
use text area [of <Literal> lines]
```

Use a text area control to display this field.

#### Character Limit
```
show up to <Literal> characters
```

Default is 50 characters.

#### Keypad
```
keypad is (email | telephone | url)
```

Specifies mobile keypad type.

#### Prompt For
```
prompt for (image | video | audio | document)
```

Prompts for specific media type.

### Field Comparison

```
compare with <RelatedField>
```

This field and RelatedField must be of type LPL. Enables comparison view.

### Date and Time

#### Time Zone
```
time zone is <RelatedField>
```

Valid only on TimeStamp field. RelatedField must be a TimeZone.

#### Time Increments
```
limit to (5|10|15|20|30) minute increments
```

Valid only on TimeStamp and Time fields.

#### Date Display
```
display as (hijri | gregorian) date
display (date | time | [date and] hours and minutes)
display with time zone
ignore daylight savings time
```

### Numeric Display

#### Decimals
```
display <Number> decimals
```

Number of decimal places to display.

#### Currency
```
currency symbol is <RelatedValue>
    [first line and totals only]
```

Displays currency symbol.

#### Percent
```
show percent symbol
    [first line and totals only]
```

Displays percent symbol.

#### Negative Amounts
```
display negative amount using (minus | parens)
```

How to display negative numbers.

### Image Display

```
display as [(portrait | photo | full)] image [up to <Literal> lines]
missing image is (<ImageName> | random background)
foreground text is <Message>
image is <FullFieldName>
title is <FullFieldName>
```

Default: 'display as portrait', 8 lines.

- **image is**: RelatedField could be an Array of images or Array of GroupFields containing an image
- **title is**: If Array of GroupFields, must specify image field and may specify title

### Special Display Modes

#### Switch
```
display as switch
```

Valid on Boolean type field only. Shows toggle switch.

#### Tag
```
display as tag
    [color of <Color> [when <SimpleCondition>]]
    …
```

Displays as colored tag.

#### Rating
```
display as rating
```

Must be a Numeric or Decimal field. Shows star rating.

#### Slider
```
display as slider
    range is from <RelatedValue> to <RelatedValue>
    [step is <RelatedValue>]
    [<AlertType> alert range is from <RelatedValue> to <RelatedValue>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
```

Shows slider control.

#### Meter/Thermometer/Progress Bar
```
display as ((meter | thermometer) chart | progress bar) [up to <Literal> lines]
    [meter range is <RelatedValue>]
    [<AlertType> alert range is from <RelatedValue> to <RelatedValue>]
    …
    [mouse over text is <Message>]
    [link is <UILink>]
```

Default: 8 lines (progress bar can have 1 line). Meter range not required when Percent type field.

#### Radio Buttons
```
display as [horizontal] radio buttons
```

Must be a States or UserDefinedStates field.

### Value Display Options

```
display when blank
display zero
display blank when zero
display as duration
display as byte size
display using <JavaFormat>
```

- **display when blank**: On SummaryForm, default is to not display blank fields
- **display zero**: Display '0' if value is blank
- **display blank when zero**: Display blank if value is zero
- **display as duration**: Can be numeric field in seconds, Time, Date, or TimeStamp
- **display as byte size**: Must be a numeric field

### Alerts and Status

```
raise <AlertType> alert when <SimpleCondition>
    …
mouse over text is <Message>
show as <StatusIcon> when <SimpleCondition>
    …
mouse over text is <Message>
link is <UILink>
```

### Field Validation and Behavior

#### Display as Required
```
display as required [when <SimpleCondition>]
```

Shows field as required (visual indicator).

#### Hidden
```
hidden
```

Valid on configuration only. Hides the field.

#### Required
```
required
```

Valid on configuration only. Makes field required.

#### Initial Value
```
initial value is <RelatedValue>
```

Valid on configuration only. Sets default value.

#### Constraint
```
constraint <Condition>
    [<Message>]
```

Valid on configuration only. Adds validation constraint.

#### Dependencies
```
depends on <RelatedField>[, <RelatedField>]…
```

RelatedField must be a mutable field on a form or list. Declares field dependencies.

#### Derived Value
```
derived value is <RelatedValue>
```

Calculates field value from expression.

#### When Value Changed
```
when value changed
    ( validate (this field | <RelatedField>)
    | <RelatedField> = <RelatedValue>
    | refresh <RelatedField>
    | refresh dependent panels)
    …
    [when <SimpleCondition>]
```

Actions to perform when field value changes. RelatedField must be mutable on form or list.

### Group and Array Fields

```
form is <FormName>
```

Valid on Group and Array fields only. Specifies form for nested display.

### Alignment

```
align as label
indent
```

- **align as label**: Make field line up in label column (valid only with no label)
- **indent**: Indent the field

## Form Layout

Form layouts define the visual structure and arrangement of form components.

### Basic Syntax

```
FormLayout ::=
    <LayoutDirective>
    [( <FormField>
     | <FormButton>
     | <CheckControl>
     | <CaptchaControl>
     | <ManualRepresentation>
     | <FormText>
     | <MultiSelectField>
     | <FormLayout>…)]
    …
```

### Layout Directives

```
LayoutDirective ::=
    ( paragraph [centered] [distributed]
    | single column [independent]
    | two column <ColumnOptions>
    | three column <ColumnOptions>
    | four column <ColumnOptions>
    | five column <ColumnOptions>
    | six column <ColumnOptions>
    | seven column <ColumnOptions>
    | eight column <ColumnOptions>
    | nine column <ColumnOptions>
    | ten column <ColumnOptions>
    | column headers
    | row headers
    | column(1|2|3|4|5|6|7|8|9|10) [of <Percent>]
    | <Header>
    | (valid | visible) when [<BusinessClass>.]<SimpleCondition>
    | locale of <RelatedValue>
    | display as text
    | blank line
    | page break
    | footer
    | large menu
    | small menu
    | <Form>
    | <List>)
```

### Column Options

```
ColumnOptions ::=
    [grid] [distributed] [independent] [table] [disable horizontal alignment]
```

| Option | Description |
|--------|-------------|
| `grid` | Grid layout |
| `distributed` | Distribute columns evenly |
| `independent` | Independent column sizing |
| `table` | Table layout |
| `disable horizontal alignment` | Disable horizontal alignment |

### Layout Types

#### Paragraph
```
paragraph [centered] [distributed]
```

Paragraph layout with optional centering and distribution.

#### Single Column
```
single column [independent]
```

Single column layout.

#### Multi-Column
```
two column <ColumnOptions>
three column <ColumnOptions>
...
ten column <ColumnOptions>
```

Multi-column layouts (2-10 columns).

#### Column Headers/Rows
```
column headers
row headers
```

Valid only with '<n> column table'.

#### Specific Column
```
column(1|2|3|4|5|6|7|8|9|10) [of <Percent>]
```

Specifies a specific column with optional width percentage.

### Conditional Layout

```
(valid | visible) when [<BusinessClass>.]<SimpleCondition>
```

Must specify BusinessClass when in MenuLayout.

### Other Directives

| Directive | Description |
|-----------|-------------|
| `<Header>` | Header section |
| `locale of <RelatedValue>` | Locale-specific layout |
| `display as text` | Display as text |
| `blank line` | Insert blank line |
| `page break` | Insert page break |
| `footer` | Footer section |
| `large menu` | Large menu style |
| `small menu` | Small menu style (default) |
| `<Form>` | Embedded form (must be top level) |
| `<List>` | Embedded list (must be top level) |

## Section Layout

Sections provide logical grouping of form components.

### Syntax

```
SectionLayout ::=
    <SectionDirective>
    <FormLayout>
    …

SectionDirective ::=
    section of <SectionName> [visible when <SimpleCondition>]
    [label is <Message>]
```

### Section Attributes

| Attribute | Description |
|-----------|-------------|
| `section of <SectionName>` | Section identifier |
| `visible when <SimpleCondition>` | Conditional visibility |
| `label is <Message>` | Section label |

## Multi-Select Field

Multi-select fields allow selection of multiple values.

### Syntax

```
MultiSelectField ::=
    multi-select field is <FullFieldName>
    [(label is (<RelatedField> | <Message>) | no label)]
    [<TextStyle>…]
    [mouse over text is <Message>]
    [display field is <RelatedField>]
    [select is [<RelatedLink>.]<ListName>]
    [use <Literal> lines]
    [display as required [when <SimpleCondition>]]
    [hidden]
    [required]
    [initial value is <RelatedValue>]
    [constraint <Condition>]
        [<Message>]
    [when value changed]
        ( validate (this field | <RelatedField>)
        | <RelatedField> = <RelatedValue>
        | refresh <RelatedField>
        | refresh dependent panels)
        …
        [when <SimpleCondition>]
```

### Multi-Select Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `multi-select field is <FullFieldName>` | Field to select (key field or array of key fields) | Required |
| `label is` | Label options | Field Label |
| `display field is <RelatedField>` | Field to display in selection | Default display |
| `select is <ListName>` | List for selection (valid on key field only) | Default list |
| `use <Literal> lines` | Number of lines to display | 5 lines |
| `display as required` | Show as required | Not required |
| `hidden` | Hide field (valid on configuration only) | Visible |
| `required` | Make required (valid on configuration only) | Not required |
| `initial value is` | Default value (valid on configuration only) | No default |
| `constraint` | Validation constraint (valid on configuration only) | No constraint |

### Multi-Select Behavior

```
when value changed
    ( validate (this field | <RelatedField>)
    | <RelatedField> = <RelatedValue>
    | refresh <RelatedField>
    | refresh dependent panels)
    …
    [when <SimpleCondition>]
```

Actions to perform when selection changes. RelatedField must be mutable on form or list.

## Examples

### Example 1: Simple Form Button

```
button of "Save"
    action is SAVE
    is primary

button of "Cancel"
    action is CANCEL
    display as link
```

### Example 2: Conditional Button

```
button of "Approve" with ApproveIcon
    action is APPROVE
    valid when Status = "Pending"
    visible when actor has role "Manager"
    is primary
    raise Warning alert when Amount > 10000
    mouse over text is "Approve this request"
```

### Example 3: Button with Multiple Actions

```
button of "Submit"
    action is SUBMIT
        when Status = "Draft"
    action is RESUBMIT
        when Status = "Rejected"
    is primary
```

### Example 4: Editor Button

```
button of "Edit Document" with EditIcon
    show editor for DocumentContent
    using mime type DocumentMimeType
```

### Example 5: Form Field with Label

```
CUSTOMERNAME
    label is "Customer Name"
    display as required
    mouse over text is "Enter the customer's full name"
```

### Example 6: Display-Only Field

```
BALANCE
    label is "Current Balance"
    display only
    display 2 decimals
    currency symbol is "$"
    display negative amount using parens
```

### Example 7: Field with Derived Value

```
TOTAL
    label is "Total Amount"
    derived value is Subtotal + Tax
    display 2 decimals
    currency symbol is "$"
    when value changed
        refresh GRANDTOTAL
```

### Example 8: Image Field

```
PRODUCTIMAGE
    label is "Product Image"
    display as portrait image up to 10 lines
    missing image is DefaultProductImage
    foreground text is "<ProductCode>"
```

### Example 9: Slider Field

```
PRIORITY
    label is "Priority Level"
    display as slider
    range is from 1 to 10
    step is 1
    Error alert range is from 8 to 10
    Warning alert range is from 5 to 7
    mouse over text is "Set priority level"
```

### Example 10: Field with Validation

```
EMAIL
    label is "Email Address"
    display as required
    keypad is email
    constraint Email matches "^[A-Za-z0-9+_.-]+@(.+)$"
        "Please enter a valid email address"
    when value changed
        validate this field
```

### Example 11: Two-Column Layout

```
Layout
    two column grid
        FIRSTNAME
        LASTNAME
        EMAIL
        PHONE
        ADDRESS
        CITY
        STATE
        ZIP
```

### Example 12: Section Layout

```
Layout
    section of PERSONALINFO visible when true
        label is "Personal Information"
        single column
            FIRSTNAME
            LASTNAME
            BIRTHDATE
    
    section of CONTACTINFO visible when true
        label is "Contact Information"
        two column
            EMAIL
            PHONE
            ADDRESS
            CITY
```

### Example 13: Multi-Select Field

```
multi-select field is CATEGORIES
    label is "Product Categories"
    display field is CategoryName
    select is CATEGORYLIST
    use 8 lines
    display as required
    when value changed
        refresh PRODUCTLIST
```

### Example 14: Conditional Layout

```
Layout
    visible when Status = "Active"
        two column
            FIELD1
            FIELD2
    
    visible when Status = "Inactive"
        single column
            FIELD3
            FIELD4
```

### Example 15: Button with Refresh

```
button of "Calculate"
    when button pressed
        TOTAL = QUANTITY * PRICE
        refresh TOTAL
        refresh dependent panels
```

## Best Practices

### Form Buttons

1. **Clear Labels**
   - Use action-oriented text
   - Keep labels concise
   - Use icons for common actions
   - Provide tooltips

2. **Primary Actions**
   - Mark primary action clearly
   - One primary button per context
   - Position prominently
   - Test with users

3. **Conditional Visibility**
   - Show relevant buttons only
   - Use valid/visible appropriately
   - Test all conditions
   - Provide feedback

4. **Button Placement**
   - Group related buttons
   - Follow platform conventions
   - Consider mobile layouts
   - Test accessibility

### Form Fields

1. **Labels**
   - Use clear, descriptive labels
   - Be consistent
   - Consider localization
   - Avoid jargon

2. **Display Modes**
   - Choose appropriate display type
   - Use read-only when appropriate
   - Consider mobile devices
   - Test with various data

3. **Validation**
   - Validate early
   - Provide clear messages
   - Use constraints appropriately
   - Test edge cases

4. **Dependencies**
   - Declare dependencies explicitly
   - Use derived values wisely
   - Refresh dependent fields
   - Test cascading changes

### Form Layouts

1. **Organization**
   - Group related fields
   - Use logical flow
   - Consider user workflow
   - Balance density

2. **Columns**
   - Use appropriate column count
   - Consider screen size
   - Test responsive behavior
   - Maintain alignment

3. **Sections**
   - Use sections for grouping
   - Provide clear labels
   - Use conditional visibility
   - Test collapsible sections

4. **Whitespace**
   - Use blank lines appropriately
   - Don't overcrowd
   - Balance information density
   - Test readability

### Multi-Select Fields

1. **Selection Lists**
   - Provide appropriate lists
   - Use display fields
   - Set reasonable line counts
   - Test with large datasets

2. **Behavior**
   - Handle value changes
   - Refresh dependent fields
   - Validate selections
   - Provide feedback

## Related Concepts

- [Forms](forms.md) - Form definitions
- [Lists](lists.md) - List definitions
- [Card Views](card-views.md) - Card-based displays
- [Actions](actions.md) - Action definitions
- [Field Definition](field-definition.md) - Field structure
- [Conditions](conditions.md) - Conditional logic
- [UI Definition](ui-definition.md) - UI components

## Summary

Form Base Definitions provide the building blocks for creating rich, interactive forms in LPL. Form buttons enable actions and navigation, form fields control data display and entry, layout directives organize components, sections provide grouping, and multi-select fields enable multiple value selection. Together, these components offer comprehensive control over form appearance, behavior, and user interaction.

**Key Components:**
- Form buttons for actions and navigation
- Form fields with extensive display options
- Layout directives for organization
- Section layouts for grouping
- Multi-select fields for multiple selections
- Conditional visibility and validation
- Derived values and dependencies
- Alerts and status indicators

**Best For:**
- Data entry forms
- Detail views
- Approval workflows
- Configuration screens
- Search forms
- Dashboard layouts
