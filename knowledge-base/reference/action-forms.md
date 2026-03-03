# Action Forms

## Overview

Action Forms are specialized forms used to format and present action parameters. They provide a focused interface for executing actions with parameters, displaying only the necessary fields for the action. Action Forms are modal by default, blocking interaction with other forms until dismissed, and include standard OK and Cancel buttons.

## Basic Syntax

```
<FormName> is an ActionForm
    [use for <DisplayType>]
    action is [<StateName>.]<ActionName>
    …
    [on completion navigate to <NavigationName>]
    [suppress completion message]
    [when <SimpleCondition>]
    [allow non-modal interaction]
    [title is <Message>]
    Layout
        (<FormLayout> | <SectionLayout>)
        …
```

## Definition

```
ActionFormDefinition ::=
    <FormName> is an ActionForm
        [use for <DisplayType>]
        action is [<StateName>.]<ActionName>
        …
        [on completion navigate to <NavigationName>]
        [suppress completion message]
        [when <SimpleCondition>]
        [allow non-modal interaction]
        [title is <Message>]
        Layout
            (<FormLayout> | <SectionLayout>)
            …
```

## Purpose

Action Forms are used to:
- Format parameters on an action
- Provide focused interface for action execution
- Display only relevant fields for the action
- Handle both parameter-based and non-parameter-based actions

## Field Scoping

### With Parameters

When an ActionForm is specified and the action has parameters:
- Field scoping is **limited to the parameters**
- Only parameter fields are accessible
- Provides focused parameter entry

### Without Parameters

When no parameters are defined on the action:
- If action is a **Create** or **Update** action:
  - Field scoping is the **whole business class**
  - All business class fields are accessible
- Provides full field access for create/update operations

## Action Form Behavior

### Standard Buttons

Action Forms automatically include:
- **OK button**: Runs the specified action
- **Cancel button**: Returns to the previous page

These buttons are provided by default and don't need to be explicitly defined.

### Modal Interaction

By default, Action Forms are **modal**:
- Block interaction on other forms
- User must dismiss the form to continue
- Ensures focused action completion

## Attributes

### Use For

```
[use for <DisplayType>]
```

Specifies when this action form should be used.

**Example:**
```
use for Create
use for Update
```

### Action

```
action is [<StateName>.]<ActionName>
…
```

Specifies the action(s) this form executes.

**Multiple Actions:**
Multiple actions can be defined as long as the parameters are the same.

**Examples:**
```
action is APPROVE
action is PENDING.SUBMIT
action is APPROVE
action is FASTAPPROVE
```

### On Completion Navigate

```
[on completion navigate to <NavigationName>]
```

Specifies where to navigate after successful action completion.

**Use Cases:**
- Navigate to detail view after creation
- Return to list after update
- Navigate to dashboard after approval
- Custom navigation flow

### Suppress Completion Message

```
[suppress completion message]
```

Suppresses the default completion message that displays after action execution.

**When to Use:**
- Custom navigation handles feedback
- Silent operations
- Batch processing
- Integrated workflows

### Conditional Action Form

```
[when <SimpleCondition>]
```

Makes the action form conditional based on a simple condition.

**Example:**
```
when Status = "Pending"
when actor has role "Manager"
```

### Allow Non-Modal Interaction

```
[allow non-modal interaction]
```

Action Forms by default are modal (block interaction on other forms until dismissed). This attribute allows non-modal interaction.

**When to Use:**
- Multi-tasking scenarios
- Reference information needed
- Parallel workflows
- Advanced user interfaces

### Title

```
[title is <Message>]
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `title is <Message>` | Form title | FormName |

The title can include field references and expressions for dynamic titles.

**Example:**
```
title is "Approve Order <Order>"
title is "Submit for Approval"
```

## Layout

```
Layout
    (<FormLayout> | <SectionLayout>)
    …
```

Defines the visual arrangement of fields and components on the action form.

**Layout Options:**
- Standard form layouts
- Section layouts for grouping
- All form layout directives available
- Form fields, buttons, text, controls

## Examples

### Example 1: Simple Approval Form

```
APPROVEFORM is an ActionForm
    action is APPROVE
    title is "Approve Request"
    
    Layout
        single column
            APPROVALCOMMENTS
                label is "Approval Comments"
                use text area of 5 lines
            APPROVALDATE
                label is "Approval Date"
                initial value is current date
```

### Example 2: Action Form with Navigation

```
CREATEORDERFORM is an ActionForm
    use for Create
    action is CREATE
    title is "Create New Order"
    on completion navigate to ORDERDETAIL
    
    Layout
        two column
            CUSTOMER
                label is "Customer"
                display as required
            ORDERDATE
                label is "Order Date"
                initial value is current date
                display as required
            SHIPTOADDRESS
                label is "Ship To Address"
            BILLTOADDRESS
                label is "Bill To Address"
```

### Example 3: Multiple Actions with Same Parameters

```
APPROVALFORM is an ActionForm
    action is APPROVE
    action is FASTAPPROVE
    title is "Approval Form"
    
    Layout
        single column
            APPROVALCOMMENTS
                label is "Comments"
                use text area of 5 lines
                display as required
            APPROVALDATE
                label is "Date"
                initial value is current date
```

### Example 4: Conditional Action Form

```
MANAGERREVIEW is an ActionForm
    action is REVIEW
    when actor has role "Manager"
    title is "Manager Review"
    
    Layout
        single column
            REVIEWCOMMENTS
                label is "Review Comments"
                use text area of 8 lines
            REVIEWDATE
                label is "Review Date"
                initial value is current date
            RECOMMENDATION
                label is "Recommendation"
                display as required
```

### Example 5: Non-Modal Action Form

```
QUICKNOTE is an ActionForm
    action is ADDNOTE
    title is "Add Quick Note"
    allow non-modal interaction
    suppress completion message
    
    Layout
        single column
            NOTETEXT
                label is "Note"
                use text area of 3 lines
            NOTEDATE
                label is "Date"
                initial value is current date
                display only
```

### Example 6: Action Form with Sections

```
SUBMITREQUEST is an ActionForm
    action is SUBMIT
    title is "Submit Request"
    on completion navigate to REQUESTLIST
    
    Layout
        section of REQUESTINFO
            label is "Request Information"
            two column
                REQUESTTYPE
                    label is "Request Type"
                    display as required
                PRIORITY
                    label is "Priority"
                    display as required
                REQUESTDATE
                    label is "Request Date"
                    initial value is current date
                DUEDATE
                    label is "Due Date"
        
        section of DETAILS
            label is "Details"
            single column
                DESCRIPTION
                    label is "Description"
                    use text area of 10 lines
                    display as required
                JUSTIFICATION
                    label is "Justification"
                    use text area of 5 lines
```

### Example 7: Update Action Form

```
UPDATESTATUS is an ActionForm
    use for Update
    action is UPDATESTATUS
    title is "Update Status"
    suppress completion message
    
    Layout
        single column
            STATUS
                label is "New Status"
                display as required
            STATUSDATE
                label is "Status Date"
                initial value is current date
            STATUSCOMMENTS
                label is "Comments"
                use text area of 5 lines
```

### Example 8: Rejection Form

```
REJECTFORM is an ActionForm
    action is REJECT
    title is "Reject Request"
    on completion navigate to PENDINGLIST
    
    Layout
        single column
            REJECTIONREASON
                label is "Rejection Reason"
                display as required
                use text area of 8 lines
            REJECTIONDATE
                label is "Rejection Date"
                initial value is current date
                display only
            ALTERNATIVEOPTIONS
                label is "Alternative Options"
                use text area of 5 lines
```

### Example 9: Batch Action Form

```
BATCHPROCESS is an ActionForm
    action is PROCESSBATCH
    title is "Process Batch"
    suppress completion message
    on completion navigate to BATCHRESULTS
    
    Layout
        two column
            BATCHDATE
                label is "Batch Date"
                initial value is current date
            BATCHTYPE
                label is "Batch Type"
                display as required
            PROCESSOPTIONS
                label is "Processing Options"
            NOTIFYONCOMPLETION
                label is "Notify on Completion"
                display as switch
```

### Example 10: Wizard-Style Action Form

```
SETUPWIZARD is an ActionForm
    action is SETUP
    title is "Setup Wizard"
    on completion navigate to DASHBOARD
    
    Layout
        section of STEP1
            label is "Step 1: Basic Information"
            two column
                COMPANYNAME
                    label is "Company Name"
                    display as required
                INDUSTRY
                    label is "Industry"
                    display as required
        
        section of STEP2
            label is "Step 2: Configuration"
            two column
                TIMEZONE
                    label is "Time Zone"
                    display as required
                CURRENCY
                    label is "Currency"
                    display as required
        
        section of STEP3
            label is "Step 3: Preferences"
            single column
                EMAILNOTIFICATIONS
                    label is "Email Notifications"
                    display as switch
                AUTOBACKUP
                    label is "Automatic Backup"
                    display as switch
```

## Use Cases

### Parameter Entry
- Collecting action parameters
- Focused data entry
- Required field validation
- Parameter-specific layouts

### Approval Workflows
- Approval/rejection forms
- Review forms
- Sign-off forms
- Authorization forms

### Create Operations
- Simplified creation forms
- Quick entry forms
- Wizard-style creation
- Template-based creation

### Update Operations
- Status updates
- Bulk updates
- Field-specific updates
- Conditional updates

### Batch Processing
- Batch parameter entry
- Processing options
- Scheduling parameters
- Notification settings

## Best Practices

### Form Design

1. **Focus on Parameters**
   - Show only necessary fields
   - Group related parameters
   - Use clear labels
   - Provide helpful defaults

2. **Layout Organization**
   - Use logical field order
   - Group with sections
   - Balance density
   - Consider mobile devices

3. **Validation**
   - Mark required fields
   - Validate early
   - Provide clear messages
   - Test edge cases

### Action Configuration

1. **Single Responsibility**
   - One action per form (or related actions)
   - Clear purpose
   - Focused functionality
   - Avoid complexity

2. **Multiple Actions**
   - Use only when parameters match
   - Document differences
   - Test all actions
   - Consider user confusion

### Navigation

1. **Completion Navigation**
   - Navigate to logical destination
   - Consider user workflow
   - Provide context
   - Test navigation paths

2. **Cancel Behavior**
   - Return to previous page
   - Confirm if needed
   - Don't lose data
   - Provide feedback

### Modal vs Non-Modal

1. **Use Modal (Default) When:**
   - Action requires focus
   - Blocking is appropriate
   - Simple workflows
   - Standard operations

2. **Use Non-Modal When:**
   - Reference needed
   - Multi-tasking required
   - Advanced workflows
   - Power user features

### Messages

1. **Completion Messages**
   - Provide by default
   - Suppress when navigating
   - Make messages clear
   - Consider localization

2. **Titles**
   - Use descriptive titles
   - Include context
   - Keep concise
   - Use dynamic content

### Performance

1. **Optimization**
   - Minimize field count
   - Use derived values
   - Lazy load when possible
   - Test with realistic data

2. **Responsiveness**
   - Fast form display
   - Quick validation
   - Efficient action execution
   - Provide feedback

## Common Patterns

### Approval Pattern

```
<ActionName>FORM is an ActionForm
    action is <ActionName>
    title is "<Action> <Entity>"
    
    Layout
        single column
            COMMENTS
                label is "Comments"
                use text area of 5 lines
            DATE
                label is "Date"
                initial value is current date
```

### Create Pattern

```
CREATE<EntityName>FORM is an ActionForm
    use for Create
    action is CREATE
    title is "Create <Entity>"
    on completion navigate to <EntityDetail>
    
    Layout
        two column
            <required parameters>
```

### Update Pattern

```
UPDATE<EntityName>FORM is an ActionForm
    use for Update
    action is UPDATE
    title is "Update <Entity>"
    suppress completion message
    
    Layout
        single column
            <update parameters>
```

### Batch Processing Pattern

```
BATCH<ProcessName>FORM is an ActionForm
    action is BATCH<ProcessName>
    title is "Batch <Process>"
    suppress completion message
    on completion navigate to RESULTS
    
    Layout
        two column
            <batch parameters>
```

## Integration with Actions

Action Forms are tightly integrated with action definitions:

### Action with Parameters

```
APPROVE is an Action
    Parameters
        APPROVALCOMMENTS is Alpha size 500
        APPROVALDATE is Date

APPROVEFORM is an ActionForm
    action is APPROVE
    title is "Approve"
    
    Layout
        single column
            APPROVALCOMMENTS
            APPROVALDATE
```

### Action without Parameters

```
SUBMIT is a Create Action

SUBMITFORM is an ActionForm
    use for Create
    action is SUBMIT
    title is "Submit Request"
    
    Layout
        two column
            <all business class fields available>
```

## Comparison with Standard Forms

| Feature | Action Form | Standard Form |
|---------|-------------|---------------|
| Purpose | Execute actions | Display/edit data |
| Buttons | OK/Cancel (automatic) | Custom buttons |
| Field Scope | Parameters or full class | Full class |
| Modal | Default modal | Not modal |
| Navigation | On completion | Manual |
| Use Case | Action execution | General data entry |

## Troubleshooting

### Issue: Fields Not Available

**Possible Causes:**
- Action has parameters (only parameters available)
- Field not in parameter list
- Incorrect field reference
- Scoping issue

**Solutions:**
- Add field to action parameters
- Use correct field names
- Check action definition
- Review field scoping rules

### Issue: Multiple Actions Not Working

**Possible Causes:**
- Parameters don't match
- Different parameter types
- Parameter order differs
- Action definitions inconsistent

**Solutions:**
- Ensure identical parameters
- Match parameter types exactly
- Use same parameter order
- Review action definitions

### Issue: Navigation Not Working

**Possible Causes:**
- Navigation not defined
- Navigation name incorrect
- Navigation not accessible
- Permission issues

**Solutions:**
- Define navigation
- Verify navigation name
- Check navigation definition
- Review permissions

### Issue: Form Not Modal

**Possible Causes:**
- Non-modal interaction enabled
- Configuration override
- Platform behavior
- Browser issues

**Solutions:**
- Remove non-modal attribute
- Check configuration
- Test in different browsers
- Review platform settings

### Issue: Completion Message Not Showing

**Possible Causes:**
- Message suppressed
- Navigation overrides message
- Action doesn't define message
- Display issue

**Solutions:**
- Remove suppress attribute
- Check action definition
- Review navigation
- Test message display

## Related Concepts

- [Forms](forms.md) - Standard form definitions
- [Actions](actions.md) - Action definitions
- [Form Base Definitions](form-base-definitions.md) - Form components
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Field Definition](field-definition.md) - Field structure
- [Navigations](navigations.md) - Navigation definitions
- [UI Definition](ui-definition.md) - UI components

## Summary

Action Forms provide a specialized, focused interface for executing actions with parameters. They automatically handle field scoping, provide standard OK/Cancel buttons, and support modal interaction by default. With features like completion navigation, message suppression, and conditional display, Action Forms streamline action execution workflows while maintaining a clean, purpose-built user interface.

**Key Features:**
- Automatic field scoping (parameters or full class)
- Standard OK/Cancel buttons
- Modal by default
- Completion navigation
- Message suppression
- Conditional display
- Multiple action support
- Section layouts

**Best For:**
- Action parameter entry
- Approval workflows
- Create/update operations
- Batch processing
- Focused action execution
- Wizard-style processes
- Quick entry forms

**Advantages:**
- Simplified interface
- Focused functionality
- Automatic button handling
- Clear user flow
- Reduced complexity
- Consistent behavior
