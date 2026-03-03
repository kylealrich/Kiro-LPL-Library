# Field Groups

Field Groups provide arbitrary grouping of fields within a business class for organizational and UI purposes.

## Purpose

Field Groups are used to:
- Organize related fields logically
- Control field display in UI forms
- Group fields for validation
- Simplify field management
- Improve code readability

## Syntax

```
Field Groups
    <FieldGroupName>
        [include [(persistent | auditable | derived)] user fields]
        <FullFieldName>…
```

## Field Group Attributes

### Field Group Name

```
<FieldGroupName>
```

A descriptive name for the field group.

### Include User Fields

```
[include [(persistent | auditable | derived)] user fields]
```

Optionally includes user-defined fields in the group:
- **persistent user fields**: Includes persistent user fields
- **auditable user fields**: Includes auditable user fields
- **derived user fields**: Includes derived user fields
- **user fields** (no qualifier): Includes all user fields

### Field List

```
<FullFieldName>…
```

List of fields to include in the group. Can reference:
- Persistent fields
- Transient fields
- Derived fields
- Local fields
- User fields

## Examples

### Simple Field Group

```
CUSTOMER is a BusinessClass
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNAME is Alpha size 50
        ADDRESS is Alpha size 100
        CITY is Alpha size 30
        STATE is Alpha size 2
        ZIP is Alpha size 10
        PHONE is Alpha size 20
        EMAIL is Alpha size 100
    
    Field Groups
        BasicInfo
            CUSTOMER
            CUSTOMERNAME
        
        AddressInfo
            ADDRESS
            CITY
            STATE
            ZIP
        
        ContactInfo
            PHONE
            EMAIL
```

### Field Group with User Fields

```
EMPLOYEE is a BusinessClass
    Persistent Fields
        EMPLOYEE is Employee
        EMPLOYEENAME is Alpha size 50
        HIREDATE is Date
        DEPARTMENT is Department
        SALARY is Decimal size 15.2
    
    Field Groups
        EmployeeDetails
            EMPLOYEE
            EMPLOYEENAME
            HIREDATE
            DEPARTMENT
            include persistent user fields
        
        CompensationInfo
            SALARY
            include auditable user fields
```

### Multiple Field Groups

```
ORDER is a BusinessClass
    Persistent Fields
        ORDER is Order
        ORDERDATE is Date
        CUSTOMER is Customer
        SALESPERSON is Employee
        SUBTOTAL is Decimal size 15.2
        TAX is Decimal size 15.2
        TOTAL is Decimal size 15.2
        STATUS is OrderStatus
        SHIPDATE is Date
        SHIPMETHOD is Alpha size 20
    
    Field Groups
        OrderHeader
            ORDER
            ORDERDATE
            CUSTOMER
            SALESPERSON
        
        OrderTotals
            SUBTOTAL
            TAX
            TOTAL
        
        OrderStatus
            STATUS
        
        ShippingInfo
            SHIPDATE
            SHIPMETHOD
```

### Field Group with Derived Fields

```
INVOICE is a BusinessClass
    Persistent Fields
        INVOICE is Invoice
        INVOICEDATE is Date
        CUSTOMER is Customer
        SUBTOTAL is Decimal size 15.2
        TAXRATE is Decimal size 5.2
    
    Derived Fields
        TAXAMOUNT is Decimal size 15.2
            TaxAmount = Subtotal * TaxRate / 100
        
        TOTAL is Decimal size 15.2
            Total = Subtotal + TaxAmount
    
    Field Groups
        InvoiceHeader
            INVOICE
            INVOICEDATE
            CUSTOMER
        
        InvoiceAmounts
            SUBTOTAL
            TAXRATE
            TAXAMOUNT
            TOTAL
            include derived user fields
```

### Field Group for Form Sections

```
PRODUCT is a BusinessClass
    Persistent Fields
        PRODUCT is Product
        PRODUCTNAME is Alpha size 100
        PRODUCTDESCRIPTION is Alpha size 255
        CATEGORY is ProductCategory
        SUBCATEGORY is ProductSubCategory
        UNITPRICE is Decimal size 15.2
        COST is Decimal size 15.2
        WEIGHT is Decimal size 10.2
        DIMENSIONS is Alpha size 50
        ACTIVE is Boolean
    
    Field Groups
        BasicInfo
            PRODUCT
            PRODUCTNAME
            PRODUCTDESCRIPTION
            ACTIVE
        
        CategoryInfo
            CATEGORY
            SUBCATEGORY
        
        PricingInfo
            UNITPRICE
            COST
        
        PhysicalInfo
            WEIGHT
            DIMENSIONS
```

### Field Group with All User Field Types

```
PROJECT is a BusinessClass
    Persistent Fields
        PROJECT is Project
        PROJECTNAME is Alpha size 100
        STARTDATE is Date
        ENDDATE is Date
        MANAGER is Employee
        BUDGET is Decimal size 15.2
    
    Field Groups
        ProjectBasics
            PROJECT
            PROJECTNAME
            MANAGER
            include persistent user fields
        
        ProjectSchedule
            STARTDATE
            ENDDATE
            include auditable user fields
        
        ProjectFinancials
            BUDGET
            include derived user fields
```

### Nested Field References

```
VENDOR is a BusinessClass
    Persistent Fields
        VENDOR is Vendor
        VENDORNAME is Alpha size 50
        CONTACTINFO is ContactInfo group
        PAYMENTTERMS is PaymentTerms group
        ACTIVE is Boolean
    
    Field Groups
        VendorBasics
            VENDOR
            VENDORNAME
            ACTIVE
        
        VendorContact
            CONTACTINFO.ContactName
            CONTACTINFO.Phone
            CONTACTINFO.Email
        
        VendorPayment
            PAYMENTTERMS.TermsDays
            PAYMENTTERMS.DiscountPercent
```

## Use Cases

### UI Form Organization

Field Groups help organize fields into logical sections on forms:

```
Field Groups
    PersonalInfo
        FirstName
        LastName
        DateOfBirth
        SSN
    
    EmploymentInfo
        HireDate
        Department
        JobTitle
        Supervisor
    
    CompensationInfo
        Salary
        BonusEligible
        BenefitsPlan
```

### Validation Grouping

Group fields that need to be validated together:

```
Field Groups
    RequiredFields
        CustomerName
        CustomerType
        CreditLimit
    
    AddressValidation
        Street
        City
        State
        ZipCode
    
    ContactValidation
        Phone
        Email
```

### Audit Grouping

Group fields for audit purposes:

```
Field Groups
    AuditableFinancials
        Revenue
        Cost
        Profit
        include auditable user fields
    
    NonAuditableInfo
        Notes
        InternalComments
```

### User Field Management

Organize user fields by type:

```
Field Groups
    CustomPersistentFields
        include persistent user fields
    
    CustomCalculatedFields
        include derived user fields
    
    CustomAuditFields
        include auditable user fields
```

## Best Practices

### Naming

- Use descriptive field group names
- Follow consistent naming conventions
- Use names that indicate the group's purpose
- Consider using suffixes like "Info", "Details", "Data"
- Keep names clear and concise

### Organization

- Group related fields together logically
- Keep field groups focused and cohesive
- Avoid overly large field groups
- Consider UI layout when grouping
- Balance between too many and too few groups

### User Fields

- Use appropriate user field qualifiers (persistent, auditable, derived)
- Document which user fields are included
- Consider the impact on forms and reports
- Test with actual user fields defined
- Plan for user field growth

### Maintenance

- Review field groups when adding new fields
- Keep field groups up to date
- Remove obsolete field references
- Document field group purposes
- Consider versioning for major changes

### UI Design

- Align field groups with form sections
- Consider field group order
- Group fields by user workflow
- Balance information density
- Test with actual users

### Performance

- Field groups don't impact database performance
- Consider UI rendering with large groups
- Test form load times
- Monitor user experience
- Optimize as needed

## Common Patterns

### Header-Detail Pattern

```
Field Groups
    Header
        OrderNumber
        OrderDate
        Customer
    
    Details
        LineItems
        Subtotal
        Tax
        Total
```

### Basic-Advanced Pattern

```
Field Groups
    BasicInfo
        Name
        Description
        Category
    
    AdvancedInfo
        TechnicalSpecs
        Certifications
        Compliance
        include derived user fields
```

### Required-Optional Pattern

```
Field Groups
    RequiredFields
        CustomerName
        CustomerType
        EffectiveDate
    
    OptionalFields
        SecondaryContact
        AlternateAddress
        SpecialInstructions
        include persistent user fields
```

### Financial Pattern

```
Field Groups
    Amounts
        BaseAmount
        DiscountAmount
        TaxAmount
        TotalAmount
    
    Accounting
        AccountCode
        CostCenter
        Project
        include auditable user fields
```

## Field Group Usage

### In Forms

Field groups are commonly used to organize form layouts:
- Each field group becomes a section or panel
- Fields within a group are displayed together
- Groups can be collapsible or tabbed
- Order of groups determines display order

### In Reports

Field groups can organize report columns:
- Group related data together
- Create logical report sections
- Control column ordering
- Simplify report design

### In Validation

Field groups can be used for validation:
- Validate all fields in a group together
- Apply group-level validation rules
- Control validation order
- Provide group-level error messages

### In APIs

Field groups can control API responses:
- Return specific field groups
- Reduce payload size
- Support partial updates
- Enable field-level security

## Related Concepts

### Persistent Fields

Field groups reference persistent fields defined in the business class.

### Transient Fields

Field groups can include transient fields for UI purposes.

### Derived Fields

Field groups can include derived fields for display.

### User Fields

Field groups can dynamically include user-defined fields.

### Form Definitions

Field groups are often used in form definitions to organize UI layout.

## Limitations

- Field groups are organizational only
- They don't affect database structure
- They don't enforce validation by themselves
- They don't control field access directly
- They don't impact query performance

## Troubleshooting

### Common Issues

1. **Field not found**: Ensure field is defined in business class
2. **User fields not appearing**: Verify user fields are defined
3. **Incorrect field order**: Check field group definition order
4. **Missing fields in UI**: Verify form uses correct field group
5. **Duplicate fields**: Check for fields in multiple groups

### Debugging Tips

- Verify all referenced fields exist
- Check field group names are unique
- Test with and without user fields
- Review form definitions using field groups
- Validate field references are correct
- Check for typos in field names

## Summary

Field Groups provide a flexible way to:
- Organize fields logically within a business class
- Control UI form layout and organization
- Include user-defined fields dynamically
- Improve code readability and maintenance
- Support various organizational patterns

Proper use of Field Groups enhances the structure and usability of business class definitions while maintaining flexibility for user customization.
