# DataSource Mapping

DataSource Mapping defines how a business class maps its CRUD operations to external data sources through web service interfaces.

## Syntax

```
DataSource Mapping
    <DataSourceCRUDType> is <DataSourceAction>
        <InvokeRule>
```

## Data Source Types

```
DataSourceType ::= ( MI    // MI uses a .mi interface definition to fulfill the contract
                   | WSI)  // WSI uses a .wsi interface definition to fulfill the contract
```

| Type | Description | Interface File |
|------|-------------|----------------|
| `MI` | M3 Interface - uses M3 interface definition | `.mi` file |
| `WSI` | Web Service Interface - uses web service interface definition | `.wsi` file |

## CRUD Types

```
DataSourceCRUDType ::= ( set
                       | find
                       | create
                       | update
                       | delete)
```

| CRUD Type | Description | Purpose |
|-----------|-------------|---------|
| `set` | Retrieve a set of records | Query multiple records |
| `find` | Find a specific record | Query single record by key |
| `create` | Create a new record | Insert operation |
| `update` | Update an existing record | Modify operation |
| `delete` | Delete a record | Remove operation |

## Data Source Action

```
DataSourceAction ::= <WebserviceInterface>.<ActionName>
```

The action specifies which web service interface and action to invoke for the CRUD operation.

## Purpose

DataSource Mapping allows a business class to fulfill its contract through an external data source rather than using the standard database persistence. This enables:
- Integration with external systems
- Access to M3 data
- Web service-based data access
- Abstraction of data source implementation

## Examples

### Complete DataSource Mapping

```
CUSTOMER is a BusinessClass
    owned by AR
    
    Patterns
        implements MI DataSource
    
    DataSource Mapping
        set is CustomerInterface.GetCustomers
            // Invoke rules for mapping
        
        find is CustomerInterface.GetCustomer
            // Invoke rules for mapping
        
        create is CustomerInterface.CreateCustomer
            // Invoke rules for mapping
        
        update is CustomerInterface.UpdateCustomer
            // Invoke rules for mapping
        
        delete is CustomerInterface.DeleteCustomer
            // Invoke rules for mapping
    
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNAME is Alpha size 50
```

### MI DataSource Example

```
ITEM is a BusinessClass
    owned by Inventory
    
    Patterns
        implements MI DataSource
    
    DataSource Mapping
        set is M3ItemInterface.ListItems
            map Company to CONO
            map Item to ITNO
            map ItemDescription from ITDS
        
        find is M3ItemInterface.GetItem
            map Company to CONO
            map Item to ITNO
            map ItemDescription from ITDS
            map UnitPrice from SAPR
        
        create is M3ItemInterface.AddItem
            map Company to CONO
            map Item to ITNO
            map ItemDescription to ITDS
        
        update is M3ItemInterface.UpdateItem
            map Company to CONO
            map Item to ITNO
            map ItemDescription to ITDS
        
        delete is M3ItemInterface.DeleteItem
            map Company to CONO
            map Item to ITNO
    
    Persistent Fields
        COMPANY is Company
        ITEM is Alpha size 20
        ITEMDESCRIPTION is Alpha size 100
        UNITPRICE is Decimal size 15.2
```

### WSI DataSource Example

```
PRODUCT is a BusinessClass
    owned by Catalog
    
    Patterns
        implements WSI DataSource
    
    DataSource Mapping
        set is ProductWebService.GetProducts
            map Category to categoryId
            map ProductList from products
        
        find is ProductWebService.GetProductById
            map ProductId to id
            map ProductName from name
            map ProductDescription from description
            map Price from unitPrice
        
        create is ProductWebService.CreateProduct
            map ProductName to name
            map ProductDescription to description
            map Price to unitPrice
        
        update is ProductWebService.UpdateProduct
            map ProductId to id
            map ProductName to name
            map ProductDescription to description
            map Price to unitPrice
        
        delete is ProductWebService.DeleteProduct
            map ProductId to id
    
    Persistent Fields
        PRODUCTID is Alpha size 20
        PRODUCTNAME is Alpha size 100
        PRODUCTDESCRIPTION is Alpha size 255
        PRICE is Decimal size 15.2
```

### Partial DataSource Mapping

```
VENDOR is a BusinessClass
    owned by AP
    
    Patterns
        implements WSI DataSource
    
    DataSource Mapping
        // Only map read operations to external source
        set is VendorWebService.GetVendors
            map VendorList from vendors
        
        find is VendorWebService.GetVendor
            map Vendor to vendorId
            map VendorName from name
        
        // Create, update, delete use standard database operations
    
    Persistent Fields
        VENDOR is Vendor
        VENDORNAME is Alpha size 50
```

### Read-Only DataSource

```
EXCHANGERATE is a BusinessClass
    owned by Currency
    
    Patterns
        implements WSI DataSource
        implements ReadOnly
    
    DataSource Mapping
        set is CurrencyWebService.GetExchangeRates
            map FromCurrency to fromCurrency
            map ToCurrency to toCurrency
            map RateList from rates
        
        find is CurrencyWebService.GetExchangeRate
            map FromCurrency to fromCurrency
            map ToCurrency to toCurrency
            map EffectiveDate to effectiveDate
            map Rate from exchangeRate
    
    Persistent Fields
        FROMCURRENCY is Alpha size 3
        TOCURRENCY is Alpha size 3
        EFFECTIVEDATE is Date
        RATE is Decimal size 15.6
```

## Invoke Rules

Within each CRUD mapping, you can specify invoke rules to map fields between the business class and the external interface. Common mapping patterns include:

### Map To (Outbound)
```
map <FieldName> to <InterfaceParameter>
```
Maps a business class field to an interface input parameter.

### Map From (Inbound)
```
map <FieldName> from <InterfaceField>
```
Maps an interface output field to a business class field.

### Bidirectional Mapping
```
map <FieldName> to <InterfaceParameter>
map <FieldName> from <InterfaceField>
```

## Best Practices

### Interface Design

- Design clear, consistent interface contracts
- Document interface parameters and return values
- Version interfaces appropriately
- Handle errors gracefully

### CRUD Mapping

- Map all required CRUD operations
- Provide appropriate field mappings
- Handle optional fields correctly
- Test all CRUD operations thoroughly

### Performance

- Consider caching for read-heavy operations
- Batch operations when possible
- Monitor external service performance
- Implement timeouts appropriately

### Error Handling

- Handle external service errors
- Provide meaningful error messages
- Implement retry logic when appropriate
- Log integration errors

### Data Consistency

- Ensure data type compatibility
- Handle data format differences
- Validate data before sending
- Handle null/empty values appropriately

### Security

- Secure interface credentials
- Use appropriate authentication
- Encrypt sensitive data
- Follow security best practices

### Testing

- Test all CRUD operations
- Test error scenarios
- Test with realistic data volumes
- Test timeout scenarios
- Test network failure scenarios

### Documentation

- Document interface dependencies
- Document field mappings
- Document error handling
- Document performance characteristics

## Common Patterns

### M3 Integration Pattern

```
DataSource Mapping
    set is M3Interface.List
        map Company to CONO
        map Division to DIVI
        map ResultSet from records
    
    find is M3Interface.Get
        map Company to CONO
        map Key to KEY
        map Fields from record
    
    create is M3Interface.Add
        map Company to CONO
        map Fields to record
    
    update is M3Interface.Update
        map Company to CONO
        map Key to KEY
        map Fields to record
    
    delete is M3Interface.Delete
        map Company to CONO
        map Key to KEY
```

### REST API Pattern

```
DataSource Mapping
    set is RestInterface.GetAll
        map QueryParams to params
        map ResultList from items
    
    find is RestInterface.GetById
        map Id to id
        map Entity from item
    
    create is RestInterface.Post
        map Entity to item
        map CreatedId from id
    
    update is RestInterface.Put
        map Id to id
        map Entity to item
    
    delete is RestInterface.Delete
        map Id to id
```

### Hybrid Pattern (Some operations external, some local)

```
DataSource Mapping
    // Read from external source
    set is ExternalInterface.GetRecords
        map Filters to query
        map Results from data
    
    find is ExternalInterface.GetRecord
        map Key to id
        map Record from data
    
    // Write to local database (no mapping)
    // create, update, delete use standard persistence
```

## Related Concepts

### Web Service Interfaces

DataSource Mapping relies on web service interface definitions (`.wsi` or `.mi` files) that define the contract for external communication.

### Business Class Patterns

DataSource Mapping is typically used with:
- `implements MI DataSource` pattern
- `implements WSI DataSource` pattern
- `implements ReadOnly` pattern (for read-only external data)
- `implements ForeignTable` pattern

### Invoke Rules

The mapping syntax uses invoke rules to specify how data flows between the business class and the external interface.

## Troubleshooting

### Common Issues

1. **Mapping Errors**: Ensure field names and types match
2. **Connection Failures**: Check network and service availability
3. **Authentication Errors**: Verify credentials and permissions
4. **Data Format Mismatches**: Ensure compatible data formats
5. **Timeout Issues**: Adjust timeout settings appropriately

### Debugging Tips

- Enable detailed logging
- Test interfaces independently
- Verify field mappings
- Check data transformations
- Monitor service responses
- Review error messages carefully
