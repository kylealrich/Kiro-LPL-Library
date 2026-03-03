# UI Base Definitions

## Overview

UI Base Definitions provide the foundational constructs for building user interfaces in LPL. These definitions include controls, styles, icons, links, and other UI elements that can be used throughout form, list, and navigation definitions.

## Action References

```
Action ::= action is [<StateName>.]<ActionName>
```

References an action, optionally qualified by state.

## Action Icons

```
ActionIcon ::= ( add | apply | approve | award | calculate | chart | claim
               | copy | create | delete | edit | export | filled star
               | filter | finalize | open | outlined star | pdf | preview
               | print | process | reject | release | request | save
               | search | send | submit | transfer | undo | validate)
```

Predefined icons for common actions.

## Aggregation Drill Link

```
AggregationDrillLink ::= row[<Parens><SetName><Parens>][.<RelatedLink>]
```

Defines drill links for aggregated data rows.

## Alert Types

```
AlertType ::= ( red      // Highest precedence
              | yellow
              | green
              | blue     // Displayed if no higher alert
              | <Icon>)  // Displayed if no higher alert
```

Alert types shown in order of precedence.

## Called Out Actions

```
CalledOutAction ::=
    ([action is] ([<RelatedLink>.][<StateName>.]<ActionName> | <StandardAction>)
    | [row] form invoke is <FormInvokeName>
    | [row] navigation is <NavigationName>
    | [row] link is <UILink>)
    [action icon is (<ActionIcon> | <Icon> | hidden)]
    [label is <Message>]
    [mouse over text is <Message>]
    [hide when invalid]
    [valid when <SimpleCondition>]
```

Defines prominent actions displayed in UI.

**Components:**
- Action reference or standard action
- Form invoke for inline forms
- Navigation for drill paths
- Link for URLs
- Icon (required for form invokes)
- Label (required for form invokes)
- Mouse over text for tooltips
- Conditional visibility

## Captcha Control

```
CaptchaControl ::=
    captcha control
    [(valid | visible) when <SimpleCondition>]
    [(left align | center | right align)]
```

CAPTCHA verification control for security.

## Check Control

```
CheckControl ::=
    check control
    [(valid | visible) when <SimpleCondition>]
    [checked state is <FieldName>]
    [check action is [<StateName>.]<ActionName>]
        …
        [when <SimpleCondition>]
    [uncheck action is [<StateName>.]<ActionName>]
        …
        [when <SimpleCondition>]
    [display as (box | switch | star)]
    [<Label>]
    [mouse over text is <Message>]
    [(left align | center | right align)]
    [(top align | middle | bottom align)]
    [align as label]
    [indent]
    [hidden]
    [when value changed]
        ( validate (this field | <RelatedField>)
        | <RelatedField> = <RelatedValue>
        | refresh <RelatedField>
        | refresh dependent panels)
        …
        [when <SimpleCondition>]
```

Checkbox or toggle control.

**Display Types:**
- `box` - Standard checkbox (default)
- `switch` - Toggle switch
- `star` - Star rating

**Alignment:**
- Horizontal: left (default), center, right
- Vertical: top (default), middle, bottom
- Matrix forms: center is default

**Actions:**
- Check action - Executes when checked
- Uncheck action - Executes when unchecked
- Conditional actions with `when` clause

**Change Handling:**
- Validate fields
- Set field values
- Refresh fields or panels

## Colors

```
Color ::= ( black | blue | brown | gray | green | jade | magenta
          | maroon | orange | purple | red | turquoise | yellow)
```

Available colors for UI elements.

**Note:** jade, magenta, and maroon are deprecated.

## Display Types

```
DisplayType ::= ( browser[(/v3 | /v4+)]
                | desktop
                | smartphone[(/v3 | /v4+)]
                | tablet[(/v3 | /v4+)]
                | v3
                | v4+)
```

Device and version-specific display types.

## Field Styles

```
FieldStyle ::= ( header(1|2|3|4|5)
               | (left align | center | right align)
               | (top align | middle | bottom align)
               | bold
               | italics
               | color of <Color>)
```

Styling options for fields.

## Form References

```
Form ::= [independent] form is [<RelatedLink>.](<FormName> | primary | inline)
```

References a form, optionally independent or inline.

## Form Text

```
FormText ::=
    ( text of (<Message> [with <Icon>] | <Icon>)
      [<TextStyle>…]
      [link is <UILink>]
      [display as tag]
      [color of <Color> [when <SimpleCondition>]…]
      [align as label]
    | blank line)
```

Static text or blank lines in forms.

**Features:**
- Text with optional icon
- Text styling
- Clickable links
- Tag display style
- Conditional coloring
- Label alignment

## Headers

```
Header ::=
    header(1|2|3|4|5) of <Message>
    [(show border | underline) [color of <Color>]]
    [color of <Color>]
    [shade]
    [(center | right align)]
    [collapsible]
    [initially (collapsed | expanded)]
```

Section headers with various styles.

**Levels:** 1 (largest) through 5 (smallest)

**Styling:**
- Border or underline
- Color
- Shading
- Alignment

**Collapsible:**
- Can be collapsed/expanded
- Initial state: expanded (default) or collapsed

## HTTP URLs

```
HttpURL ::= <Message>
```

HTTP/HTTPS URL as a message.

## Icons

```
Icon ::= icon.<IconName>

IconName ::= <Literal>
```

Icon reference using icon name.

**Icon Files:**
- Location: `<LASRCDIR>/<ProductLine>/icons`
- PNG format: `<ImageName>_<Size>.png` (e.g., `icon_16.png`, `icon_32.png`)
- SVG format: `<ImageName>.svg`
- PNG files must be square
- Size denotes pixel dimensions (16, 32, 72, etc.)

**SOHO Icons:**
Action icons can also use SOHO icons: https://design.infor.com/product/identity/icons

## Images

```
ImageName ::= (<Literal> | random background)
```

Image reference using image name.

**Image Files:**
- Location: `<LASRCDIR>/<ProductLine>/images`
- PNG format: `<ImageName>.png`
- SVG format: `<ImageName>.svg`
- `random background` - Uses random background image

## Labels

```
Label ::=
    label is (<Message> | default)
    [<TextStyle>…]
```

Field or control label with optional styling.

## List References

```
List ::=
    list is (<RelatedLink> | <AggregationDrillLink>).(<ListName> | primary [audit list])
    [search <FullFieldName> using <RelatedValue>…]
    [cannot be empty]
    [when <SimpleCondition>]
    [<Message>]
    [confirmation required on empty]
    [when <SimpleCondition>]
    [<Message>]
    [form is (<FormName> | primary | inline)]
    …
    [<FormDefinition>]
    [current period is <RelatedValue>]
    [current year is <RelatedValue>]
    [Row Dimensions]
        <DimensionField>
        …
    [filter list is <RelatedLink>.(<ListName> | primary)…]
    [valid when <SimpleCondition>]
    [group label is <RelatedValue>]
    filter <RelatedField> using <RelatedValue>
    …
    [implements DragAndDrop]
    action is [<StateName>.]<ActionName>
        [invoked.<FullFieldName> = [drop target.]<RelatedValue>…]
    [helper list is <RelatedLink>.(<ListName> | primary)…]
    [valid when <SimpleCondition>]
    [title is <Message>]
    [search <FullFieldName> using <RelatedValue>…]
    [<Action>]
    [invoked.<FullFieldName> = (<RelatedValue> | parameter)…]
```

Comprehensive list definition with many options.

**Features:**
- Search functionality
- Empty validation (wizard context)
- Associated forms
- Period/year context (cube views)
- Row dimensions (cube views)
- Filter lists
- Drag and drop
- Helper lists

**Helper Lists:**
- Display below main list
- Useful for copying instances
- Useful for reference data
- Can specify actions
- Parameters filled by context or explicitly

## Manual Representation

```
ManualRepresentation ::=
    manual representation
    representation name is <Literal>
    [(label is (<RelatedField> | <Message> | number) | no label)]
    [<TextStyle>…]
    [align as label]
    [show up to <Literal> lines]
    [show up to <Literal> characters]
    Property Mapping
        <MessageProperty> = <RelatedValue>
        …
    [raise <AlertType> alert when <SimpleCondition>…]
    [mouse over text is <Message>]
    [link is <UILink>]
```

Custom field representation with property mapping.

## Navigation References

```
Navigation ::= navigation is <NavigationName>
```

References a navigation definition.

## Page References

```
Page ::= page is <PageName>[.<PanelName>]
```

References a page, optionally with panel.

## Simple Conditions

```
SimpleCondition ::= <Parens> ( <RelatedField>
                              | <RelatedField>.<StateName>
                              | <FieldCondition>
                              | <ActorCondition>
                              | display type.<DisplayType>) <Parens>
```

Boolean conditions for UI logic.

**Types:**
- Field value (must be Boolean)
- State check
- Field condition (only 'entered' allowed)
- Actor condition
- Display type check

## Standard Actions

```
StandardAction ::= ( open
                   | save [and (new | close)]
                   | inline create
                   | create within
                   | create menu
                   | export to (pdf | csv)
                   | search
                   | saved searches
                   | drill around
                   | refresh
                   | charts
                   | helper list
                   | next record
                   | previous record
                   | copy row
                   | audit compare
                   | sort
                   | download)
```

Built-in standard actions.

## Status Icons

```
StatusIcon ::= ( approved | attached | complete | private | public | rejected)
```

Predefined status indicator icons.

## Templates

```
Template ::=
    template is <RelatedLink>
    [implements DynamicChildVisibility]
    child designator field is <FullFieldName>
    parent match field is <FullFieldName>
    [target] business class is <BusinessClass>
    ( <Form>
      [<FormDefinition>]
    | list is (<ListName> | primary))
```

Template-driven UI for creating instances.

**Purpose:**
- Build list of template instances
- Create target business class instances
- Business class must implement TemplateDriven pattern
- Related link must be template business class

**Dynamic Child Visibility:**
- Children visible based on field values
- Child designator field on template class
- Parent match field on target class
- Children visible when values match

## Text Styles

```
TextStyle ::= ( header(1|2|3|4|5)
              | (left align | center | right align)
              | (top align | middle | bottom align)
              | bold
              | italics
              | bullet
              | left of field
              | right of field
              | color of <Color>)
```

Text styling options.

**Note:** Text styles do not affect fields in List or AuditList definitions.

## UI Links

```
UILink ::= ( [<RelatedLink>.]<NavigationName>
           | [(embedded | external | dialogue)] <HttpURL>
             [lazy url retrieval]
             [mingle url is <Message>]
           | registration
           | signin
           | signout)
```

Links to navigations, URLs, or authentication pages.

**URL Types:**
- Default: Replaces entire frame
- `embedded`: Opens within application frame
- `external`: Opens in external browser or new tab
- `dialogue`: Opens in dialog (browser only)

**Mingle URL:**
- Used if Mingle is installed
- Format: `<logicalId>/<restofurl>`

**Lazy URL Retrieval:**
- URL retrieved when needed, not immediately

## URL References

```
URL ::= url is <HttpURL>
```

URL specification.

## Best Practices

1. **Use Appropriate Icons**
   - Choose meaningful action icons
   - Use standard icons for common actions
   - Provide custom icons for specific needs
   - Ensure icons are square and properly sized

2. **Apply Consistent Styling**
   - Use headers hierarchically (1-5)
   - Apply colors consistently
   - Align elements appropriately
   - Use text styles purposefully

3. **Design Intuitive Controls**
   - Use check controls appropriately
   - Provide clear labels
   - Add mouse over text for guidance
   - Handle value changes properly

4. **Implement Effective Links**
   - Choose appropriate link types
   - Use embedded for in-app navigation
   - Use external for external resources
   - Provide clear link text

5. **Leverage Conditional Display**
   - Show/hide based on conditions
   - Use simple conditions
   - Test all condition paths
   - Provide appropriate defaults

6. **Optimize Lists**
   - Use helper lists effectively
   - Implement search when needed
   - Configure filters appropriately
   - Enable drag-and-drop when useful

7. **Design Accessible UIs**
   - Provide clear labels
   - Use appropriate colors
   - Ensure sufficient contrast
   - Support keyboard navigation

## Related Concepts

- [UI Definition](ui-definition.md) - Overall UI structure
- [Matrix Forms](matrix-forms.md) - Grid-based forms
- [Form Invokes](form-invokes.md) - Form automation
- [Messages](messages.md) - User messages
- [Actions](actions.md) - Action definitions

## Notes

- Icon files must be in `<LASRCDIR>/<ProductLine>/icons` directory
- Image files must be in `<LASRCDIR>/<ProductLine>/images` directory
- PNG icon files must be square
- Action icons can use SOHO icons
- Alert types display in order of precedence (red, yellow, green, blue)
- Text styles don't affect List or AuditList fields
- Check control alignment defaults differ in matrix forms (center vs left)
- Helper lists display below main list
- Template-driven UI requires TemplateDriven pattern implementation
- Simple conditions must result in Boolean values
- Standard actions are built-in and don't require definition
- UI links support various navigation and URL types
- Collapsible headers default to expanded state
- Colors jade, magenta, and maroon are deprecated
