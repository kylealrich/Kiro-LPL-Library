# StateCycles

## Overview

StateCycles define state machines for business objects, enabling workflow management, lifecycle tracking, and state-dependent behavior. They provide a structured way to model business processes with states, transitions, and state-specific rules and actions. StateCycles can be nested, allowing complex hierarchical state management.

## Syntax

```
<StateCycleName> is a StateCycle
    [state field is <persistent FieldName>]
    [initial state is <StateName>]
    <StateDefinition>
    …

StateDefinition ::=
    <StateName> is a State
        Entrance Rules
            <Rule>
            …
        Exit Rules
            <Rule>
            …
        Field Rules
            [(merge | replace) specifications]
            <FieldRules>
            …
        SubType <ConditionName> Field Rules
            [(merge | replace) specifications]
            <FieldRules>
            …
        [<EventHandler>…]
        [<ActionDefinition>…]
        [<StateCycleDefinition>…]
```

## Components

### StateCycle Definition

**StateCycle Name**
- Unique identifier for the state cycle
- Follows LPL naming conventions (uppercase alphanumeric)

**State Field**
- `state field is <persistent FieldName>` - Specifies which field stores the state
- Default: `<StateCycleName>State`
- Must be a persistent field

**Initial State**
- `initial state is <StateName>` - Specifies the starting state
- Default: First state definition
- Required for new records

### State Definition

Each state includes:
- **Entrance Rules** - Execute when entering the state
- **Exit Rules** - Execute when leaving the state
- **Field Rules** - State-specific field behavior
- **SubType Field Rules** - Conditional field rules within state
- **Event Handlers** - State-specific event handling
- **Actions** - State-specific actions
- **Nested StateCycles** - Sub-state machines

## State Naming and Addressing

### Uniqueness Rules
- StateNames must be unique within a parent State
- StateCycles do NOT participate in name scoping
- StateNames within different StateCycles at same level must be unique

### State Addressing
- **Fully Qualified**: `ParentState.ChildState.GrandchildState`
- **Partial**: Can omit levels if unambiguous
- **Example**: If `Open` state has `ApprovalCycle` with `NeedsApproval` state, address as `Open.NeedsApproval`

## Automatic Action Generation

### Default Behavior
- If no Actions defined: Create, Update, and Delete are automatically generated
- If any Actions defined: At least one must be a Create-type action

### Rationale
- Business objects must be creatable
- Update and Delete are optional
- Cannot have a business object that can never be created

## Use Cases

### Workflow Management
- Approval processes
- Document lifecycle
- Order processing
- Request handling

### Lifecycle Tracking
- Product lifecycle
- Project phases
- Employee status
- Contract stages

### State-Dependent Behavior
- Field validation by state
- Available actions by state
- Permissions by state
- Business rules by state

### Complex Processes
- Multi-stage approvals
- Nested workflows
- Parallel processes
- Conditional paths

## Examples

### Example 1: Simple Order StateCycle

```
ORDER_LIFECYCLE is a StateCycle
    state field is order.status
    initial state is DRAFT
    
    DRAFT is a State
        Entrance Rules
            editable = true
            submitted.date = null
        
        Exit Rules
            constraint total.amount > 0
                "Order must have a total amount"
        
        Field Rules
            CUSTOMER
                required "Customer required before submission"
        
        SUBMIT action
            make transition to SUBMITTED
    
    SUBMITTED is a State
        Entrance Rules
            submitted.date = today
            submitted.by = current.user
            editable = false
        
        Field Rules
            CUSTOMER
                cannot be changed
        
        APPROVE action
            make transition to APPROVED
        
        REJECT action
            make transition to REJECTED
    
    APPROVED is a State
        Entrance Rules
            approved.date = today
            approved.by = current.user
        
        FULFILL action
            make transition to FULFILLED
    
    FULFILLED is a State
        Entrance Rules
            fulfilled.date = today
        
        Exit Rules
            constraint false
                "Cannot leave fulfilled state"
    
    REJECTED is a State
        Entrance Rules
            rejected.date = today
            rejected.by = current.user
```

Simple order workflow with state transitions.

### Example 2: Nested StateCycles

```
DOCUMENT_LIFECYCLE is a StateCycle
    initial state is DRAFT
    
    DRAFT is a State
        Entrance Rules
            draft.start.date = today
        
        SUBMIT action
            make transition to REVIEW
    
    REVIEW is a State
        Entrance Rules
            review.start.date = today
        
        REVIEW_PROCESS is a StateCycle
            state field is review.status
            initial state is PENDING
            
            PENDING is a State
                ASSIGN_REVIEWER action
                    make transition to IN_REVIEW
            
            IN_REVIEW is a State
                Entrance Rules
                    reviewer = current.user
                    review.date = today
                
                APPROVE_REVIEW action
                    make transition to APPROVED
                
                REQUEST_CHANGES action
                    make transition to CHANGES_REQUESTED
            
            APPROVED is a State
                Entrance Rules
                    // Move parent to next state
                    make transition to APPROVED in DOCUMENT_LIFECYCLE
            
            CHANGES_REQUESTED is a State
                Entrance Rules
                    // Move parent back to draft
                    make transition to DRAFT in DOCUMENT_LIFECYCLE
    
    APPROVED is a State
        Entrance Rules
            approved.date = today
        
        PUBLISH action
            make transition to PUBLISHED
    
    PUBLISHED is a State
        Entrance Rules
            published.date = today
        
        Exit Rules
            constraint false
                "Cannot unpublish document"
```

Document workflow with nested review process.

### Example 3: State-Specific Field Rules

```
EMPLOYEE_STATUS is a StateCycle
    state field is employment.status
    initial state is APPLICANT
    
    APPLICANT is a State
        Field Rules
            HIRE_DATE
                cannot be entered "Hire date not applicable for applicants"
            
            SALARY
                cannot be entered "Salary not set for applicants"
        
        HIRE action
            make transition to ACTIVE
    
    ACTIVE is a State
        Entrance Rules
            hire.date = today
            status.change.date = today
        
        Field Rules
            HIRE_DATE
                cannot be changed "Hire date cannot be modified"
                required "Hire date required for active employees"
            
            SALARY
                required "Salary required for active employees"
        
        TERMINATE action
            make transition to TERMINATED
        
        SUSPEND action
            make transition to SUSPENDED
    
    SUSPENDED is a State
        Entrance Rules
            suspension.date = today
        
        Field Rules
            SUSPENSION_REASON
                required "Suspension reason required"
        
        REACTIVATE action
            make transition to ACTIVE
        
        TERMINATE action
            make transition to TERMINATED
    
    TERMINATED is a State
        Entrance Rules
            termination.date = today
        
        Field Rules
            TERMINATION_REASON
                required "Termination reason required"
        
        Exit Rules
            constraint false
                "Cannot change status after termination"
```

Employee status with state-specific field validation.

### Example 4: Conditional State Behavior

```
PROJECT_LIFECYCLE is a StateCycle
    initial state is PLANNING
    
    PLANNING is a State
        SubType project.type = "Internal" Field Rules
            BUDGET
                default to internal.default.budget
        
        SubType project.type = "External" Field Rules
            BUDGET
                required "Budget required for external projects"
            CLIENT
                required "Client required for external projects"
        
        START action
            make transition to ACTIVE
    
    ACTIVE is a State
        Entrance Rules
            start.date = today
        
        Field Rules
            START_DATE
                cannot be changed
        
        COMPLETE action
            make transition to COMPLETED
        
        CANCEL action
            make transition to CANCELLED
    
    COMPLETED is a State
        Entrance Rules
            completion.date = today
        
        Exit Rules
            constraint false
                "Cannot reopen completed project"
    
    CANCELLED is a State
        Entrance Rules
            cancellation.date = today
        
        Field Rules
            CANCELLATION_REASON
                required "Cancellation reason required"
```

Project lifecycle with conditional field rules.

### Example 5: Approval Workflow

```
APPROVAL_CYCLE is a StateCycle
    state field is approval.status
    initial state is PENDING
    
    PENDING is a State
        Entrance Rules
            submitted.for.approval = today
        
        Field Rules
            AMOUNT
                cannot be changed "Amount cannot be changed during approval"
        
        LEVEL1_APPROVE action
            if amount <= 1000
                make transition to APPROVED
            else
                make transition to LEVEL2_APPROVAL
        
        REJECT action
            make transition to REJECTED
    
    LEVEL2_APPROVAL is a State
        Entrance Rules
            level2.approval.date = today
        
        LEVEL2_APPROVE action
            if amount <= 10000
                make transition to APPROVED
            else
                make transition to LEVEL3_APPROVAL
        
        REJECT action
            make transition to REJECTED
    
    LEVEL3_APPROVAL is a State
        Entrance Rules
            level3.approval.date = today
        
        LEVEL3_APPROVE action
            make transition to APPROVED
        
        REJECT action
            make transition to REJECTED
    
    APPROVED is a State
        Entrance Rules
            final.approval.date = today
            final.approver = current.user
        
        Exit Rules
            constraint current.user.has.permission("RevokeApproval")
                "Only authorized users can revoke approval"
    
    REJECTED is a State
        Entrance Rules
            rejection.date = today
            rejected.by = current.user
        
        Field Rules
            REJECTION_REASON
                required "Rejection reason required"
```

Multi-level approval workflow.

### Example 6: State Entrance and Exit Rules

```
TICKET_LIFECYCLE is a StateCycle
    initial state is NEW
    
    NEW is a State
        Entrance Rules
            created.date = today
            status.color = "Blue"
        
        Exit Rules
            constraint assigned.to not empty
                "Ticket must be assigned before moving to next state"
        
        ASSIGN action
            make transition to ASSIGNED
    
    ASSIGNED is a State
        Entrance Rules
            assignment.date = today
            status.color = "Yellow"
            
            send notification
                to assigned.to
                description is "Ticket assigned to you"
        
        Exit Rules
            log "Ticket " + ticket.id + " leaving assigned state"
        
        START_WORK action
            make transition to IN_PROGRESS
    
    IN_PROGRESS is a State
        Entrance Rules
            start.date = today
            status.color = "Orange"
        
        Exit Rules
            if transitioning.to = RESOLVED
                constraint resolution.notes not empty
                    "Resolution notes required"
        
        RESOLVE action
            make transition to RESOLVED
        
        BLOCK action
            make transition to BLOCKED
    
    BLOCKED is a State
        Entrance Rules
            blocked.date = today
            status.color = "Red"
        
        Field Rules
            BLOCKING_REASON
                required "Blocking reason required"
        
        UNBLOCK action
            make transition to IN_PROGRESS
    
    RESOLVED is a State
        Entrance Rules
            resolution.date = today
            status.color = "Green"
            
            send notification
                to created.by
                description is "Your ticket has been resolved"
        
        CLOSE action
            make transition to CLOSED
        
        REOPEN action
            make transition to IN_PROGRESS
    
    CLOSED is a State
        Entrance Rules
            closed.date = today
            status.color = "Gray"
        
        Exit Rules
            constraint current.user.has.permission("ReopenClosed")
                "Only authorized users can reopen closed tickets"
```

Ticket lifecycle with entrance/exit rules and notifications.

## Best Practices

1. **Design Clear State Models**
   - Define distinct states
   - Model real business processes
   - Keep states meaningful
   - Avoid unnecessary complexity

2. **Use Entrance Rules**
   - Initialize state-specific fields
   - Set timestamps
   - Send notifications
   - Update related records

3. **Use Exit Rules**
   - Validate state transition requirements
   - Check prerequisites
   - Prevent invalid transitions
   - Log state changes

4. **Leverage State-Specific Field Rules**
   - Enforce state-dependent validation
   - Control field editability
   - Set state-specific defaults
   - Implement conditional behavior

5. **Define Appropriate Actions**
   - Provide clear transition paths
   - Name actions descriptively
   - Validate before transitions
   - Handle edge cases

6. **Handle Initial State**
   - Set appropriate initial state
   - Initialize required fields
   - Consider default values
   - Document initial state logic

7. **Document State Machines**
   - Diagram state transitions
   - Explain business rules
   - Note special cases
   - Provide examples

## Common Patterns

### Linear Workflow Pattern
```
STATECYCLE is a StateCycle
    STATE1 is a State
        ACTION1 action
            make transition to STATE2
    
    STATE2 is a State
        ACTION2 action
            make transition to STATE3
    
    STATE3 is a State
        // Terminal state
```

### Approval Pattern
```
APPROVAL is a StateCycle
    PENDING is a State
        APPROVE action
            make transition to APPROVED
        REJECT action
            make transition to REJECTED
    
    APPROVED is a State
    REJECTED is a State
```

### Lifecycle Pattern
```
LIFECYCLE is a StateCycle
    DRAFT is a State
        SUBMIT action
            make transition to ACTIVE
    
    ACTIVE is a State
        COMPLETE action
            make transition to COMPLETED
        CANCEL action
            make transition to CANCELLED
    
    COMPLETED is a State
    CANCELLED is a State
```

### Nested Workflow Pattern
```
PARENT_CYCLE is a StateCycle
    PARENT_STATE is a State
        CHILD_CYCLE is a StateCycle
            CHILD_STATE is a State
```

## State Transition

### Making Transitions

```
make transition to <FullStateName>
```

**Examples:**
- `make transition to APPROVED`
- `make transition to REVIEW.IN_PROGRESS`
- `make transition to Open.NeedsApproval`

### Transition Flow

1. **Exit Rules** - Execute on current state
2. **Transition** - State field updated
3. **Entrance Rules** - Execute on new state

## Troubleshooting

### Issue: Cannot transition to state

**Possible Causes:**
- Exit rules constraint failed
- Invalid state name
- State not accessible
- Permission denied

**Solutions:**
- Review exit rule constraints
- Verify state name
- Check state accessibility
- Verify permissions

### Issue: State field not updating

**Possible Causes:**
- Wrong state field specified
- Field not persistent
- Transaction rolled back
- Logic error

**Solutions:**
- Verify state field definition
- Ensure field is persistent
- Check for errors
- Review logic

### Issue: Entrance rules not firing

**Possible Causes:**
- Syntax error
- State not entered
- Transition failed
- Logic error

**Solutions:**
- Check syntax
- Verify transition occurred
- Review exit rules
- Test transition

### Issue: Ambiguous state reference

**Possible Causes:**
- Duplicate state names
- Unclear addressing
- Nested cycle confusion
- Name collision

**Solutions:**
- Use fully qualified names
- Ensure unique names within scope
- Review state hierarchy
- Clarify addressing

## Related Concepts

- [Actions and Actors](actions-actors.md) - Action definitions
- [Field Rules](field-rules.md) - Field-level rules
- [SubType Field Rules](subtype-field-rules.md) - Conditional field rules
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Conditions](conditions.md) - Boolean expressions

## Notes

- State definitions are optional
- If no actions defined, Create/Update/Delete are auto-generated
- At least one Create-type action required if any actions defined
- StateNames must be unique within parent State
- StateCycles do NOT participate in name scoping
- State addressing can be partial if unambiguous
- Default state field: `<StateCycleName>State`
- Default initial state: First state definition
- Entrance Rules execute when entering state
- Exit Rules execute when leaving state
- State-specific Field Rules override base rules
- Nested StateCycles enable complex workflows
- Merge/replace specifications valid only for LHF LPL
- Test state transitions thoroughly
- Document state machine diagrams
