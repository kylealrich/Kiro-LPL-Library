# Action Rules

## Overview

Action Rules define business logic that executes within the context of actions (Create, Update, Delete, etc.). They enable complex workflows, external integrations, document generation, notifications, and process automation.

## Action Rule Types

Action rules are valid in Action context or RulesField only and include:

- State transitions
- Transaction management
- Action invocations
- Workflow initiation
- Process automation
- Document generation
- Notifications and email
- ION BOD messaging
- Actor management
- Text search indexing

## State Transitions

```
make transition to <FullStateName>
```

Transitions the business object to a new state in a state cycle.

**Example:**
```
make transition to APPROVED.FINAL
```

## Transaction Management

```
commit transaction
```

Forces a commit in the middle of a transaction and starts a new transaction.

**Warning:**
- Can cause data integrity problems unless carefully managed
- On SetAction, schedules commit at next boundary (no integrity problems)

**Example:**
```
commit transaction
log "Checkpoint committed"
```

## Set Action Control

```
end set action instance loop
```

Valid in instance rules in SetAction only. Ends the instance action loop but continues to run Set Rules.

## Text Search

```
build text search field <FullFieldName>
    Fields
    (<RelatedValue> | [<RelatedLink>.]<FieldGroupName>)
    …
```

Concatenates specified fields into the text search field for full-text indexing.

**Example:**
```
build text search field SEARCH_TEXT
    Fields
    product.name
    product.description
    product.category.name
```

## Workflow Management

### Initiate Workflow

```
initiate <RelatedValue> workflow
    [resume on error]
        [<ResumeOnErrorRule>…]
    [Variables]
        [include [(persistent | auditable | user)] fields from <RelatedLink>]
        …
        [<RelatedField>…]
        [variable name is <LPLConstructName>]
    [assign message id to <FullFieldName>]
```

Initiates a workflow process. RelatedValue must be a WorkflowDefinitionCode.

**Example:**
```
initiate APPROVAL_WORKFLOW workflow
    Variables
        include persistent fields from this instance
        approval.level
        variable name is APPROVAL_LEVEL
    assign message id to workflow.message.id
```

### Cancel Workflow

```
cancel <RelatedValue> workflow
    message id is <FullFieldName>
```

Cancels an active workflow. RelatedValue must be a WorkflowDefinitionCode.

## Process Automation

### Initiate Process

```
( initiate <PFlowServiceName> process
| trigger <RelatedValue> PA service)
    [resume on error]
        [<ResumeOnErrorRule>…]
    [title is <Message>]
    [Criteria]
        <RelatedField>…
        [category filter is <RelatedField>]
        [variable name is <RelatedValue>]
    [Variables]
        [include [(persistent | auditable | user)] fields from <RelatedLink>]
        …
        [<RelatedField>…]
        [variable name is <LPLConstructName>]
    [URLs]
        (<HttpURL> | <LinkBack>)…
```

Initiates ProcessFlow service or triggers PA service. RelatedValue must be literal or PfiServiceDefinition key field.

**Example:**
```
initiate PURCHASE_APPROVAL process
    title is "Purchase Order Approval Required"
    Variables
        include persistent fields from this instance
        po.amount
        variable name is PO_AMOUNT
    URLs
        linkback "Approve" to APPROVE action
        linkback "Reject" to REJECT action
```

### Cancel Process

```
( cancel <PFlowServiceName> process
| cancel <RelatedValue> PA service)
```

Cancels an active process.

## Action Invocation

### Invoke Action

```
invoke <InvocationTarget>
    [in ( foreground
        | background [on <RelatedField>]
        | background group [<Parens>(<RelatedValue>)<Parens>])]
    [<InvokeRule>…]
    [<Rule>…]
    [<ControlRule>]
        (<InvokeRule> | <Rule>)
        …
```

Invokes another action, transaction, or interface.

**Execution Modes:**
- `foreground` - Synchronous execution (default)
- `background` - Asynchronous execution
- `background on <RelatedField>` - Scheduled background execution
- `background group` - Named background group for coordination

**Example:**
```
invoke CREATE.ORDER_LINE order.line set
    invoked.order.number = order.number
    invoked.product = each.product
    invoked.quantity = each.quantity
```

### Invoke Modify Update Stamp

```
invoke modify update stamp <RelatedLink>
```

Updates the update stamp of a related object without triggering full update logic.

### Invoke Method

```
invoke method <NativeMethod>
```

Invokes a native Java method.

### Invoke Script

```
invoke script <RelatedValue>
```

Invokes a user script. RelatedValue is a UserScript key field value.

### Invoke Derived Field

```
invoke derived field <RelatedValue>
```

Forces recalculation of a derived field.

## Invocation Targets

```
InvocationTarget ::= <InvocationAction>
    [ ( <RelatedLink>
      | <BusinessClass>
      | <BusinessTask>
      | <LawsonClassicTransaction>
      | <M3Interface>
      | <WebserviceInterface>
      | <StaticJavaPD>)]

InvocationAction ::= ( [<FullStateName>].<ActionName>
                     | audit create
                     | audit update
                     | change effective date
                     | insert history
                     | delete history
                     | change create date
                     | purge audit log entries)
```

## Invoke Rules

Rules specific to action invocation context.

### Run Outside Action Background Group

```
run outside of action background group
```

Executes action outside the implicit action background group, allowing top-level action to complete independently.

### Run After

```
run after ( <FullFieldName>
          | current action background group
          | action background group <FullFieldName>
          | background group <Parens><RelatedValue><Parens>)
```

Schedules action to run after specified action or group completes.

### On Error

```
on error
    invoke <InvocationTarget>
        [<Rule>…]
        [<ControlRule>]
            (<InvokeRule> | <Rule>)
            …
```

Defines error handling for background actions.

### Assign Identifiers

```
assign async action request id to <FullFieldName>
assign async [action] background group id to <FullFieldName>
```

Captures identifiers for background action tracking.

### Assign Result

```
assign result to (<KeyField> | <ViewField> | <BusinessObjectReference>)
```

Captures result of invoked action.

### Assign Message Group ID

```
assign message group id to <LawsonClassicMessageGroup KeyField>
```

Assigns message group identifier for classic transaction.

### Resume On Error

```
resume on error
    [<ResumeOnErrorRule>…]
```

Catches errors and executes specified rules.

**ResumeOnErrorRule:**
```
(<Rule> | cancel resume | <FullFieldName> = error message)
```

### Fill In Fields

```
fill in [blank] user fields from (parameters | <RelatedLink>)
fill in [blank] audit fields from <RelatedLink>
fill in [blank] fields from <RelatedLink>
    [except <InvokeRuleKeyword>.(<FullFieldName> | <FieldGroupName>)…]
```

Populates fields in invoked action from source.

### Field Assignment

```
<InvokeRuleKeyword>.<FullFieldName> <AssignmentOp> <RelatedValue>
```

**InvokeRuleKeyword:**
- `invoked` - References invoked action fields
- `datasource` - Valid only with DataSource pattern

### Initialize Fields

```
initialize <InvokeRuleKeyword>.<FullFieldName>…
```

Resets fields in invoked action.

### Result Assignment

```
<FullFieldName> <AssignmentOp> result.<FullFieldName>
```

Assigns result fields from classic transaction.

## Document Generation

```
generate document <RelatedValue>
    set using action <ActionName>
```

Generates document in background and uses specified action to set it on calling instance.

RelatedValue must be `<NavigationName> as pdf…`

**Example:**
```
generate document INVOICE_REPORT as pdf
    set using action SET_INVOICE_DOCUMENT
```

## Database Import

```
dbimport <RelatedField> into <BusinessClass>
```

Imports CSV data into business class. RelatedField must be of type CSVText.

Supports create, update, and delete operations (standard ReplicationSet format).

## ION BOD Messaging

```
send ion bod
    bod is <RelatedValue>
    bod type is <RelatedValue>
    [assign message id to <FullFieldName>]
    [(expect | no) acknowledgement]
    [accounting entity is <RelatedValue>]
    [location is <RelatedValue>]
    [document id is <RelatedValue>]
    [variation id is <RelatedValue>]
    [revision id is <RelatedValue>]
    [source is <RelatedValue>]
    [instance count is <RelatedValue>]
    [custom property is <RelatedValue>]
```

Sends ION Business Object Document.

**Requirements:**
- `bod is` - Must be XMLDocument
- `bod type is` - Format: `<verb>.<noun>`
- `assign message id to` - Must be IONOutboxQueue type
- `custom property is` - Must be IONCustomerProperty type

**Example:**
```
send ion bod
    bod is order.xml.document
    bod type is "Sync.SalesOrder"
    assign message id to ion.message.id
    expect acknowledgement
    accounting entity is company
    location is warehouse.location
```

## Notifications

```
send notification
    to <RelatedValue>
    description is <Message>
    [priority is (very low | low | medium | high | very high)]
    [detail is <Message>]
    [<LinkBack>]
    [navigation is <NavigationName>]
```

Sends notification to actor or actor group.

**Example:**
```
send notification
    to approval.manager
    description is "Purchase order requires approval"
    priority is high
    detail is "PO Amount: " + po.amount
    linkback "Review" to REVIEW action
```

## Email

```
send email
    [to <RelatedValue>…]
    [cc <RelatedValue>…]
    [bcc <RelatedValue>…]
    from <RelatedValue>…
    [ignore invalid addresses]
    [subject <Message>]
    [<Attachment>…]
    [Attachments]
        (<Attachment> | <ControlRule>)
        …
    [<Appointment>…]
    [Appointments]
        (<Appointment> | <ControlRule>)
        …
    Contents
        (<Message> | <ControlRule where <Rule> is replaced by <Message>>)
        …
```

Sends email with optional attachments and appointments.

**Requirements:**
- Must enter at least one to, cc, or bcc
- Email addresses cannot include spaces
- Can have multiple address lines
- Multiple addresses on one line use comma or semicolon delimiter

### Attachment

```
Attachment ::=
    attachment <RelatedField>
        [name is <RelatedValue>]
        [mime type is <RelatedValue>]
```

### Appointment

```
Appointment ::=
    appointment <RelatedValue>
        [send as (meeting | [appointment] message | [appointment] attachment)]
        [name is <RelatedValue>]
        [location is <RelatedValue>]
        [cancel]
        date [range] is <RelatedValue>
            [(to <RelatedValue> | for <RelatedValue> days | hours | minutes)]
        [reminder <RelatedValue> days | hours | minutes] before]
```

**Appointment Types:**
- `meeting` - Email attachment, sent to originator who sends invites
- `message` - Embedded in email (default), sent to each attendee
- `attachment` - Email attachment, sent to each attendee

**Example:**
```
send email
    to customer.email
    from system.email
    subject "Order Confirmation"
    Contents
        "Your order " + order.number + " has been confirmed."
        "Total: " + order.total
    Attachments
        attachment order.pdf.document
            name is "Order_" + order.number + ".pdf"
            mime type is "application/pdf"
```

## Actor Management

### Link Actor

```
link <RelatedValue> to this agent
```

Links an actor to the current agent. RelatedValue must be an Actor.

### Register New Actor

```
register new actor
    login name is <RelatedValue>
    password is <RelatedValue>
    webapp is <WebAppName>
    [registration key is <Message>]
    [role is <RelatedValue>…]
    [person name is <RelatedValue>]
    [contact info is <RelatedValue>]
```

Creates a new actor registration.

### Update Actor

```
update actor
    [person name is <RelatedValue>]
    [contact info is <RelatedValue>]
```

Updates actor information.

## Examples

### Example 1: Create Related Records

```
CREATE_ORDER
    for each order.line set
        invoke CREATE.ORDER_LINE
            invoked.order.number = order.number
            invoked.line.number = each.line.number
            invoked.product = each.product
            invoked.quantity = each.quantity
```

### Example 2: Background Processing

```
PROCESS_BATCH
    invoke PROCESS_ITEM item set in background
        invoked.batch.id = batch.id
        invoked.status = "Processing"
```

### Example 3: Workflow with Error Handling

```
SUBMIT_FOR_APPROVAL
    initiate APPROVAL_WORKFLOW workflow
        resume on error
            status = "Error"
            error.message = error message
        Variables
            include persistent fields from this instance
        assign message id to workflow.id
```

### Example 4: Send Notification

```
APPROVE_ORDER
    make transition to APPROVED
    send notification
        to order.requester
        description is "Your order has been approved"
        priority is medium
        linkback "View Order" to VIEW action
```

### Example 5: Generate and Email Document

```
SEND_INVOICE
    generate document INVOICE_REPORT as pdf
        set using action SET_INVOICE_PDF
    
    send email
        to customer.email
        from accounts.receivable.email
        subject "Invoice " + invoice.number
        Contents
            "Please find attached invoice " + invoice.number
        Attachments
            attachment invoice.pdf
                name is "Invoice_" + invoice.number + ".pdf"
```

### Example 6: Conditional Invocation

```
PROCESS_ORDER
    if order.total > 10000
        invoke MANAGER_APPROVAL
            invoked.order.number = order.number
    else
        invoke AUTO_APPROVE
            invoked.order.number = order.number
```

### Example 7: Background Group Coordination

```
BATCH_PROCESS
    for each item set
        invoke PROCESS_ITEM in background group (BATCH_GROUP)
            invoked.item.id = each.item.id
    
    invoke FINALIZE_BATCH in background
        run after background group (BATCH_GROUP)
        invoked.batch.id = batch.id
```

## Best Practices

1. **Error Handling**
   - Always use resume on error for background actions
   - Provide meaningful error messages
   - Log errors appropriately
   - Consider rollback scenarios

2. **Performance**
   - Use background processing for long-running operations
   - Batch related operations
   - Minimize transaction scope
   - Consider commit points carefully

3. **Notifications**
   - Provide clear, actionable messages
   - Include relevant context
   - Use appropriate priority levels
   - Test notification delivery

4. **Document Generation**
   - Generate in background when possible
   - Handle generation failures
   - Validate document content
   - Consider storage implications

5. **Workflow Integration**
   - Pass necessary context
   - Handle workflow errors
   - Track workflow status
   - Test workflow paths

## Related Concepts

- [Field Rules](field-rules.md) - Field-level rules
- [Actions and Actors](actions-actors.md) - Action definitions
- [Messages](messages.md) - User messages
- [Conditions](conditions.md) - Boolean expressions
- [Form Invokes](form-invokes.md) - Form automation

## Notes

- Action rules execute in action context
- Background actions run asynchronously
- Commit transaction can cause integrity issues if misused
- ION BOD requires proper message configuration
- Email addresses cannot contain spaces
- Workflow and process automation require proper setup
- Actor management requires appropriate permissions
- Text search fields must be properly indexed
