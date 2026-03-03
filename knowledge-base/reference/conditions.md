# Conditions

Conditions are named boolean expressions that can be reused throughout a business class. They encapsulate complex logic into reusable, testable components.

## Purpose

Conditions are used to:
- Define reusable boolean logic
- Simplify complex conditional expressions
- Improve code readability and maintainability
- Enable consistent business rule application
- Support validation and workflow logic

## Syntax

```
Conditions
    <ConditionName>
        [<SqlName>]
        [<ClassicName>]
        [<DefaultLabel>]
        [restricted]
        when <Condition>
```

## Condition Structure

```
Condition ::= <Parens><ConditionNode><Parens>
              [<Conjunction> <Parens><ConditionNode><Parens>…]

Conjunction ::= ( and
                | or)
```

## Condition Node Types

```
ConditionNode ::= ( <ActorCondition>
                  | <ChangedCondition>
                  | <ComparisonCondition>
                  | <FieldCondition>
                  | <FieldWasCondition>
                  | <IsABusClassCondition>
                  | <RelatedCondition>
                  | <RelationCondition>
                  | <WithinBusClassGroupCondition>)
```

## Actor Condition

```
ActorCondition ::= [authenticated] actor (has role | in group) <Text>
```

Text is either a role name or an actor group name.

### Examples

```
Conditions
    ISMANAGER
        when actor has role "Manager"
    
    ISADMIN
        when authenticated actor in group "Administrators"
```

## Changed Condition

```
ChangedCondition ::= (<RelatedLink> | <RelatedField> | [<RelatedLink>].<FieldGroupName>)
                     changed between <RelatedValue> and <RelatedValue>
```

'changed between' RelatedValues must be Date or TimeStamp.

### Examples

```
Conditions
    RECENTLYCHANGED
        when Customer changed between current date - 30 days and current date
    
    PRICECHANGED
        when Product.Price changed between StartDate and EndDate
```

## Comparison Condition

```
ComparisonCondition ::= <RelatedValue> <ConditionOperator> <RelatedValue>
```

### Condition Operators

```
ConditionOperator ::= ( =
                      | !=
                      | >=
                      | <=
                      | >
                      | <
                      | !>
                      | !<
                      | like
                      | contains
                      | [not] within
                      | [not] overlaps
                      | [not] matches)
```

### Operator Descriptions

| Operator | Description |
|----------|-------------|
| `=` | Equal to |
| `!=` | Not equal to |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |
| `>` | Greater than |
| `<` | Less than |
| `!>` | Not greater than |
| `!<` | Not less than |
| `like` | Wild-card searching (similar to =) |
| `contains` | Text search (field must be text searchable) |
| `within` | Hierarchy or range containment |
| `not within` | Not within hierarchy or range |
| `overlaps` | Range overlap (FieldRange pattern) |
| `not overlaps` | No range overlap |
| `matches` | Regular expression match |
| `not matches` | Does not match regular expression |

### Within Operator

The `within` operator is valid with:

1. **Array key fields that implement the Hierarchy pattern**: Evaluates to true when the left operand's occurring fields match up to the non-blank right operand's occurring fields.
   - Example: `{5,3,9,10,20}` matches `{5,,,,}`, `{5,3,,,}`, `{5,3,9,,}`, `{5,3,9,10,}`, `{5,3,9,10,20}`
   - Concept: Left operand is 'within' the right operand's hierarchical structure

2. **Group field that implements the FieldRange pattern**: Right operand must be a FieldRange

3. **TimeStamp, Date, Period, Year**: Valid on both sides as long as the left operand has more specificity than the right operand
   - Valid: `<TimeStamp> within <Date>`
   - Invalid: `<Date> within <TimeStamp>`

### Overlaps Operator

Valid only with two group fields that implement the FieldRange pattern.

### Matches Operator

Treats the right side operand as a regular expression to determine if the left side operand completely matches the pattern. See java.util.regex.Pattern documentation or "Mastering Regular Expressions, 2nd Edition," Jeffrey E.F. Friedl, O'Reilly and Associates, 2002.

### Examples

```
Conditions
    HIGHVALUE
        when Amount >= 10000
    
    ACTIVEANDRECENT
        when Status = "Active" and CreateDate >= current date - 90 days
    
    NAMELIKE
        when CustomerName like "Smith%"
    
    SEARCHTEXT
        when Description contains SearchCriteria
    
    INRANGE
        when EffectiveDate within DateRange
    
    EMAILVALID
        when Email matches "^[A-Za-z0-9+_.-]+@(.+)$"
```

## Field Condition

```
FieldCondition ::= [<FieldOperator>](<RelatedField> | <FieldGroupName>)
                   ( entered
                   | is numeric
                   | is high value
                   | changed
                   | in context
                   | is leap year
                   | is last day in month
                   | has audit change)
```

**Notes:**
- `entered` not yet implemented with `<FieldGroupName>`
- `is numeric` not yet implemented with `<FieldGroupName>`
- `in context` not yet implemented with `<FieldGroupName>`
- Cannot use `<RelatedField>` with `changed` and `in context` - these can take a `<FullFieldName>`
- `has audit change` valid only in context of an audit entry

### Examples

```
Conditions
    AMOUNTENTERED
        when Amount entered
    
    DATECHANGED
        when OrderDate changed
    
    CUSTOMERINCONTEXT
        when Customer in context
    
    LEAPYEAR
        when FiscalYear is leap year
    
    MONTHEND
        when TransactionDate is last day in month
```

## Field Was Condition

```
FieldWasCondition ::= <FullFieldName> was [always] <RelatedValue>
                      [between <RelatedValue> and <RelatedValue>]
```

Between RelatedValues must be Date or TimeStamp.

### Examples

```
Conditions
    WASACTIVE
        when Status was "Active"
    
    WASALWAYSACTIVE
        when Status was always "Active" between StartDate and EndDate
    
    PRICEHISTORY
        when Price was 100.00 between current date - 365 days and current date
```

## Is A Business Class Condition

```
IsABusClassCondition ::= <RelatedField> is a[n] <BusinessClass>
```

### Examples

```
Conditions
    ISMANAGER
        when Employee is a Manager
    
    ISSPECIALCUSTOMER
        when Customer is a PreferredCustomer
```

## Related Condition

```
RelatedCondition ::= [<FieldOperator>]( [<RelatedLink>.]<ConditionName>
                                       | <RelatedField>.<StateName>)
```

### Examples

```
Conditions
    CUSTOMERACTIVE
        when Customer.Active
    
    ORDERELIGIBLE
        when Customer.IsEligible
```

## Relation Condition

```
RelationCondition ::= [(first | no[t])] <RelatedLink> (exists | pending | has future changes)
```

**Notes:**
- `pending` is valid only with a BusinessObjectReference field or a KeyField designated as 'store as BusinessObjectReference'
- Returns true if the reference is filled out but the record does not actually exist yet
- Currently can happen only when using asynchronous AGS or other async third party interface
- `has future changes` returns true if there are future effective dated changes from the current as of date

### Examples

```
Conditions
    HASORDERS
        when Order set exists
    
    NOORDERS
        when not Order set exists
    
    FIRSTORDER
        when first Order set exists
    
    PENDINGCREATION
        when RelatedObject pending
    
    HASFUTURECHANGES
        when Customer has future changes
```

## Within Business Class Group Condition

```
WithinBusClassGroupCondition ::= <RelatedLink> [not] within <RelatedField>
```

RelatedField must be a BusinessClassGroup or an LPL Text field that is a condition.

### Examples

```
Conditions
    INAUTHORIZEDGROUP
        Customer within AuthorizedCustomers
    
    NOTINRESTRICTEDGROUP
        Employee not within RestrictedEmployees
```

## Complete Examples

### Simple Conditions

```
CUSTOMER is a BusinessClass
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNAME is Alpha size 50
        CREDITLIMIT is Decimal size 15.2
        BALANCE is Decimal size 15.2
        ACTIVE is Boolean
    
    Conditions
        ISACTIVE
            default label is "Is Active"
            when Active == true
        
        OVERCREDITLIMIT
            default label is "Over Credit Limit"
            when Balance > CreditLimit
        
        GOODSTANDING
            default label is "Good Standing"
            when Active == true and Balance <= CreditLimit
```

### Complex Conditions

```
ORDER is a BusinessClass
    Persistent Fields
        ORDER is Order
        ORDERDATE is Date
        CUSTOMER is Customer
        STATUS is OrderStatus
        TOTAL is Decimal size 15.2
        SHIPDATE is Date
    
    Conditions
        CANSHIP
            default label is "Can Ship"
            when Status == OrderStatus.Approved 
                 and Customer.Active == true
                 and Customer.Balance <= Customer.CreditLimit
                 and ShipDate == blank
        
        ISOVERDUE
            default label is "Is Overdue"
            when Status != OrderStatus.Shipped
                 and Status != OrderStatus.Cancelled
                 and OrderDate < current date - 30 days
        
        RECENTORDER
            default label is "Recent Order"
            when OrderDate >= current date - 90 days
```

### Actor-Based Conditions

```
DOCUMENT is a BusinessClass
    Conditions
        CANAPPROVE
            default label is "Can Approve"
            when actor has role "Approver" or actor has role "Manager"
        
        CANDELETE
            default label is "Can Delete"
            when actor in group "Administrators"
        
        ISOWNER
            default label is "Is Owner"
            when CreatedBy == actor
```

### Audit-Based Conditions

```
TRANSACTION is a BusinessClass
    Conditions
        AMOUNTCHANGED
            default label is "Amount Changed"
            when Amount changed
        
        RECENTLYMODIFIED
            default label is "Recently Modified"
            when Transaction changed between current date - 7 days and current date
        
        HASAUDITCHANGE
            default label is "Has Audit Change"
            when Amount has audit change
```

### Pattern-Based Conditions

```
EMPLOYEE is a BusinessClass
    Persistent Fields
        EMAIL is Alpha size 100
        PHONE is Alpha size 20
        SSN is Alpha size 11
    
    Conditions
        VALIDEMAIL
            default label is "Valid Email"
            when Email matches "^[A-Za-z0-9+_.-]+@(.+)$"
        
        VALIDPHONE
            default label is "Valid Phone"
            when Phone matches "^\\d{3}-\\d{3}-\\d{4}$"
        
        VALIDSSN
            default label is "Valid SSN"
            when SSN matches "^\\d{3}-\\d{2}-\\d{4}$"
```

## Use Cases

### Validation

```
Conditions
    VALIDORDER
        when Customer entered
             and OrderDate entered
             and Total > 0
```

### Eligibility

```
Conditions
    ELIGIBLE
        when Age >= 18
             and Active == true
             and Balance >= MinimumBalance
```

### Workflow

```
Conditions
    CANPROCEED
        when Status == Status.Pending
             and actor has role "Approver"
             and Amount <= ApprovalLimit
```

### Security

```
Conditions
    HASACCESS
        when actor has role "User"
             or actor in group "Administrators"
             or CreatedBy == actor
```

## Best Practices

### Naming

- Use descriptive condition names
- Use verb phrases (Is, Can, Has, Should)
- Follow consistent naming conventions
- Make names self-documenting
- Avoid abbreviations

### Logic

- Keep conditions focused and single-purpose
- Break complex logic into multiple conditions
- Use meaningful conjunctions (and/or)
- Test all branches
- Handle edge cases

### Reusability

- Design conditions for reuse
- Avoid overly specific conditions
- Document condition purpose
- Consider composition of simpler conditions
- Test conditions independently

### Performance

- Keep conditions efficient
- Avoid expensive operations
- Consider query impact
- Test with realistic data
- Monitor performance

### Maintenance

- Document complex conditions
- Review conditions periodically
- Update as business rules change
- Test after modifications
- Version control condition changes

## Common Patterns

### Status Check Pattern

```
Conditions
    ISACTIVE
        when Status == "Active"
```

### Range Check Pattern

```
Conditions
    INRANGE
        when Value >= MinValue and Value <= MaxValue
```

### Existence Check Pattern

```
Conditions
    HASRELATEDRECORDS
        when RelatedRecords set exists
```

### Permission Check Pattern

```
Conditions
    HASPERMISSION
        when actor has role "Role" or actor in group "Group"
```

### Date Range Pattern

```
Conditions
    INCURRENTPERIOD
        when EffectiveDate >= PeriodStart and EffectiveDate <= PeriodEnd
```

## Related Concepts

### Derived Fields

Conditions can be used in derived field calculations.

### Field Rules

Conditions control when field rules execute.

### Actions

Conditions determine action availability and execution.

### Validation

Conditions implement validation logic.

### Security

Conditions enforce security rules.

## Troubleshooting

### Common Issues

1. **Condition always false**: Check logic and field values
2. **Performance issues**: Simplify complex conditions
3. **Null handling**: Test with null/blank values
4. **Type mismatches**: Ensure compatible types in comparisons
5. **Operator errors**: Use correct operator for data type

### Debugging Tips

- Test conditions with various data
- Check field values at runtime
- Verify operator usage
- Test all logical branches
- Review conjunction logic (and/or)
- Test with edge cases
- Use logging to trace condition evaluation

## Summary

Conditions provide:
- Reusable boolean logic
- Named expressions for clarity
- Consistent business rule application
- Improved code maintainability
- Support for complex conditional logic

Proper use of Conditions enhances code quality, readability, and maintainability while ensuring consistent application of business rules throughout the application.
