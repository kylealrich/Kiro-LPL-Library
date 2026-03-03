# Actions and Actors

## Action Attributes

```
ActionAttribute ::= ( <Actor>
                    | action
                    | action comment
                    | action type[.(Create | Update | Delete | Unknown)]
                    | action tag
                    | applied stamp
                    | audit entry id
                    | audit period[.(Past | Current | Future)]
                    | correction
                    | correction comment
                    | effective date
                    | effective time zone
                    | effective through
                    | effective stamp
                    | entry stamp
                    | initiating action
                    | invoking action
                    | reason code
                    | subject
                    | system stamp
                    | action request id
                    | changed field names
                    | changed fields
                    | purge date
                    | audit transaction id
                    | session
                    | server identity
                    | remote identity)
```

### Action Attribute Details

Action attributes either reference the currently executed action or an audited action. An audited action can be addressed using `for each audit log records`. Within this for each, the `each` keyword can be used in conjunction with these attributes.

Examples:
- `each.action` - Returns the name of the past (or future) action
- `each.effective date` - Returns the effective date of that past action

### Attribute Descriptions

| Attribute | Description |
|-----------|-------------|
| `action` | The action name |
| `action comment` | Comment associated with the action |
| `action type` | Type of action (Create, Update, Delete, Unknown) |
| `action type.Create` | True if action type is Create |
| `action type.Update` | True if action type is Update |
| `action type.Delete` | True if action type is Delete |
| `action type.Unknown` | True if action type is Unknown |
| `action tag` | Designer defined 'tag' |
| `applied stamp` | Logical timestamp of when record actually changed / action was 'applied' |
| `audit entry id` | If active entry – the id of the entry |
| `audit period` | Period of the audit entry |
| `audit period.Past` | True if audit period is Past |
| `audit period.Current` | True if audit period is Current |
| `audit period.Future` | True if audit period is Future |
| `correction` | True if active entry is a correction |
| `correction comment` | If correction – the comment |
| `effective date` | Date when the action becomes effective |
| `effective time zone` | Time zone for effective date (default is server time zone) |
| `effective through` | If new entry correction – the effective through date |
| `effective stamp` | Effective timestamp |
| `entry stamp` | Logical timestamp of change 'request' |
| `initiating action` | The action that initiated this action |
| `invoking action` | Action immediately preceding this one in the invoke chain |
| `reason code` | Reason code for the action |
| `subject` | Subject of the action |
| `system stamp` | Physical timestamp of action |
| `action request id` | ID of the action request |
| `changed field names` | Comma separated list of changed field names |
| `changed fields` | List of changed fields (valid only in 'for each' rule) |
| `purge date` | Date all data was purged before |
| `audit transaction id` | ID that ties all audit log entries done in a transaction |
| `session` | Currently not in use |
| `server identity` | Set of IP addresses for the server that ran the action |
| `remote identity` | IP address for the remote client that ran the action |

## Action Tag Value

```
ActionTagValue ::= action tag.<ActionTag>
```

Allows reference to a specific action tag value, which are defined in the Product Line Definition file.

## Actor

```
Actor ::= [(authenticated | agent)] actor[ (<ActorAttribute>
                                           | context.<FieldName>
                                           | context.<SecurityClaim>)]
```

### Actor Variations

- **actor**: By itself, is the current actor which is represented by the Actor KeyField in the environment product line
- **authenticated actor**: Useful in an Actor Proxy situation
- **agent actor**: The actor that this Agent Stereotype is linked to
- **actor context.\<FieldName\>**: Any KeyField within this product line
  - Actors can have any keyfield within the system defined as a context field with a specific value
  - Example: `Company = 1` for actor `ApClerk1`
  - If used when doing a `for each audit log records`, then it refers to the actor of the particular audit log action

## Actor Attributes

```
ActorAttribute ::= ( <actor FieldName>
                   | initiator
                   | approver
                   | final approver)
```

### Actor Attribute Details

| Attribute | Description |
|-----------|-------------|
| `<actor FieldName>` | Field on Actor.busclass in environment product line |
| `initiator` | Returns true if the actor is the initiator of a RequestAction |
| `approver` | Returns true if the actor is an approver of a RequestAction |
| `final approver` | Returns true if the actor is a 'final approver' of a RequestAction |

## Agent

```
Agent ::= [[authenticated] actor.]agent[<Parens><BusinessClass><Parens>]
```

If this business class is an Agent, another agent that has been linked to this agent can be addressed with the `agent(<BusinessClass>)` designation. 

To access agents that the current actor has been linked with, add the `actor` keyword in front of the `agent` keyword.

### Examples

- `agent(SupplierAgent)` - Access a specific agent type
- `actor.agent(SupplierAgent)` - Access agents linked to the current actor

## LinkBack

```
LinkBack ::= linkback<Parens>
             webapp is <WebAppName>
             navigation is <NavigationName>
             [allow anonymous access]
             [show form only]
             [text is <Message>]
             [session key <KeyField> is <RelatedValue>…]
             <Parens>
```

The linkback statement is a single line statement. These options have been placed on separate lines in the BNF for readability.

### LinkBack Options

| Option | Description |
|--------|-------------|
| `webapp is <WebAppName>` | Specifies the web application |
| `navigation is <NavigationName>` | Specifies the navigation target |
| `allow anonymous access` | Allows access without authentication |
| `show form only` | Suppresses display of the header and navigation bar |
| `text is <Message>` | Display text for the link |
| `session key <KeyField> is <RelatedValue>` | Sets session key values |

### LinkBack Behavior

This will result in a full HTTP URL link that will bring up the designated navigation. 

- If a `text is` message is defined, then the linkback will be an HTML link
- Otherwise, it will be a simple URL

### Example

```
linkback(webapp is MyWebApp navigation is EmployeeDetail 
         text is "View Employee" 
         session key Employee is Employee)
```

## Audit Log Context

When using `for each audit log records`, you can access action attributes for each historical record:

```
for each audit log records
    // Access historical action information
    each.action              // Action name
    each.actor               // Actor who performed the action
    each.effective date      // When the action was effective
    each.action comment      // Comment on the action
    each.changed field names // Fields that were changed
```

## Actor Context in Actions

Actors can have context fields that define their scope:

```
actor context.Company        // Company context for the actor
actor context.Department     // Department context for the actor
actor context.<SecurityClaim> // Security claim context
```

This is useful for multi-tenant or multi-company scenarios where actors need to be scoped to specific organizational units.
