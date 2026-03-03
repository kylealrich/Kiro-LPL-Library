# Audit Index Fields

Audit Index Fields specify which fields should be added to the audit index (snapshot table) for optimized as-of-date queries.

## Purpose

Audit Index Fields are used to:
- Optimize as-of-date query performance
- Create snapshot tables for historical data access
- Improve query speed for audited business classes
- Control which fields are indexed for historical queries
- Balance performance vs storage requirements

## Syntax

```
Audit Index Fields
    <FullFieldName>…
```

## Default Behavior

By default, all fields in Sets are put in the audit index. The Audit Index Fields section allows you to explicitly specify additional fields or override the default behavior.

## How Audit Index Works

When a business class implements the Auditing pattern:
1. An audit log table stores all changes
2. An audit index (snapshot table) stores the current state as of specific dates
3. The audit index optimizes as-of-date queries
4. Fields specified in Audit Index Fields are included in the snapshot table

## Examples

### Simple Audit Index Fields

```
CUSTOMER is a BusinessClass
    Patterns
        implements Auditing
    
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNAME is Alpha size 50
        CUSTOMERTYPE is Alpha size 20
        CREDITLIMIT is Decimal size 15.2
        ACTIVE is Boolean
    
    Audit Index Fields
        CUSTOMERNAME
        CUSTOMERTYPE
        ACTIVE
```

### Audit Index with Key Fields

```
ORDER is a BusinessClass
    Patterns
        implements Auditing
        implements EffectiveDated
    
    Persistent Fields
        ORDER is Order
        ORDERDATE is Date
        CUSTOMER is Customer
        SALESPERSON is Employee
        STATUS is OrderStatus
        TOTAL is Decimal size 15.2
    
    Audit Index Fields
        ORDERDATE
        CUSTOMER
        SALESPERSON
        STATUS
```

### Audit Index for Financial Data

```
TRANSACTION is a BusinessClass
    Patterns
        implements Auditing
    
    Persistent Fields
        TRANSACTION is Transaction
        TRANSACTIONDATE is Date
        ACCOUNT is Account
        AMOUNT is Decimal size 15.2
        DESCRIPTION is Alpha size 255
        POSTED is Boolean
    
    Audit Index Fields
        TRANSACTIONDATE
        ACCOUNT
        AMOUNT
        POSTED
```

### Audit Index with Derived Fields

```
INVOICE is a BusinessClass
    Patterns
        implements Auditing
    
    Persistent Fields
        INVOICE is Invoice
        INVOICEDATE is Date
        CUSTOMER is Customer
        SUBTOTAL is Decimal size 15.2
        TAXAMOUNT is Decimal size 15.2
    
    Derived Fields
        TOTAL is Decimal size 15.2
            Total = Subtotal + TaxAmount
    
    Audit Index Fields
        INVOICEDATE
        CUSTOMER
        SUBTOTAL
        TAXAMOUNT
        TOTAL
```

### Selective Audit Index

```
EMPLOYEE is a BusinessClass
    Patterns
        implements Auditing
    
    Persistent Fields
        EMPLOYEE is Employee
        EMPLOYEENAME is Alpha size 50
        DEPARTMENT is Department
        JOBTITLE is Alpha size 50
        HIREDATE is Date
        SALARY is Decimal size 15.2
        ACTIVE is Boolean
        NOTES is Text
    
    Audit Index Fields
        EMPLOYEENAME
        DEPARTMENT
        JOBTITLE
        HIREDATE
        ACTIVE
        // Exclude SALARY and NOTES from audit index for performance
```

### Audit Index with Group Fields

```
VENDOR is a BusinessClass
    Patterns
        implements Auditing
    
    Persistent Fields
        VENDOR is Vendor
        VENDORNAME is Alpha size 50
        CONTACTINFO is ContactInfo group
        PAYMENTTERMS is PaymentTerms group
        ACTIVE is Boolean
    
    Audit Index Fields
        VENDORNAME
        CONTACTINFO.ContactName
        CONTACTINFO.Phone
        CONTACTINFO.Email
        ACTIVE
```

### Audit Index for Effective Dated Records

```
PRICELIST is a BusinessClass
    Patterns
        implements Auditing
        implements EffectiveDated
    
    Persistent Fields
        PRICELIST is PriceList
        PRODUCT is Product
        EFFECTIVEDATE is Date
        PRICE is Decimal size 15.2
        CURRENCY is Alpha size 3
    
    Audit Index Fields
        PRODUCT
        EFFECTIVEDATE
        PRICE
        CURRENCY
```

## Use Cases

### Historical Reporting

Optimize queries that need historical data:

```
Audit Index Fields
    ReportDate
    Department
    Amount
    Status
```

### Compliance and Audit

Include fields required for compliance reporting:

```
Audit Index Fields
    TransactionDate
    Account
    Amount
    ApprovedBy
    ApprovalDate
```

### Performance Optimization

Include frequently queried fields:

```
Audit Index Fields
    CustomerName
    CustomerType
    Region
    Active
```

### Trend Analysis

Include fields used in trend analysis:

```
Audit Index Fields
    Period
    Product
    SalesAmount
    Quantity
```

## Best Practices

### Field Selection

- Include fields frequently used in as-of-date queries
- Include fields used in WHERE clauses
- Include fields used in JOIN conditions
- Include fields used in reporting
- Exclude large text or binary fields

### Performance Considerations

- Balance query performance vs storage requirements
- Include only necessary fields
- Consider query patterns
- Monitor audit index size
- Test query performance

### Storage Management

- Audit index tables can grow large
- Include only essential fields
- Consider data retention policies
- Monitor disk space usage
- Plan for growth

### Maintenance

- Review audit index fields periodically
- Add fields as query patterns change
- Remove unused fields
- Document field selection rationale
- Test impact of changes

### Field Types

- Include key fields for joins
- Include date fields for time-based queries
- Include status fields for filtering
- Include amount fields for aggregation
- Exclude large text fields unless necessary

## Common Patterns

### Financial Audit Pattern

```
Audit Index Fields
    TransactionDate
    Account
    Amount
    PostedDate
    PostedBy
```

### Customer History Pattern

```
Audit Index Fields
    CustomerName
    CustomerType
    Status
    CreditLimit
    LastOrderDate
```

### Inventory Snapshot Pattern

```
Audit Index Fields
    Product
    Location
    Quantity
    UnitCost
    LastMovementDate
```

### Employee History Pattern

```
Audit Index Fields
    EmployeeName
    Department
    JobTitle
    Salary
    Status
```

## Relationship with Other Patterns

### Auditing Pattern

Audit Index Fields are only relevant when the business class implements the Auditing pattern:

```
Patterns
    implements Auditing
```

### Disable Audit Index

You can disable the audit index entirely:

```
Patterns
    implements Auditing
    disable AuditIndex
```

### Effective Dating

Audit Index Fields work with EffectiveDated pattern:

```
Patterns
    implements Auditing
    implements EffectiveDated
```

## Performance Impact

### Query Performance

**Benefits:**
- Faster as-of-date queries
- Improved historical reporting
- Better query optimization
- Reduced audit log scanning

**Considerations:**
- More fields = larger index
- Storage requirements increase
- Index maintenance overhead
- Backup/restore time increases

### Write Performance

**Impact:**
- Audit index updated on changes
- Additional write overhead
- More disk I/O
- Longer transaction times

**Mitigation:**
- Include only necessary fields
- Consider batch updates
- Monitor write performance
- Optimize as needed

## Storage Considerations

### Audit Index Size

The audit index size depends on:
- Number of fields included
- Field sizes
- Number of records
- Change frequency
- Retention period

### Calculation Example

```
Audit Index Size ≈ 
    (Number of Records) × 
    (Number of Versions per Record) × 
    (Sum of Field Sizes)
```

### Storage Planning

- Estimate audit index size
- Plan for growth
- Monitor actual usage
- Adjust retention policies
- Consider archiving strategies

## Monitoring and Maintenance

### Monitoring

- Track audit index size
- Monitor query performance
- Review disk space usage
- Check index fragmentation
- Analyze query patterns

### Maintenance Tasks

- Rebuild indexes periodically
- Archive old audit data
- Purge unnecessary records
- Update statistics
- Optimize queries

## Related Concepts

### Auditing Pattern

Audit Index Fields require the Auditing pattern to be implemented.

### As-Of-Date Processing

Audit Index optimizes as-of-date queries for historical data access.

### Sets

By default, fields used in Sets are included in the audit index.

### Effective Dating

Works with EffectiveDated pattern for temporal data management.

## Troubleshooting

### Common Issues

1. **Audit index too large**: Include fewer fields or implement retention policy
2. **Slow queries**: Add frequently queried fields to audit index
3. **Write performance issues**: Reduce number of indexed fields
4. **Missing historical data**: Ensure audit index is enabled
5. **Disk space issues**: Implement archiving or purging strategy

### Debugging Tips

- Review query execution plans
- Check audit index size
- Monitor query performance
- Analyze field usage patterns
- Test with and without audit index
- Review audit log vs audit index queries

## Best Practice Checklist

- [ ] Include fields used in as-of-date queries
- [ ] Include key fields for joins
- [ ] Include date fields for time-based queries
- [ ] Exclude large text or binary fields
- [ ] Document field selection rationale
- [ ] Test query performance
- [ ] Monitor audit index size
- [ ] Plan for storage growth
- [ ] Review periodically
- [ ] Consider retention policies

## Summary

Audit Index Fields provide a mechanism to:
- Optimize as-of-date query performance
- Control which fields are included in snapshot tables
- Balance query performance vs storage requirements
- Support historical reporting and analysis
- Enable efficient temporal data access

Proper configuration of Audit Index Fields is essential for maintaining good performance in audited business classes while managing storage requirements effectively.
