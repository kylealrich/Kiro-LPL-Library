# Menu Panels

## Overview

Menu Panels provide navigation menus within panels on Pages and Composite Forms. They organize menu items with titles, descriptions, icons, and images, supporting hierarchical menu structures and anonymous access. Menu Panels can include optional footers using card views for additional information or branding.

## Basic Syntax

```
MenuPanel ::=
    Layout
        [footer is <CardViewName>]
        <MenuLayout>
        …

MenuLayout ::=
    ( <FormText>
    | <MenuItem>
        [title is <Message>]
        [menu item name is <MenuItemName>]
        [description is <Message>]
        [(icon is <Icon> | image is <ImageName>)]
        [allow anonymous access]
    | <LayoutDirective>
        [<MenuLayout>…])
```

## Menu Panel Structure

### Layout

```
Layout
    [footer is <CardViewName>]
    <MenuLayout>
    …
```

The layout contains the menu structure and optional footer.

### Footer

```
footer is <CardViewName>
```

**Must be first element under Layout.** Provides a footer using a card view.

**Use Cases:**
- Branding information
- Contact details
- Version information
- Help links
- Additional navigation

## Menu Layout Components

### Form Text

```
<FormText>
```

Static text within the menu for labels, instructions, or separators.

**Use Cases:**
- Section headers
- Instructions
- Separators
- Informational text

### Menu Item

```
<MenuItem>
    [title is <Message>]
    [menu item name is <MenuItemName>]
    [description is <Message>]
    [(icon is <Icon> | image is <ImageName>)]
    [allow anonymous access]
```

Individual menu item with navigation or action.

#### Menu Item Attributes

| Attribute | Description |
|-----------|-------------|
| `title is <Message>` | Menu item title |
| `menu item name is <MenuItemName>` | Unique identifier for the menu item |
| `description is <Message>` | Description text for the menu item |
| `icon is <Icon>` | Icon for the menu item |
| `image is <ImageName>` | Image for the menu item |
| `allow anonymous access` | Allows anonymous users to access |

**Note:** Use either icon or image, not both.

### Layout Directive

```
<LayoutDirective>
    [<MenuLayout>…]
```

Standard layout directives for organizing menu items.

**Available Directives:**
- Paragraph
- Single/multi-column layouts
- Sections
- Conditional visibility
- Blank lines
- Headers

## Examples

### Example 1: Simple Menu Panel

```
Layout
    DASHBOARD
        title is "Dashboard"
        icon is DashboardIcon
    
    CUSTOMERS
        title is "Customers"
        icon is CustomerIcon
    
    ORDERS
        title is "Orders"
        icon is OrderIcon
    
    REPORTS
        title is "Reports"
        icon is ReportIcon
```

### Example 2: Menu with Descriptions

```
Layout
    SALES
        title is "Sales"
        description is "Manage sales orders and quotes"
        icon is SalesIcon
    
    INVENTORY
        title is "Inventory"
        description is "Track inventory levels and movements"
        icon is InventoryIcon
    
    PURCHASING
        title is "Purchasing"
        description is "Create and manage purchase orders"
        icon is PurchaseIcon
```

### Example 3: Menu with Footer

```
Layout
    footer is COMPANYINFO
    
    HOME
        title is "Home"
        icon is HomeIcon
    
    PRODUCTS
        title is "Products"
        icon is ProductIcon
    
    SERVICES
        title is "Services"
        icon is ServiceIcon
    
    CONTACT
        title is "Contact"
        icon is ContactIcon
```

### Example 4: Hierarchical Menu with Sections

```
Layout
    section of SALES
        "Sales"
        
        ORDERS
            title is "Orders"
            icon is OrderIcon
        
        QUOTES
            title is "Quotes"
            icon is QuoteIcon
        
        CUSTOMERS
            title is "Customers"
            icon is CustomerIcon
    
    section of INVENTORY
        "Inventory"
        
        PRODUCTS
            title is "Products"
            icon is ProductIcon
        
        WAREHOUSES
            title is "Warehouses"
            icon is WarehouseIcon
        
        STOCKLEVELS
            title is "Stock Levels"
            icon is StockIcon
```

### Example 5: Menu with Images

```
Layout
    DASHBOARD
        title is "Dashboard"
        image is DashboardImage
        description is "View key metrics and reports"
    
    ANALYTICS
        title is "Analytics"
        image is AnalyticsImage
        description is "Analyze business data"
    
    SETTINGS
        title is "Settings"
        image is SettingsImage
        description is "Configure system settings"
```

### Example 6: Menu with Anonymous Access

```
Layout
    HOME
        title is "Home"
        icon is HomeIcon
        allow anonymous access
    
    ABOUT
        title is "About Us"
        icon is InfoIcon
        allow anonymous access
    
    CONTACT
        title is "Contact"
        icon is ContactIcon
        allow anonymous access
    
    LOGIN
        title is "Login"
        icon is LoginIcon
        allow anonymous access
```

### Example 7: Multi-Column Menu

```
Layout
    two column
        SALES
            title is "Sales"
            icon is SalesIcon
        
        MARKETING
            title is "Marketing"
            icon is MarketingIcon
        
        FINANCE
            title is "Finance"
            icon is FinanceIcon
        
        HR
            title is "Human Resources"
            icon is HRIcon
        
        IT
            title is "IT"
            icon is ITIcon
        
        OPERATIONS
            title is "Operations"
            icon is OperationsIcon
```

### Example 8: Menu with Form Text

```
Layout
    "Main Menu"
    
    blank line
    
    DASHBOARD
        title is "Dashboard"
        icon is DashboardIcon
    
    REPORTS
        title is "Reports"
        icon is ReportIcon
    
    blank line
    
    "Administration"
    
    blank line
    
    USERS
        title is "Users"
        icon is UserIcon
    
    SETTINGS
        title is "Settings"
        icon is SettingsIcon
```

### Example 9: Conditional Menu Items

```
Layout
    DASHBOARD
        title is "Dashboard"
        icon is DashboardIcon
    
    visible when actor has role "Manager"
        REPORTS
            title is "Reports"
            icon is ReportIcon
        
        ANALYTICS
            title is "Analytics"
            icon is AnalyticsIcon
    
    visible when actor has role "Admin"
        ADMIN
            title is "Administration"
            icon is AdminIcon
```

### Example 10: Complete Menu with Footer

```
Layout
    footer is APPFOOTER
    
    section of MAIN
        "Main Navigation"
        
        HOME
            title is "Home"
            description is "Return to home page"
            icon is HomeIcon
        
        DASHBOARD
            title is "Dashboard"
            description is "View your dashboard"
            icon is DashboardIcon
    
    section of MODULES
        "Modules"
        
        SALES
            title is "Sales"
            description is "Sales management"
            icon is SalesIcon
        
        INVENTORY
            title is "Inventory"
            description is "Inventory management"
            icon is InventoryIcon
        
        PURCHASING
            title is "Purchasing"
            description is "Purchase management"
            icon is PurchaseIcon
    
    section of TOOLS
        "Tools"
        
        REPORTS
            title is "Reports"
            description is "Generate reports"
            icon is ReportIcon
        
        SETTINGS
            title is "Settings"
            description is "System settings"
            icon is SettingsIcon
```

### Example 11: Menu with Menu Item Names

```
Layout
    DASHBOARD
        title is "Dashboard"
        menu item name is MAIN_DASHBOARD
        icon is DashboardIcon
    
    ORDERS
        title is "Orders"
        menu item name is ORDER_LIST
        icon is OrderIcon
    
    CUSTOMERS
        title is "Customers"
        menu item name is CUSTOMER_LIST
        icon is CustomerIcon
```

### Example 12: Public Menu

```
Layout
    footer is PUBLICFOOTER
    
    HOME
        title is "Home"
        icon is HomeIcon
        allow anonymous access
    
    PRODUCTS
        title is "Products"
        description is "Browse our products"
        icon is ProductIcon
        allow anonymous access
    
    SERVICES
        title is "Services"
        description is "View our services"
        icon is ServiceIcon
        allow anonymous access
    
    ABOUT
        title is "About Us"
        icon is InfoIcon
        allow anonymous access
    
    CONTACT
        title is "Contact"
        description is "Get in touch"
        icon is ContactIcon
        allow anonymous access
```

### Example 13: Menu with Large Icons

```
Layout
    large menu
    
    SALES
        title is "Sales"
        description is "Manage sales operations"
        image is SalesImage
    
    INVENTORY
        title is "Inventory"
        description is "Track inventory"
        image is InventoryImage
    
    REPORTS
        title is "Reports"
        description is "Generate reports"
        image is ReportsImage
```

### Example 14: Grouped Menu

```
Layout
    section of OPERATIONS
        "Operations"
        
        two column
            ORDERS
                title is "Orders"
                icon is OrderIcon
            
            SHIPPING
                title is "Shipping"
                icon is ShippingIcon
            
            RECEIVING
                title is "Receiving"
                icon is ReceivingIcon
            
            RETURNS
                title is "Returns"
                icon is ReturnIcon
    
    section of MANAGEMENT
        "Management"
        
        single column
            REPORTS
                title is "Reports"
                icon is ReportIcon
            
            ANALYTICS
                title is "Analytics"
                icon is AnalyticsIcon
            
            SETTINGS
                title is "Settings"
                icon is SettingsIcon
```

### Example 15: Menu with Mixed Content

```
Layout
    footer is BRANDINGCARD
    
    "Welcome to the Application"
    
    blank line
    
    QUICKSTART
        title is "Quick Start"
        description is "Get started quickly"
        icon is QuickStartIcon
    
    blank line
    
    section of MODULES
        "Available Modules"
        
        SALES
            title is "Sales"
            icon is SalesIcon
        
        INVENTORY
            title is "Inventory"
            icon is InventoryIcon
        
        FINANCE
            title is "Finance"
            icon is FinanceIcon
    
    blank line
    
    HELP
        title is "Help"
        description is "Get help and support"
        icon is HelpIcon
```

## Use Cases

### Application Navigation
- Main application menu
- Module navigation
- Feature access
- Quick links

### Portal Menus
- Landing page navigation
- Public website menus
- User portals
- Dashboard navigation

### Hierarchical Navigation
- Grouped menu items
- Categorized navigation
- Multi-level menus
- Organized access

### Role-Based Menus
- Conditional menu items
- Permission-based access
- User-specific menus
- Security enforcement

### Public Menus
- Anonymous access
- Public websites
- Marketing pages
- Information portals

## Best Practices

### Menu Design

1. **Organization**
   - Group related items
   - Use logical order
   - Limit menu depth
   - Keep it simple

2. **Visual Design**
   - Use consistent icons
   - Provide descriptions
   - Balance text and images
   - Consider branding

3. **Hierarchy**
   - Use sections for grouping
   - Limit nesting levels
   - Clear structure
   - Intuitive navigation

### Menu Items

1. **Titles**
   - Clear and concise
   - Action-oriented
   - Consistent naming
   - Avoid jargon

2. **Descriptions**
   - Helpful context
   - Brief explanations
   - Consistent style
   - Optional but useful

3. **Icons and Images**
   - Meaningful icons
   - Consistent style
   - Appropriate size
   - Good contrast

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

### Footer

1. **Footer Content**
   - Branding information
   - Contact details
   - Version info
   - Help links

2. **Footer Design**
   - Keep concise
   - Use card view
   - Match menu style
   - Test appearance

### Layout

1. **Layout Directives**
   - Use appropriate layouts
   - Single column for simple menus
   - Multi-column for many items
   - Sections for grouping

2. **Conditional Visibility**
   - Show relevant items
   - Hide based on permissions
   - Test all conditions
   - Provide feedback

### Performance

1. **Optimization**
   - Minimize menu items
   - Lazy load if needed
   - Optimize images
   - Cache appropriately

2. **Responsiveness**
   - Test on mobile
   - Adapt to screen size
   - Touch-friendly
   - Accessible

### Accessibility

1. **Keyboard Navigation**
   - Tab through items
   - Enter to select
   - Escape to close
   - Shortcuts available

2. **Screen Readers**
   - Proper labels
   - Descriptions helpful
   - Structure clear
   - Test with tools

## Common Patterns

### Simple Menu Pattern

```
Layout
    <MenuItem1>
        title is "<Title>"
        icon is <Icon>
    
    <MenuItem2>
        title is "<Title>"
        icon is <Icon>
```

### Grouped Menu Pattern

```
Layout
    section of <GroupName>
        "<Group Label>"
        
        <MenuItem1>
            title is "<Title>"
            icon is <Icon>
        
        <MenuItem2>
            title is "<Title>"
            icon is <Icon>
```

### Menu with Footer Pattern

```
Layout
    footer is <CardViewName>
    
    <MenuItem1>
        title is "<Title>"
        icon is <Icon>
    
    <MenuItem2>
        title is "<Title>"
        icon is <Icon>
```

### Descriptive Menu Pattern

```
Layout
    <MenuItem>
        title is "<Title>"
        description is "<Description>"
        icon is <Icon>
```

## Troubleshooting

### Issue: Menu Items Not Displaying

**Possible Causes:**
- Layout errors
- Conditional visibility false
- Permission issues
- Menu item definition errors

**Solutions:**
- Check layout syntax
- Verify conditions
- Check permissions
- Validate menu items

### Issue: Icons Not Showing

**Possible Causes:**
- Icon not found
- Icon name incorrect
- Icon not loaded
- Display issues

**Solutions:**
- Verify icon exists
- Check icon name
- Ensure icon loaded
- Test display

### Issue: Footer Not Displaying

**Possible Causes:**
- Footer not first element
- Card view not found
- Card view errors
- Layout issues

**Solutions:**
- Move footer to first
- Verify card view exists
- Fix card view
- Check layout

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

### Issue: Menu Layout Problems

**Possible Causes:**
- Layout directive errors
- Too many items
- Nesting issues
- Responsive problems

**Solutions:**
- Fix layout directives
- Reduce item count
- Simplify nesting
- Test responsive design

## Related Concepts

- [Panel Definitions](panel-definitions.md) - Panel types
- [Pages](pages.md) - Page definitions
- [Composite Forms](composite-forms.md) - Multi-panel forms
- [Card Views](card-views.md) - Card view definitions
- [Form Base Definitions](form-base-definitions.md) - Form components
- [Navigations](navigations.md) - Navigation definitions
- [UI Definition](ui-definition.md) - UI components

## Summary

Menu Panels provide navigation menus within panels on Pages and Composite Forms. With support for titles, descriptions, icons, images, hierarchical organization, and anonymous access, Menu Panels enable intuitive navigation interfaces. Optional footers using card views provide additional branding and information. Flexible layout directives support various menu structures from simple lists to complex hierarchical menus.

**Key Features:**
- Menu items with titles
- Descriptions for context
- Icons and images
- Anonymous access support
- Footer with card view
- Hierarchical organization
- Layout directives
- Conditional visibility
- Form text for labels
- Section grouping

**Menu Item Attributes:**
- Title
- Menu item name
- Description
- Icon or image
- Anonymous access

**Best For:**
- Application navigation
- Portal menus
- Hierarchical navigation
- Role-based menus
- Public menus
- Dashboard navigation
- Module access
- Quick links

**Advantages:**
- Flexible organization
- Rich visual design
- Hierarchical support
- Anonymous access
- Footer capability
- Conditional display
- Multiple layouts
- Easy to configure

**Important Notes:**
- Footer must be first element under Layout
- Use either icon or image, not both
- Test anonymous access thoroughly
- Validate server-side security
- Consider mobile responsiveness
- Use meaningful icons and descriptions
