# Security Class Definitions

## Overview

Security Class definitions control access rights and permissions within LPL applications. They define who can access what objects, perform which actions, and under what conditions. Security classes support inheritance, ontological policies, and fine-grained access control for business classes, actions, fields, menus, and other securable objects.

## Syntax

```
<SecurityClassName> is a SecurityClass
    [extends <SecurityClassName>]
    [description is <Message>]
    Access Rights
        <PolicyDefinition>…
```

## Core Components

### Security Class Attributes

| Attribute | Description | Required |
|-----------|-------------|----------|
| `extends` | Inherits access rights from parent security class | No |
| `description` | Human-readable description of security class purpose | No |
| `Access Rights` | Section containing all policy definitions | Yes |

### Policy Types

| Type | Description | Use Case |
|------|-------------|----------|
| `AccessPolicy` | Direct access rules for specific objects | Explicit permissions |
| `OntologicalPolicy` | Key-based access grants for related objects | Dynamic permissions based on relationships |

## Access Policy Structure

### Securable Object Types

| Type | Description |
|------|-------------|
| `BusinessClass` | Business class definitions |
| `BusinessTask` | Business task definitions |
| `Menu` | Menu definitions |
| `MenuItem` | Individual menu items |
| `WebApp` | Web application definitions |
| `DataArea` | Data area containers |
| `Module` | Module containers |
| `Field` | Individual fields |
| `KeyField` | Key fields for ontological policies |

### Action Keywords

| Keyword | Description | Scope |
|---------|-------------|-------|
| `all actions` | All action types | All objects |
| `all audit views` | All audit viewing actions | All objects |
| `all creates` | All create actions (includes inquiries) | All objects |
| `all deletes` | All delete actions | All objects |
| `all inquiries` | All inquiry/read actions | All objects |
| `all functions` | All function actions | All objects |
| `all updates` | All update actions | All objects |
| `all global UI configuration` | Global UI customization | DataArea only |
| `all security configuration` | Security settings management | DataArea only |
| `all personalization` | All personalization features | DataArea only |
| `limited list personalization` | List customization only | DataArea only |
| `limited form personalization` | Form customization only | DataArea only |
| `all scheduled actions` | Scheduled job management | DataArea only |
| `all actor groups` | Actor group management | DataArea only |
| `data menu` | Data menu access | DataArea only |
| `future data indicator` | Future-dated data visibility | DataArea only |
| `UpdateEffectiveDatedData` | Update effective-dated records | All objects |
| `ViewAuditLog` | View audit log entries | All objects |
| `ViewFullAuditLog` | View complete audit history | All objects |

### Access Levels

| Level | Description |
|-------|-------------|
| `is accessible` | Grants access to the object/action |
| `is accessible and attachable` | Grants access and attachment rights |
| `is not accessible` | Denies access to the object/action |
| `is neither accessible nor attachable` | Denies both access and attachment |

### Temporal Access

| Scope | Description |
|-------|-------------|
| `in past` | Access to historical/past-dated records |
| `in current` | Access to current records |
| `in future` | Access to future-dated records |
| `in past, current, future` | Access to all temporal states |


### Constraints

| Type | Description |
|------|-------------|
| `unconditionally` | Access granted without conditions |
| `when <Condition>` | Access granted only when condition is true |

## Examples

### Example 1: Basic Security Class with Simple Access

```lpl
ReadOnlyUser is a SecurityClass
    description is "Basic read-only access to core business classes"
    Access Rights
        Customer BusinessClass
            is accessible
            for all inquiries
            unconditionally
        
        Product BusinessClass
            is accessible
            for all inquiries
            unconditionally
        
        Order BusinessClass
            is accessible
            for all inquiries
            unconditionally
```

### Example 2: Security Class with Inheritance

```lpl
BasicUser is a SecurityClass
    description is "Foundation access rights"
    Access Rights
        Customer BusinessClass
            is accessible
            for all inquiries
            unconditionally

PowerUser is a SecurityClass
    extends BasicUser
    description is "Enhanced access with create and update rights"
    Access Rights
        Customer BusinessClass
            is accessible
            for all creates, all updates
            unconditionally
        
        Order BusinessClass
            is accessible
            for all actions
            unconditionally
```

### Example 3: Conditional Access Based on Field Values

```lpl
RegionalManager is a SecurityClass
    description is "Access limited to specific region"
    Access Rights
        Customer BusinessClass
            is accessible
            for all actions
            when Customer.Region = Actor.Region
        
        Order BusinessClass
            is accessible
            for all inquiries, all updates
            when Order.Customer.Region = Actor.Region
```

### Example 4: Temporal Access Control

```lpl
HistoricalDataAnalyst is a SecurityClass
    description is "Access to historical and current data only"
    Access Rights
        Employee BusinessClass
            is accessible in past, current
            for all inquiries
            unconditionally
        
        Payroll BusinessClass
            is accessible in past, current
            for all inquiries, ViewAuditLog
            unconditionally
        
        Employee BusinessClass
            is not accessible in future
            for all actions
            unconditionally
```

### Example 5: Field-Level Security

```lpl
StandardEmployee is a SecurityClass
    description is "Employee with restricted field access"
    Access Rights
        All Fields for Employee BusinessClass
            excluding Salary Field, BonusAmount Field, SSN Field
            is accessible
            for all inquiries
            unconditionally
        
        Salary Field
            is not accessible
            for all actions
            unconditionally
        
        BonusAmount Field
            is not accessible
            for all actions
            unconditionally
```

### Example 6: Ontological Policy with Key Field

```lpl
DepartmentManager is a SecurityClass
    description is "Access to department-related data"
    Access Rights
        Department KeyField
            grants access
            to all business classes
            for all actions
            when Department = Actor.Department
```

### Example 7: Menu and UI Access Control

```lpl
LimitedUIUser is a SecurityClass
    description is "Restricted UI and menu access"
    Access Rights
        MainMenu Menu
            is accessible
            for all inquiries
            unconditionally
        
        AdminMenu Menu
            is not accessible
            for all actions
            unconditionally
        
        Reports MenuItem for MainMenu Menu
            is accessible
            for all inquiries
            unconditionally
        
        DataArea DataArea
            is accessible
            for limited list personalization, limited form personalization
            unconditionally
```

### Example 8: Action-Specific Access

```lpl
OrderProcessor is a SecurityClass
    description is "Order processing with specific action rights"
    Access Rights
        Order BusinessClass
            is accessible
            for all inquiries, all creates, all updates
            unconditionally
        
        Order BusinessClass
            is accessible
            for Order.Submit, Order.Approve
            unconditionally
        
        Order BusinessClass
            is not accessible
            for Order.Cancel, Order.Delete
            unconditionally
```

### Example 9: Audit and Security Configuration Access

```lpl
SecurityAdministrator is a SecurityClass
    description is "Full security and audit access"
    Access Rights
        DataArea DataArea
            is accessible
            for all security configuration, all actor groups
            unconditionally
        
        all business classes
            is accessible
            for ViewFullAuditLog
            unconditionally
        
        SecurityClass BusinessClass
            is accessible
            for all actions
            unconditionally
```

### Example 10: Excluding Specific Actions

```lpl
DataEntryClerk is a SecurityClass
    description is "Data entry with limited delete rights"
    Access Rights
        Customer BusinessClass
            is accessible
            for all actions excluding all deletes
            unconditionally
        
        Product BusinessClass
            is accessible
            for all inquiries, all creates, all updates
            unconditionally
        
        Order BusinessClass
            is accessible
            for all creates, all updates
            when Order.Status = "Draft"
```

### Example 11: Attachability Control

```lpl
DocumentManager is a SecurityClass
    description is "Document attachment management"
    Access Rights
        Contract BusinessClass
            is accessible and attachable
            for all inquiries, all updates
            unconditionally
        
        Invoice BusinessClass
            is accessible and attachable
            for all actions
            unconditionally
        
        Employee BusinessClass
            is accessible
            for all inquiries
            unconditionally
```

### Example 12: Complex Ontological Policy

```lpl
ProjectTeamMember is a SecurityClass
    description is "Access based on project membership"
    Access Rights
        Project KeyField
            grants access
            to all business classes excluding Budget BusinessClass, Payroll BusinessClass
            for all inquiries, all updates
            when Project.Team contains Actor
        
        Project KeyField
            grants access
            to Task BusinessClass, Milestone BusinessClass
            for all actions
            when Project.Team contains Actor
```

### Example 13: State-Based Access Control

```lpl
ApprovalManager is a SecurityClass
    description is "Access based on workflow state"
    Access Rights
        PurchaseOrder BusinessClass
            is accessible
            for all inquiries
            unconditionally
        
        PurchaseOrder BusinessClass
            is accessible
            for PurchaseOrder.Pending.Approve, PurchaseOrder.Pending.Reject
            when PurchaseOrder.Approver = Actor
        
        PurchaseOrder BusinessClass
            is not accessible
            for all updates
            when PurchaseOrder.Status = "Approved"
```

### Example 14: Module and Container Access

```lpl
ModuleDeveloper is a SecurityClass
    description is "Development access to specific modules"
    Access Rights
        FinanceModule Module
            is accessible
            for all actions
            unconditionally
        
        HRModule Module
            is not accessible
            for all actions
            unconditionally
        
        DataArea DataArea
            is accessible
            for all global UI configuration, all personalization
            unconditionally
```

### Example 15: Comprehensive Multi-Level Security

```lpl
ExecutiveUser is a SecurityClass
    description is "Executive-level access with comprehensive rights"
    Access Rights
        all business classes
            is accessible in past, current, future
            for all inquiries, ViewAuditLog
            unconditionally
        
        Customer BusinessClass
            is accessible and attachable
            for all actions
            unconditionally
        
        Order BusinessClass
            is accessible
            for all actions
            unconditionally
        
        Employee BusinessClass
            is accessible
            for all inquiries, all updates
            unconditionally
        
        All Fields for Employee BusinessClass
            is accessible
            for all inquiries
            unconditionally
        
        DataArea DataArea
            is accessible
            for all personalization, data menu, future data indicator
            unconditionally
```

## Use Cases

### Role-Based Access Control

1. **Read-Only Users**: Grant inquiry access only, no modifications
2. **Data Entry Clerks**: Create and update access, limited delete rights
3. **Managers**: Full access to department data, conditional on ownership
4. **Administrators**: Full system access including security configuration
5. **Auditors**: Read access with full audit log visibility

### Organizational Security

1. **Department-Based**: Access limited to user's department data
2. **Region-Based**: Access limited to geographic regions
3. **Project-Based**: Access limited to assigned projects
4. **Team-Based**: Access limited to team membership
5. **Hierarchy-Based**: Access based on organizational hierarchy

### Temporal Security

1. **Historical Analysis**: Access to past data only
2. **Current Operations**: Access to current data only
3. **Planning**: Access to future-dated data
4. **Audit Review**: Access to all temporal states
5. **Effective Dating**: Control updates to effective-dated records

### Field-Level Security

1. **Sensitive Data**: Restrict access to salary, SSN, personal information
2. **Calculated Fields**: Read-only access to derived values
3. **System Fields**: Restrict modification of system-maintained fields
4. **Partial Records**: Grant access to subset of fields
5. **Progressive Disclosure**: Show fields based on user role

### Action-Level Security

1. **Workflow Control**: Restrict actions based on state
2. **Approval Rights**: Grant approval actions to specific roles
3. **Delete Protection**: Prevent deletion by certain users
4. **Batch Operations**: Control access to bulk actions
5. **Special Functions**: Restrict access to administrative functions

## Best Practices

### Security Class Design

1. **Use Inheritance**: Build security classes hierarchically to reduce duplication
2. **Descriptive Names**: Use clear, role-based names for security classes
3. **Document Purpose**: Always include description for maintainability
4. **Principle of Least Privilege**: Grant minimum necessary access
5. **Regular Review**: Periodically audit security class definitions

### Access Control Strategy

1. **Default Deny**: Start with no access, explicitly grant permissions
2. **Explicit Over Implicit**: Be explicit about access rules
3. **Separate Concerns**: Create focused security classes for specific roles
4. **Avoid Over-Permissioning**: Don't grant "all actions" unless necessary
5. **Test Thoroughly**: Verify access rules work as intended

### Conditional Access

1. **Simple Conditions**: Keep conditions straightforward and testable
2. **Performance Aware**: Complex conditions can impact performance
3. **Field Availability**: Ensure referenced fields exist and are accessible
4. **Actor Context**: Use Actor fields for user-specific conditions
5. **Null Handling**: Consider null values in conditional logic

### Ontological Policies

1. **Key Field Selection**: Choose appropriate key fields for relationships
2. **Scope Carefully**: Use excluding clause to limit scope
3. **Performance Impact**: Ontological policies can be expensive
4. **Test Relationships**: Verify key field relationships work correctly
5. **Document Logic**: Explain the relationship-based access model

### Field-Level Security

1. **Group Related Fields**: Apply same rules to related fields together
2. **Exclude Explicitly**: Use excluding clause for sensitive fields
3. **Consider Dependencies**: Some fields may depend on others
4. **UI Impact**: Hidden fields may affect form layouts
5. **Audit Fields**: Consider access to audit and system fields

### Temporal Access

1. **Business Requirements**: Align temporal access with business needs
2. **Effective Dating**: Consider impact on effective-dated records
3. **Historical Reporting**: Ensure analysts can access historical data
4. **Future Planning**: Control who can create future-dated records
5. **Audit Trail**: Maintain audit access across temporal states


### Menu and UI Security

1. **Progressive Disclosure**: Show menus based on user capabilities
2. **Consistent Experience**: Ensure UI matches access rights
3. **Personalization Limits**: Control customization capabilities
4. **Menu Structure**: Organize menus by security requirements
5. **Error Prevention**: Hide unavailable options to prevent errors

## Common Patterns

### Pattern 1: Hierarchical Role Structure

```lpl
BaseUser is a SecurityClass
    description is "Foundation access for all users"
    Access Rights
        // Common read access

StandardUser is a SecurityClass
    extends BaseUser
    description is "Standard user with create/update rights"
    Access Rights
        // Additional create/update access

PowerUser is a SecurityClass
    extends StandardUser
    description is "Power user with advanced features"
    Access Rights
        // Advanced features and actions

Administrator is a SecurityClass
    extends PowerUser
    description is "Full administrative access"
    Access Rights
        // Administrative and security configuration
```

### Pattern 2: Department-Based Access

```lpl
DepartmentUser is a SecurityClass
    description is "Access limited to department data"
    Access Rights
        Department KeyField
            grants access
            to all business classes
            for all inquiries, all updates
            when Department = Actor.Department
```

### Pattern 3: Conditional State-Based Access

```lpl
WorkflowUser is a SecurityClass
    description is "Access based on workflow state"
    Access Rights
        Document BusinessClass
            is accessible
            for all inquiries
            unconditionally
        
        Document BusinessClass
            is accessible
            for all updates
            when Document.Status = "Draft" and Document.Owner = Actor
        
        Document BusinessClass
            is accessible
            for Document.Submit
            when Document.Status = "Draft" and Document.Owner = Actor
        
        Document BusinessClass
            is accessible
            for Document.Approve, Document.Reject
            when Document.Status = "Pending" and Document.Approver = Actor
```

### Pattern 4: Field Exclusion Pattern

```lpl
RestrictedFieldAccess is a SecurityClass
    description is "Access with sensitive field restrictions"
    Access Rights
        All Fields for Employee BusinessClass
            excluding Salary Field, SSN Field, BankAccount Field
            is accessible
            for all inquiries, all updates
            unconditionally
        
        // Explicitly deny sensitive fields
        Salary Field
            is not accessible
            for all actions
            unconditionally
```

### Pattern 5: Temporal Segregation Pattern

```lpl
HistoricalUser is a SecurityClass
    description is "Historical data access only"
    Access Rights
        all business classes
            is accessible in past
            for all inquiries, ViewAuditLog
            unconditionally
        
        all business classes
            is not accessible in current, future
            for all actions
            unconditionally
```

### Pattern 6: Multi-Level Approval Pattern

```lpl
Level1Approver is a SecurityClass
    description is "First level approval rights"
    Access Rights
        Request BusinessClass
            is accessible
            for Request.Level1Approve, Request.Level1Reject
            when Request.Amount <= 10000 and Request.Level1Approver = Actor

Level2Approver is a SecurityClass
    extends Level1Approver
    description is "Second level approval rights"
    Access Rights
        Request BusinessClass
            is accessible
            for Request.Level2Approve, Request.Level2Reject
            when Request.Amount > 10000 and Request.Level2Approver = Actor
```

## Troubleshooting

### Common Issues

**Issue**: User cannot access expected objects
- **Solution**: Verify security class is assigned to user's actor
- **Solution**: Check for conflicting "is not accessible" rules
- **Solution**: Ensure conditions evaluate to true for user's context
- **Solution**: Verify inheritance chain includes necessary permissions

**Issue**: Conditional access not working
- **Solution**: Verify field references in conditions are correct
- **Solution**: Check that Actor fields are populated
- **Solution**: Test condition logic with actual data
- **Solution**: Ensure referenced fields are accessible

**Issue**: Ontological policy not granting access
- **Solution**: Verify key field relationship is correctly defined
- **Solution**: Check that key field values match
- **Solution**: Test relationship traversal in business class
- **Solution**: Ensure excluding clause doesn't block access

**Issue**: Field-level security not hiding fields
- **Solution**: Verify field names match exactly
- **Solution**: Check that UI respects field security
- **Solution**: Ensure "All Fields" exclusion is correct
- **Solution**: Test with different forms and views

**Issue**: Temporal access not working as expected
- **Solution**: Verify temporal scope (past/current/future) is correct
- **Solution**: Check effective dating on business class
- **Solution**: Ensure date context is set properly
- **Solution**: Test with records in different temporal states

**Issue**: Inherited permissions not applying
- **Solution**: Verify extends clause references correct parent
- **Solution**: Check for overriding rules in child class
- **Solution**: Ensure parent class is properly defined
- **Solution**: Test parent class permissions independently

**Issue**: Menu items visible but not accessible
- **Solution**: Add menu item access rules explicitly
- **Solution**: Verify business class access includes menu actions
- **Solution**: Check for UI-specific access restrictions
- **Solution**: Ensure menu security matches action security

**Issue**: Attachability not working
- **Solution**: Verify "and attachable" is included in access rule
- **Solution**: Check business class supports attachments
- **Solution**: Ensure attachment actions are accessible
- **Solution**: Test with actual attachment operations

**Issue**: Performance degradation with security
- **Solution**: Simplify complex conditions
- **Solution**: Optimize ontological policy key fields
- **Solution**: Reduce number of conditional rules
- **Solution**: Consider caching for expensive checks
- **Solution**: Profile security evaluation performance

**Issue**: Audit log access denied
- **Solution**: Add ViewAuditLog or ViewFullAuditLog action
- **Solution**: Verify audit configuration is enabled
- **Solution**: Check DataArea-level audit permissions
- **Solution**: Ensure user has appropriate audit rights

## Security Class Composition

### Combining Multiple Policies

Security classes can combine different policy types for comprehensive access control:

```lpl
ComprehensiveSecurity is a SecurityClass
    description is "Combined access policies"
    Access Rights
        // Direct access policy
        Customer BusinessClass
            is accessible
            for all inquiries, all updates
            unconditionally
        
        // Ontological policy
        Department KeyField
            grants access
            to Employee BusinessClass, Budget BusinessClass
            for all actions
            when Department = Actor.Department
        
        // Field-level policy
        All Fields for Employee BusinessClass
            excluding Salary Field
            is accessible
            for all inquiries
            unconditionally
        
        // Temporal policy
        Payroll BusinessClass
            is accessible in past, current
            for all inquiries
            unconditionally
        
        // Menu policy
        AdminMenu Menu
            is not accessible
            for all actions
            unconditionally
```

### Layered Security Approach

Build security in layers from general to specific:

```lpl
LayeredSecurity is a SecurityClass
    description is "Layered security approach"
    Access Rights
        // Layer 1: Grant broad inquiry access
        all business classes
            is accessible
            for all inquiries
            unconditionally
        
        // Layer 2: Grant specific update access
        Customer BusinessClass
            is accessible
            for all updates
            when Customer.Owner = Actor
        
        // Layer 3: Restrict sensitive actions
        Customer BusinessClass
            is not accessible
            for Customer.Delete
            unconditionally
        
        // Layer 4: Field-level restrictions
        All Fields for Customer BusinessClass
            excluding CreditLimit Field
            is accessible
            for all actions
            unconditionally
```

## Integration with Business Logic

### Security in Action Rules

Security classes work with action rules to enforce business logic:

```lpl
// In Security Class
SalesRep is a SecurityClass
    Access Rights
        Order BusinessClass
            is accessible
            for Order.Submit
            when Order.SalesRep = Actor

// In Business Class Action Rule
Order is a BusinessClass
    Submit is an Update Action
        // Security class controls who can execute
        // Action rule controls what happens
        Rules
            set Status = "Submitted"
            set SubmittedDate = today
            set SubmittedBy = Actor
```

### Security with Validation Rules

Combine security with validation for robust control:

```lpl
// Security controls access
Manager is a SecurityClass
    Access Rights
        Expense BusinessClass
            is accessible
            for Expense.Approve
            when Expense.Approver = Actor

// Validation ensures data integrity
Expense is a BusinessClass
    Approve is an Update Action
        Rules
            require Amount > 0
            require Status = "Pending"
            require ApprovalDate is null
            set Status = "Approved"
            set ApprovalDate = today
```

## Related Concepts

- [Actions](actions.md) - Action definitions and execution
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Conditions](conditions.md) - Conditional logic and expressions
- [Rules](rules.md) - Business rules and validation
- [Actions and Actors](actions-actors.md) - Actor context and security
- [Field Definition](field-definition.md) - Field structure and properties
- [Menu Definitions](menu-definitions.md) - Menu structure and access
- [Audit Entry Rules](audit-entry-rules.md) - Audit logging configuration

## Summary

Security Class definitions provide comprehensive access control for LPL applications. Through inheritance, ontological policies, conditional access, and field-level security, you can implement sophisticated role-based access control that aligns with organizational structure and business requirements. Follow best practices for security design, test thoroughly, and regularly review access rights to maintain a secure application environment.
