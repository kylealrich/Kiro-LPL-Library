# Panel Definitions

## Overview

Panel Definitions specify the content and behavior of panels within Pages and Composite Forms. Panels can contain menus, lists, search interfaces, or external URLs. They support conditional visibility based on session keys or actor context, enabling dynamic panel display based on user state and permissions.

## Basic Syntax

```
PanelDefinition ::=
    [(valid | visible) when <SimpleCondition>]
    ( <MenuPanel>
    | <ListPanel>
    | <SearchPanel>
    | <URLPanel>)
```

## Conditional Visibility

```
[(valid | visible) when <SimpleCondition>]
```

Controls when the panel is valid or visible.

**Important Restrictions:**
- SimpleCondition must start with `SessionKey` or `actor.context.KeyField`
- Not valid when the Panel is used in a Multi-Pane Panel

### Valid vs Visible

| Attribute | Description |
|-----------|-------------|
| `valid when` | Panel is enabled/accessible only when condition is true |
| `visible when` | Panel is displayed only when condition is true |

### Condition Requirements

Conditions must reference:
- **SessionKey**: Session-level variables
- **actor.context.KeyField**: Actor context fields

**Examples:**
```
valid when SessionKey.UserRole = "Manager"
visible when actor.context.Department = "Sales"
valid when SessionKey.HasPermission = true
visible when actor.context.SecurityLevel >= 3
```

## Panel Types

### Menu Panel

```
<MenuPanel>
```

Displays a menu interface. See [Menu Panel](#menu-panel-details) section for details.

### List Panel

```
<ListPanel>
```

Displays a list of records. See [List Panel](#list-panel-details) section for details.

### Search Panel

```
<SearchPanel>
```

Displays a search interface. See [Search Panel](#search-panel-details) section for details.

### URL Panel

```
<URLPanel>
```

Displays external content via URL. See [URL Panel](#url-panel-details) section for details.

## Menu Panel Details

Menu Panels provide navigation menus within panels.

### Purpose
- Navigation structure
- Menu hierarchies
- Action grouping
- Quick access

### Use Cases
- Application menus
- Context menus
- Navigation trees
- Action menus

## List Panel Details

List Panels display lists of business class instances.

### Purpose
- Record display
- Data browsing
- Selection interfaces
- Master-detail views

### Use Cases
- Record lists
- Search results
- Related records
- Selection lists

## Search Panel Details

Search Panels provide search interfaces within panels.

### Purpose
- Search criteria entry
- Filtering interfaces
- Query builders
- Advanced search

### Use Cases
- Record search
- Data filtering
- Query interfaces
- Find functionality

## URL Panel Details

URL Panels embed external content via URLs.

### Purpose
- External content integration
- Web page embedding
- Third-party integration
- External resources

### Use Cases
- External websites
- Documentation
- Help content
- Third-party tools

## Examples

### Example 1: Conditional List Panel

```
ORDERLIST is a Panel
    title is "Orders"
    visible when SessionKey.ShowOrders = true
    ORDERLIST
```

### Example 2: Role-Based Menu Panel

```
ADMINMENU is a Panel
    title is "Administration"
    valid when actor.context.Role = "Administrator"
    ADMINMENU
```

### Example 3: Department-Specific List

```
DEPTORDERS is a Panel
    title is "Department Orders"
    visible when actor.context.Department entered
    DEPARTMENTORDERLIST
```

### Example 4: Permission-Based Search Panel

```
ADVANCEDSEARCH is a Panel
    title is "Advanced Search"
    valid when SessionKey.AdvancedSearchEnabled = true
    ADVANCEDSEARCHFORM
```

### Example 5: Security Level URL Panel

```
DOCUMENTATION is a Panel
    title is "Documentation"
    visible when actor.context.SecurityLevel >= 2
    DOCUMENTATIONURL
```

### Example 6: Multiple Conditional Panels

```
SALESVIEW is a Page
    title is "Sales View"
    
    MYSALES is a Panel
        title is "My Sales"
        visible when actor.context.Role = "Salesperson"
        MYSALESLIST
    
    TEAMSALES is a Panel
        title is "Team Sales"
        visible when actor.context.Role = "Manager"
        TEAMSALESLIST
    
    ALLSALES is a Panel
        title is "All Sales"
        visible when actor.context.Role = "Executive"
        ALLSALESLIST
```

### Example 7: Session-Based Panel Visibility

```
DASHBOARD is a Page
    title is "Dashboard"
    
    SUMMARY is a Panel
        title is "Summary"
        SUMMARYFORM
    
    DETAILS is a Panel
        title is "Details"
        visible when SessionKey.ShowDetails = true
        DETAILLIST
    
    ANALYTICS is a Panel
        title is "Analytics"
        visible when SessionKey.AnalyticsEnabled = true
        ANALYTICSCHART
```

### Example 8: Context-Based Menu Panel

```
NAVIGATION is a Page
    title is "Navigation"
    
    MAINMENU is a Panel
        title is "Main Menu"
        MAINMENU
    
    ADMINMENU is a Panel
        title is "Admin Menu"
        valid when actor.context.IsAdmin = true
        ADMINMENU
    
    REPORTMENU is a Panel
        title is "Reports"
        valid when actor.context.CanViewReports = true
        REPORTMENU
```

### Example 9: Department-Filtered Lists

```
WORKVIEW is a Page
    title is "Work View"
    
    TASKS is a Panel
        title is "Tasks"
        visible when actor.context.Department entered
        TASKLIST
    
    PROJECTS is a Panel
        title is "Projects"
        visible when actor.context.Department entered
        PROJECTLIST
```

### Example 10: Feature Flag Panels

```
FEATURES is a Page
    title is "Features"
    
    STANDARDFEATURES is a Panel
        title is "Standard Features"
        STANDARDLIST
    
    BETAFEATURES is a Panel
        title is "Beta Features"
        visible when SessionKey.BetaFeaturesEnabled = true
        BETALIST
    
    EXPERIMENTALFEATURES is a Panel
        title is "Experimental"
        visible when SessionKey.ExperimentalEnabled = true
        EXPERIMENTALLIST
```

## Use Cases

### Role-Based Access
- Show panels based on user role
- Hide sensitive content
- Provide role-specific views
- Enforce security

### Permission-Based Display
- Display based on permissions
- Show authorized content only
- Hide restricted features
- Control access

### Feature Flags
- Enable/disable features
- Beta feature access
- A/B testing
- Gradual rollout

### Context-Specific Views
- Department-specific content
- Location-based display
- Organization-specific views
- Custom contexts

### Session State
- Show based on session state
- Display preferences
- User selections
- Temporary states

## Best Practices

### Conditional Visibility

1. **Condition Design**
   - Use clear conditions
   - Test all scenarios
   - Document requirements
   - Consider edge cases

2. **Session Keys**
   - Define session keys clearly
   - Use meaningful names
   - Document purpose
   - Manage lifecycle

3. **Actor Context**
   - Use appropriate context fields
   - Validate context availability
   - Handle missing context
   - Test with various users

### Security

1. **Access Control**
   - Use valid when for security
   - Don't rely solely on visibility
   - Validate server-side
   - Test thoroughly

2. **Sensitive Content**
   - Hide sensitive panels
   - Use valid when for restrictions
   - Audit access
   - Log violations

### Performance

1. **Conditional Loading**
   - Load visible panels only
   - Defer hidden panels
   - Minimize initial load
   - Cache appropriately

2. **Condition Evaluation**
   - Keep conditions simple
   - Avoid complex logic
   - Test performance
   - Monitor evaluation time

### User Experience

1. **Panel Visibility**
   - Provide clear feedback
   - Explain why hidden
   - Offer alternatives
   - Guide users

2. **Navigation**
   - Maintain consistent navigation
   - Update dynamically
   - Provide breadcrumbs
   - Test usability

### Testing

1. **Condition Testing**
   - Test all conditions
   - Test with various users
   - Test edge cases
   - Verify security

2. **Integration Testing**
   - Test panel interactions
   - Test context changes
   - Test session changes
   - Verify behavior

## Common Patterns

### Role-Based Pattern

```
<PanelName> is a Panel
    title is "<Panel Title>"
    valid when actor.context.Role = "<RoleName>"
    <PanelContent>
```

### Permission-Based Pattern

```
<PanelName> is a Panel
    title is "<Panel Title>"
    valid when actor.context.HasPermission = true
    <PanelContent>
```

### Feature Flag Pattern

```
<PanelName> is a Panel
    title is "<Panel Title>"
    visible when SessionKey.FeatureEnabled = true
    <PanelContent>
```

### Context-Based Pattern

```
<PanelName> is a Panel
    title is "<Panel Title>"
    visible when actor.context.ContextField entered
    <PanelContent>
```

## Multi-Pane Panel Restriction

**Important:** Conditional visibility (`valid when` / `visible when`) is **not valid** when the Panel is used in a Multi-Pane Panel.

### Why the Restriction

Multi-Pane Panels have fixed layouts with specific pane positions. Conditional visibility would disrupt the layout structure.

### Workaround

If conditional display is needed in a Multi-Pane Panel:
1. Use conditional content within the pane
2. Use conditional fields/lists
3. Handle visibility at content level
4. Consider alternative panel types

### Example

**Not Valid:**
```
WORKSPACE is a MultiPanePanel
    pane 1
        visible when SessionKey.ShowPane1 = true  // NOT VALID
        TASKLIST
```

**Valid Alternative:**
```
WORKSPACE is a MultiPanePanel
    pane 1
        TASKLIST
            // Handle conditional display within list
```

## Session Keys

Session keys are session-level variables that can be used in conditions.

### Common Session Keys
- User preferences
- Feature flags
- Temporary states
- Session settings
- Display options

### Setting Session Keys
Session keys are typically set through:
- User preferences
- Configuration
- Application logic
- Session initialization
- User actions

### Example Session Keys
```
SessionKey.ShowDetails
SessionKey.AdvancedMode
SessionKey.BetaFeaturesEnabled
SessionKey.UserRole
SessionKey.Department
```

## Actor Context

Actor context provides access to the current user's context fields.

### Common Actor Context Fields
- Role
- Department
- Location
- Security level
- Permissions
- Organization

### Accessing Actor Context
```
actor.context.Role
actor.context.Department
actor.context.SecurityLevel
actor.context.HasPermission
actor.context.Organization
```

### Example Conditions
```
valid when actor.context.Role = "Manager"
visible when actor.context.Department = "Sales"
valid when actor.context.SecurityLevel >= 3
visible when actor.context.HasPermission = true
```

## Troubleshooting

### Issue: Panel Not Displaying

**Possible Causes:**
- Condition evaluates to false
- Session key not set
- Actor context missing
- Syntax error in condition

**Solutions:**
- Check condition logic
- Verify session key values
- Validate actor context
- Review condition syntax

### Issue: Panel Always Visible

**Possible Causes:**
- Condition always true
- Wrong condition field
- Session key always set
- Logic error

**Solutions:**
- Review condition logic
- Check field references
- Verify session key behavior
- Test with various users

### Issue: Condition Not Working in Multi-Pane

**Possible Causes:**
- Using condition in Multi-Pane Panel
- Not allowed by design
- Restriction not understood

**Solutions:**
- Remove condition from Multi-Pane Panel
- Use alternative approach
- Handle visibility at content level
- Consider different panel type

### Issue: Security Bypass

**Possible Causes:**
- Using visible instead of valid
- Client-side only check
- No server-side validation
- Condition too permissive

**Solutions:**
- Use valid when for security
- Implement server-side checks
- Validate permissions
- Tighten conditions

### Issue: Performance Problems

**Possible Causes:**
- Complex conditions
- Frequent evaluation
- Many conditional panels
- Slow context lookup

**Solutions:**
- Simplify conditions
- Cache condition results
- Reduce conditional panels
- Optimize context access

## Related Concepts

- [Pages](pages.md) - Page definitions
- [Composite Forms](composite-forms.md) - Multi-panel forms
- [Lists](lists.md) - List definitions
- [Forms](forms.md) - Form definitions
- [Conditions](conditions.md) - Conditional logic
- [UI Definition](ui-definition.md) - UI components

## Summary

Panel Definitions specify the content and behavior of panels within Pages and Composite Forms. With support for conditional visibility based on session keys and actor context, panels can dynamically adapt to user roles, permissions, and session state. Panels can contain menus, lists, search interfaces, or external URLs, providing flexible content options for various use cases.

**Key Features:**
- Conditional visibility
- Session key conditions
- Actor context conditions
- Multiple panel types
- Valid vs visible control
- Security enforcement
- Dynamic display

**Panel Types:**
- Menu panels
- List panels
- Search panels
- URL panels

**Best For:**
- Role-based access
- Permission-based display
- Feature flags
- Context-specific views
- Session state management
- Dynamic interfaces
- Security enforcement

**Important Restrictions:**
- Conditions must start with SessionKey or actor.context
- Not valid in Multi-Pane Panels
- Server-side validation required for security

**Best Practices:**
- Use valid when for security
- Use visible when for display preferences
- Test all conditions thoroughly
- Document condition requirements
- Validate server-side
- Handle missing context gracefully
- Provide user feedback
- Monitor performance
