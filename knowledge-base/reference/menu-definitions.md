# Menu Definitions

## Overview

Menu Definitions create application menus that provide navigation to pages, lists, forms, actions, and other menus. Menus exist in the same namespace as BusinessClasses, BusinessTasks, and Pages. They support hierarchical structures, search terms, conditional visibility, and various navigation targets including springboard display for touch-friendly interfaces.

## Menu Files

Menu definition files have an extension of `.menu`.

**Namespace:** Menu names exist in the same namespace as BusinessClasses, BusinessTasks, and Pages.

## Basic Syntax

```
Menu Structure ::=
    <MenuName> is a Menu
        [show as springboard]
        [Search Terms]
            <Message>
            …
        (<MenuItems> | <MenuPanel>)
        [<MenuName> is a Menu …]
            [Search Terms]
                <Message>
                …
            <MenuItems>
```

## Menu Attributes

### Show as Springboard

```
show as springboard
```

Brings up the menu in a springboard layout - a grid-based, touch-friendly interface ideal for tablets and touch devices.

### Search Terms

```
Search Terms
    <Message>
    …
```

Defines search terms for finding menu items. Enables quick search and filtering of menu options.

### Inline Menus

```
<MenuName> is a Menu …
```

Allows menus to be 'in-lined' within a single menu definition file. Enables hierarchical menu structures within one file.

## Menu Items

```
MenuItems ::=
    Menu Items
        <MenuItemName>
        …
        [title is <Message>]
        [(icon is <Icon> | image is <ImageName>)]
        [(valid | visible) when <BusinessClass>.<SimpleCondition>]
        <MenuItem>
        [allow anonymous access]
        [Search Terms]
            <Message>
            …
```

### Menu Item Attributes

| Attribute | Description |
|-----------|-------------|
| `<MenuItemName>` | Unique identifier for the menu item |
| `title is <Message>` | Display title for the menu item |
| `icon is <Icon>` | Icon for the menu item |
| `image is <ImageName>` | Image for the menu item |
| `valid when` | Menu item enabled only when condition true |
| `visible when` | Menu item displayed only when condition true |
| `allow anonymous access` | Allows anonymous users to access |

**Note:** Use either icon or image, not both.

### Conditional Visibility

```
(valid | visible) when <BusinessClass>.<SimpleCondition>
```

Conditions must reference a BusinessClass and SimpleCondition.

**Example:**
```
valid when Customer.Active = true
visible when Order.Status = "Pending"
```

## Menu Item Types

```
MenuItem ::=
    ( page is <PageName>[.<PanelName>]
    | list is <BusinessClass>[<AsOfOperator>].(<ListName> | primary)
    | form is <BusinessClass>.(<FormName> | primary)
        [actor agent required]
        [create action is [<StateName>.]<ActionName>]
        [open action is [<StateName>.]<ActionName>]
        …
        [(restrict | enable) action [<StateName>.]<ActionName>]
        …
    | action is [<BusinessClass> | <BusinessTask>].<ActionName>
    | link is [<BusinessClass>.]<UILink>
    | menu is <MenuName>
    | image map is <ImageMapName>
    | webapp is <WebAppName>)
```

### Page Navigation

```
page is <PageName>[.<PanelName>]
```

Navigates to a page, optionally to a specific panel.

**Example:**
```
page is DASHBOARD
page is CUSTOMERVIEW.ORDERS
```

### List Navigation

```
list is <BusinessClass>[<AsOfOperator>].(<ListName> | primary)
```

Navigates to a list for a business class.

**Features:**
- Automatic instance resolution
- Temporal context with AsOfOperator
- Primary list or named list

**Example:**
```
list is Customer.CUSTOMERLIST
list is Order.primary
list is Account as of current date.ACCOUNTLIST
```

### Form Navigation

```
form is <BusinessClass>.(<FormName> | primary)
    [actor agent required]
    [create action is [<StateName>.]<ActionName>]
    [open action is [<StateName>.]<ActionName>]
    …
    [(restrict | enable) action [<StateName>.]<ActionName>]
    …
```

Navigates to a form for a business class.

**Attributes:**
- **actor agent required**: Requires actor agent
- **create action**: Specifies create action
- **open action**: Specifies open action(s)
- **restrict/enable action**: Controls action availability

**Example:**
```
form is Customer.CUSTOMERFORM
form is Order.primary
    create action is CREATE
    open action is EDIT
    restrict action DELETE
```

### Action Execution

```
action is [<BusinessClass> | <BusinessTask>].<ActionName>
```

Executes an action on a business class or business task.

**Example:**
```
action is Order.APPROVE
action is IMPORTTASK.IMPORT
```

### Link Navigation

```
link is [<BusinessClass>.]<UILink>
```

Navigates to a UI link. Except NavigationName when no BusinessClass.

**Example:**
```
link is Customer.CUSTOMERDETAIL
link is HELPPAGE
```

### Submenu

```
menu is <MenuName>
```

Opens another menu as a submenu.

**Example:**
```
menu is ADMINMENU
menu is REPORTSMENU
```

### Image Map

```
image map is <ImageMapName>
```

Displays an image map for navigation.

### Web Application

```
webapp is <WebAppName>
```

**Valid internally for configuration only.** References a web application.

## Navigation Bar Home Item

**Important:** If a menu is used in a Navigation Bar within a `.webapp` file, then the first menu item is 'Home' which takes the user to the 'home page' in the webapp definition file.

## Context-Based Instance Resolution

For menu items with BusinessClass references:
- `form is <BusinessClass>…`
- `list is <BusinessClass>…`
- `link is <BusinessClass>…`
- `action is <BusinessClass>…`

**Resolution Process:**
1. Look for an agent of BusinessClass type linked with the current actor
2. If not found, fill in primary keys with:
   - Actor context variables
   - Session.key variables
3. Try to find an instance based on those keys

This enables automatic context-aware navigation to the most relevant instance.

## Examples

### Example 1: Simple Menu

```
MAINMENU is a Menu
    Menu Items
        DASHBOARD
            title is "Dashboard"
            icon is DashboardIcon
            page is DASHBOARD
        
        CUSTOMERS
            title is "Customers"
            icon is CustomerIcon
            list is Customer.CUSTOMERLIST
        
        ORDERS
            title is "Orders"
            icon is OrderIcon
            list is Order.ORDERLIST
```

### Example 2: Menu with Springboard

```
MOBILEMENU is a Menu
    show as springboard
    
    Menu Items
        HOME
            title is "Home"
            image is HomeImage
            page is HOMEPAGE
        
        SALES
            title is "Sales"
            image is SalesImage
            menu is SALESMENU
        
        REPORTS
            title is "Reports"
            image is ReportsImage
            menu is REPORTSMENU
```

### Example 3: Menu with Search Terms

```
APPLICATIONMENU is a Menu
    Search Terms
        "main menu"
        "navigation"
        "home"
    
    Menu Items
        DASHBOARD
            title is "Dashboard"
            icon is DashboardIcon
            page is DASHBOARD
            Search Terms
                "home"
                "overview"
                "summary"
        
        CUSTOMERS
            title is "Customers"
            icon is CustomerIcon
            list is Customer.CUSTOMERLIST
            Search Terms
                "clients"
                "accounts"
                "contacts"
```

### Example 4: Menu with Conditional Items

```
ADMINMENU is a Menu
    Menu Items
        USERS
            title is "Users"
            icon is UserIcon
            visible when User.IsAdmin = true
            list is User.USERLIST
        
        SETTINGS
            title is "Settings"
            icon is SettingsIcon
            valid when User.IsAdmin = true
            page is SETTINGSPAGE
        
        AUDIT
            title is "Audit Log"
            icon is AuditIcon
            visible when User.CanViewAudit = true
            list is AuditEntry.AUDITLIST
```

### Example 5: Menu with Forms

```
SALESMENU is a Menu
    Menu Items
        CUSTOMERS
            title is "Customers"
            icon is CustomerIcon
            form is Customer.CUSTOMERFORM
            create action is CREATE
            open action is EDIT
        
        ORDERS
            title is "Orders"
            icon is OrderIcon
            form is Order.ORDERFORM
            create action is CREATE
            open action is EDIT
            restrict action DELETE
```

### Example 6: Menu with Actions

```
PROCESSMENU is a Menu
    Menu Items
        IMPORT
            title is "Import Data"
            icon is ImportIcon
            action is IMPORTTASK.IMPORT
        
        EXPORT
            title is "Export Data"
            icon is ExportIcon
            action is EXPORTTASK.EXPORT
        
        SYNC
            title is "Synchronize"
            icon is SyncIcon
            action is SYNCTASK.SYNC
```

### Example 7: Hierarchical Menu with Inline Menus

```
MAINMENU is a Menu
    Menu Items
        DASHBOARD
            title is "Dashboard"
            icon is DashboardIcon
            page is DASHBOARD
        
        SALES
            title is "Sales"
            icon is SalesIcon
            menu is SALESMENU
        
        ADMIN
            title is "Administration"
            icon is AdminIcon
            menu is ADMINMENU
    
    SALESMENU is a Menu
        Menu Items
            CUSTOMERS
                title is "Customers"
                icon is CustomerIcon
                list is Customer.CUSTOMERLIST
            
            ORDERS
                title is "Orders"
                icon is OrderIcon
                list is Order.ORDERLIST
            
            QUOTES
                title is "Quotes"
                icon is QuoteIcon
                list is Quote.QUOTELIST
    
    ADMINMENU is a Menu
        Menu Items
            USERS
                title is "Users"
                icon is UserIcon
                list is User.USERLIST
            
            SETTINGS
                title is "Settings"
                icon is SettingsIcon
                page is SETTINGSPAGE
```

### Example 8: Menu with Anonymous Access

```
PUBLICMENU is a Menu
    Menu Items
        HOME
            title is "Home"
            icon is HomeIcon
            page is HOMEPAGE
            allow anonymous access
        
        ABOUT
            title is "About Us"
            icon is InfoIcon
            page is ABOUTPAGE
            allow anonymous access
        
        CONTACT
            title is "Contact"
            icon is ContactIcon
            page is CONTACTPAGE
            allow anonymous access
        
        LOGIN
            title is "Login"
            icon is LoginIcon
            page is LOGINPAGE
            allow anonymous access
```

### Example 9: Menu with Temporal Lists

```
REPORTSMENU is a Menu
    Menu Items
        CURRENTACCOUNTS
            title is "Current Accounts"
            icon is AccountIcon
            list is Account as of current date.ACCOUNTLIST
        
        CURRENTBALANCES
            title is "Current Balances"
            icon is BalanceIcon
            list is Balance as of current date.BALANCELIST
```

### Example 10: Menu with Panel Navigation

```
WORKSPACEMENU is a Menu
    Menu Items
        TASKS
            title is "My Tasks"
            icon is TaskIcon
            page is WORKSPACE.TASKS
        
        PROJECTS
            title is "My Projects"
            icon is ProjectIcon
            page is WORKSPACE.PROJECTS
        
        CALENDAR
            title is "Calendar"
            icon is CalendarIcon
            page is WORKSPACE.CALENDAR
```

### Example 11: Menu with Links

```
HELPMENU is a Menu
    Menu Items
        DOCUMENTATION
            title is "Documentation"
            icon is DocIcon
            link is DOCPAGE
        
        SUPPORT
            title is "Support"
            icon is SupportIcon
            link is Customer.SUPPORTPAGE
        
        ABOUT
            title is "About"
            icon is InfoIcon
            link is ABOUTPAGE
```

### Example 12: Complex Menu with Multiple Features

```
APPLICATIONMENU is a Menu
    show as springboard
    
    Search Terms
        "main"
        "application"
        "menu"
    
    Menu Items
        DASHBOARD
            title is "Dashboard"
            image is DashboardImage
            page is DASHBOARD
            Search Terms
                "home"
                "overview"
        
        SALES
            title is "Sales"
            image is SalesImage
            menu is SALESMENU
            visible when User.Department = "Sales"
            Search Terms
                "customers"
                "orders"
                "revenue"
        
        INVENTORY
            title is "Inventory"
            image is InventoryImage
            menu is INVENTORYMENU
            Search Terms
                "products"
                "stock"
                "warehouse"
        
        REPORTS
            title is "Reports"
            image is ReportsImage
            menu is REPORTSMENU
            valid when User.CanViewReports = true
            Search Terms
                "analytics"
                "data"
                "insights"
    
    SALESMENU is a Menu
        Search Terms
            "sales"
            "revenue"
        
        Menu Items
            CUSTOMERS
                title is "Customers"
                icon is CustomerIcon
                form is Customer.CUSTOMERFORM
                create action is CREATE
                Search Terms
                    "clients"
                    "accounts"
            
            ORDERS
                title is "Orders"
                icon is OrderIcon
                list is Order.ORDERLIST
                Search Terms
                    "sales orders"
                    "purchases"
```

### Example 13: Menu with Actor Agent

```
MYMENU is a Menu
    Menu Items
        MYPROFILE
            title is "My Profile"
            icon is ProfileIcon
            form is User.USERFORM
            actor agent required
        
        MYTASKS
            title is "My Tasks"
            icon is TaskIcon
            list is Task.MYTASKLIST
        
        MYPROJECTS
            title is "My Projects"
            icon is ProjectIcon
            list is Project.MYPROJECTLIST
```

### Example 14: Menu with Action Restrictions

```
ORDERMENU is a Menu
    Menu Items
        ORDERS
            title is "Orders"
            icon is OrderIcon
            form is Order.ORDERFORM
            create action is CREATE
            open action is EDIT
            open action is VIEW
            restrict action DELETE when Order.Status = "Shipped"
            enable action APPROVE when Order.Status = "Pending"
```

### Example 15: Department-Specific Menu

```
DEPARTMENTMENU is a Menu
    Menu Items
        SALES
            title is "Sales"
            icon is SalesIcon
            menu is SALESMENU
            visible when User.Department = "Sales"
        
        FINANCE
            title is "Finance"
            icon is FinanceIcon
            menu is FINANCEMENU
            visible when User.Department = "Finance"
        
        HR
            title is "Human Resources"
            icon is HRIcon
            menu is HRMENU
            visible when User.Department = "HR"
        
        IT
            title is "IT"
            icon is ITIcon
            menu is ITMENU
            visible when User.Department = "IT"
```

## Use Cases

### Application Navigation
- Main application menu
- Module navigation
- Feature access
- Quick links

### Role-Based Menus
- Department-specific menus
- Permission-based access
- User role menus
- Security enforcement

### Hierarchical Navigation
- Nested menus
- Submenu structures
- Organized access
- Categorized features

### Mobile Interfaces
- Springboard menus
- Touch-friendly navigation
- Tablet interfaces
- Responsive menus

### Public Menus
- Anonymous access
- Public websites
- Marketing pages
- Information portals

## Best Practices

### Menu Design

1. **Organization**
   - Logical grouping
   - Clear hierarchy
   - Limit depth
   - Intuitive structure

2. **Menu Items**
   - Clear titles
   - Meaningful icons
   - Consistent naming
   - Avoid jargon

3. **Search Terms**
   - Include synonyms
   - Common terms
   - User language
   - Enable discovery

### Springboard Menus

1. **When to Use**
   - Touch interfaces
   - Tablet applications
   - Mobile-first design
   - Visual navigation

2. **Design**
   - Use images
   - Clear labels
   - Appropriate sizing
   - Touch-friendly

### Conditional Visibility

1. **Security**
   - Use valid when for security
   - Hide sensitive items
   - Validate server-side
   - Test thoroughly

2. **User Experience**
   - Show relevant items
   - Hide unavailable features
   - Provide feedback
   - Guide users

### Context Resolution

1. **Instance Resolution**
   - Understand resolution process
   - Test with various contexts
   - Handle missing instances
   - Provide fallbacks

2. **Agent Usage**
   - Use actor agents appropriately
   - Test agent resolution
   - Handle missing agents
   - Document behavior

### Anonymous Access

1. **Public Content**
   - Mark public items clearly
   - Test anonymous access
   - Secure private content
   - Validate permissions

2. **Security**
   - Don't rely on hiding
   - Validate server-side
   - Audit access
   - Test thoroughly

### Performance

1. **Optimization**
   - Minimize menu items
   - Lazy load submenus
   - Cache appropriately
   - Test performance

2. **Large Menus**
   - Use hierarchical structure
   - Implement search
   - Provide filtering
   - Consider pagination

### Testing

1. **Functional Testing**
   - Test all menu items
   - Test navigation
   - Test conditions
   - Verify access

2. **Integration Testing**
   - Test context resolution
   - Test agent resolution
   - Test permissions
   - Verify behavior

## Common Patterns

### Simple Menu Pattern

```
<MenuName> is a Menu
    Menu Items
        <MenuItem1>
            title is "<Title>"
            icon is <Icon>
            <NavigationTarget>
```

### Hierarchical Menu Pattern

```
<MenuName> is a Menu
    Menu Items
        <MenuItem1>
            title is "<Title>"
            icon is <Icon>
            menu is <SubMenuName>
    
    <SubMenuName> is a Menu
        Menu Items
            <SubMenuItem1>
                title is "<Title>"
                icon is <Icon>
                <NavigationTarget>
```

### Springboard Menu Pattern

```
<MenuName> is a Menu
    show as springboard
    
    Menu Items
        <MenuItem1>
            title is "<Title>"
            image is <Image>
            <NavigationTarget>
```

### Conditional Menu Pattern

```
<MenuName> is a Menu
    Menu Items
        <MenuItem1>
            title is "<Title>"
            icon is <Icon>
            visible when <BusinessClass>.<Condition>
            <NavigationTarget>
```

## Troubleshooting

### Issue: Menu Items Not Displaying

**Possible Causes:**
- Conditional visibility false
- Permission issues
- Menu definition errors
- Navigation target errors

**Solutions:**
- Check visibility conditions
- Verify permissions
- Validate menu definition
- Check navigation targets

### Issue: Context Resolution Not Working

**Possible Causes:**
- No agent found
- Context variables missing
- Primary keys not set
- Instance not found

**Solutions:**
- Verify agent exists
- Set context variables
- Check primary keys
- Ensure instance exists

### Issue: Anonymous Access Not Working

**Possible Causes:**
- Not marked for anonymous
- Server-side restrictions
- Configuration issues
- Security settings

**Solutions:**
- Add allow anonymous access
- Check server config
- Review security settings
- Test thoroughly

### Issue: Springboard Not Displaying

**Possible Causes:**
- Not marked as springboard
- Images missing
- Layout issues
- Device compatibility

**Solutions:**
- Add show as springboard
- Verify images exist
- Check layout
- Test on device

### Issue: Search Not Working

**Possible Causes:**
- Search terms not defined
- Search disabled
- Configuration issues
- Index problems

**Solutions:**
- Define search terms
- Enable search
- Check configuration
- Rebuild index

## Related Concepts

- [Menu Panels](menu-panels.md) - Menu panel definitions
- [Pages](pages.md) - Page definitions
- [Lists](lists.md) - List definitions
- [Forms](forms.md) - Form definitions
- [Actions](actions.md) - Action definitions
- [Navigations](navigations.md) - Navigation definitions
- [Business Class Definition](business-class-definition.md) - Business class structure

## Summary

Menu Definitions create application menus that provide navigation to pages, lists, forms, actions, and other menus. With support for hierarchical structures, search terms, conditional visibility, springboard display, and automatic context-based instance resolution, Menu Definitions enable sophisticated navigation systems. Menus can be tailored to user roles, departments, and permissions, providing secure, intuitive access to application features.

**Key Features:**
- Hierarchical menu structures
- Springboard display
- Search terms
- Conditional visibility
- Anonymous access
- Context-based instance resolution
- Multiple navigation targets
- Inline menu definitions
- Icon and image support
- Action restrictions

**Navigation Targets:**
- Pages (with optional panel)
- Lists (with temporal context)
- Forms (with action control)
- Actions
- Links
- Submenus
- Image maps
- Web applications

**Best For:**
- Application navigation
- Role-based menus
- Hierarchical navigation
- Mobile interfaces
- Public menus
- Module access
- Feature organization
- Quick access

**Advantages:**
- Flexible structure
- Context-aware navigation
- Security integration
- Search capability
- Touch-friendly options
- Easy configuration
- Hierarchical organization
- Automatic instance resolution

**Important Notes:**
- Menu names share namespace with BusinessClasses, BusinessTasks, and Pages
- First menu item in Navigation Bar is 'Home'
- Context resolution tries agent first, then context variables
- Use valid when for security, visible when for display
- Test anonymous access thoroughly
- Validate server-side security
- Consider mobile and touch interfaces
- Document search terms for discoverability
