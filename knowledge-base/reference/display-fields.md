# Display Fields

Valid on key fields only.

## Syntax

```
Display Fields
    <RelatedField>
    <RelatedField>
    ...
```

## Purpose

Display Fields are the 'description' or 'name' fields for a key field. They are the 'human' names we give things, such as:
- Company name
- Employee name
- Product description
- Customer name
- Account description

## Behavior

Display Fields provide human-readable representations of key field values. When a key field is referenced in the UI or reports, the display fields are typically shown alongside or instead of the key value itself.

## Examples

### Company Display Field

```
COMPANY is a KeyField
    business class is Company
    type is Alpha size 4
    
    Context
        // context definitions
    
    Display Fields
        CompanyName
```

In this example, when COMPANY "0001" is displayed, it might show as "0001 - Acme Corporation" where "Acme Corporation" is the value of the CompanyName display field.

### Employee Display Field

```
EMPLOYEE is a KeyField
    business class is Employee
    type is Alpha size 10
    
    Context
        COMPANY
    
    Display Fields
        EmployeeName
```

### Product with Multiple Display Fields

```
PRODUCT is a KeyField
    business class is Product
    type is Alpha size 20
    
    Context
        COMPANY
    
    Display Fields
        ProductDescription
        ProductShortName
```

Multiple display fields can be defined. The first is typically the primary display field, with additional fields providing alternative or supplementary descriptions.

### Account Display Field

```
ACCOUNT is a KeyField
    business class is Account
    type is Alpha size 20
    
    Context
        COMPANY
    
    Display Fields
        AccountDescription
```

## Best Practices

### Field Selection

- Choose fields that provide meaningful, human-readable descriptions
- Use fields that are commonly understood by users
- Ensure display fields are always populated (or have good defaults)

### Naming Conventions

- Use descriptive field names like `<KeyField>Name` or `<KeyField>Description`
- Be consistent across similar key fields
- Examples:
  - `CompanyName` for COMPANY
  - `EmployeeName` for EMPLOYEE
  - `ProductDescription` for PRODUCT

### Multiple Display Fields

- The first display field is typically the primary one
- Additional display fields can provide:
  - Short names for space-constrained displays
  - Alternative descriptions for different contexts
  - Translated names for multi-language support

### Field Types

- Display fields are typically Alpha or Text fields
- Keep display fields reasonably sized for UI display
- Consider using translatable fields for multi-language support

### Data Quality

- Ensure display fields are required or have defaults
- Validate that display fields are populated during data entry
- Consider making display fields text searchable

### UI Considerations

- Display fields are often shown in dropdowns and lookups
- They appear in reports alongside key values
- They provide context in error messages
- They improve user experience by showing meaningful names

## Common Patterns

### Standard Display Field

```
VENDOR is a KeyField
    business class is Vendor
    type is Alpha size 10
    
    Context
        COMPANY
    
    Display Fields
        VendorName
```

### Display Field with Short Name

```
CUSTOMER is a KeyField
    business class is Customer
    type is Alpha size 10
    
    Context
        COMPANY
    
    Display Fields
        CustomerName
        CustomerShortName
```

### Translatable Display Field

```
CATEGORY is a KeyField
    business class is Category
    type is Alpha size 10
    
    Display Fields
        CategoryDescription
        
// In the business class:
// CategoryDescription is Alpha size 100
//     translatable
//     text searchable
```

## Display Field Usage

### In UI

When a key field is displayed in the user interface:
- Lookups show the display field(s) to help users select the right value
- Dropdowns display the key value and display field together
- Forms show display fields as read-only context information

### In Reports

Reports typically show:
- The key value for precise identification
- The display field for human readability
- Example: "Company: 0001 - Acme Corporation"

### In Error Messages

Error messages can reference display fields to make them more user-friendly:
- Instead of: "Invalid value for COMPANY: 0001"
- Show: "Invalid company: 0001 - Acme Corporation"

### In Audit Logs

Audit logs may include display fields to make historical records more readable without requiring lookups.

## Related Concepts

### Representative Text

Display fields are related to but distinct from representative text, which is a formatted string representation of a business object.

### Surrogate Keys

Display fields are particularly important for surrogate keys where the key value itself has no business meaning.

### Search Fields

Display fields are often marked as `text searchable` to enable users to find records by their human-readable names rather than just key values.

## Implementation Notes

### Field Definition

Display fields must be defined as fields within the business class that the key field references:

```
// In the KeyField definition
COMPANY is a KeyField
    business class is Company
    Display Fields
        CompanyName

// In the Company business class
COMPANY is a Company
    Persistent Fields
        COMPANYNAME is Alpha size 50
            text searchable
            default label is "Company Name"
```

### Performance Considerations

- Display fields are frequently accessed, so ensure they're indexed appropriately
- Consider caching display field values in frequently-used contexts
- For large datasets, optimize queries that join key fields with their display fields

### Maintenance

- Keep display fields up to date when key field values change
- Provide UI for users to maintain display field values
- Consider validation rules to ensure display fields are meaningful and unique when appropriate
