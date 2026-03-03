# Webservice Interface Definitions

## Overview

Webservice Interface definitions (.wsi files) enable LPL applications to interact with external web services. These definitions specify the connection details, authentication, and data mapping required to execute webservice operations from LPL code. They support multiple service styles including REST, SOAP, and custom XML-based protocols.

## Syntax

```
<WebserviceInterface> is a WebserviceInterface
    wsdlURL is <Literal>
    endpoint is <Literal>
    service is <Literal>
    service style is <WSIServiceStyle>
    authentication is <WSIAuthentication>
    
    <ActionName> is a[n] <WSIActionType> Action …
        [method is <Literal>]
        Input Record
            namespace is <Literal>
            <FieldName> [<DataDefinition>]…
                [attribute name is <Literal>]
                [element name is <Literal>]
                [required]
        Output Record
            <FieldName> [<DataDefinition>]…
                [attribute name is <Literal>]
                [element name is <Literal>]
                [repeating]
```

## Core Components

### Webservice Interface Attributes

| Attribute | Description | Required |
|-----------|-------------|----------|
| `wsdlURL` | URL to the WSDL definition document | Yes |
| `endpoint` | Service endpoint URL where requests are sent | Yes |
| `service` | Service name identifier | Yes |
| `service style` | Protocol and encoding style (SOAP, REST, etc.) | Yes |
| `authentication` | Authentication method (basic, token) | Yes |

### Service Styles (WSIServiceStyle)

| Style | Protocol | Description | Processing |
|-------|----------|-------------|------------|
| `RESTGET` | REST | HTTP GET with XML response | Synchronous |
| `RESTPOST` | REST | HTTP POST with one-level XML request | Synchronous |
| `SOAP_V1` | SOAP | RPC encoding (deprecated) | Synchronous |
| `SOAP_V2` | SOAP | Document/Literal encoding with namespaces | Synchronous |
| `XMLHTTP` | Custom | Proprietary XML request/response | Synchronous |
| `FACEBOOK` | Custom | Facebook API integration | Synchronous |

### Authentication Types (WSIAuthentication)

| Type | Description |
|------|-------------|
| `basic` | HTTP Basic Authentication with username/password |
| `token` | Token-based authentication passed in request |

### Action Types (WSIActionType)

| Type | Purpose | Processing |
|------|---------|------------|
| `Create` | Create new resource | Synchronous |
| `Update` | Update existing resource | Synchronous |
| `Delete` | Delete resource | Synchronous |
| `Find` | Search for resources | Synchronous |
| `Instance` | Retrieve single instance | Synchronous |
| `List` | Retrieve multiple instances | Synchronous |


## Field Mapping Attributes

### Input Record Fields

| Attribute | Description |
|-----------|-------------|
| `namespace` | XML namespace for the input record |
| `attribute name` | Maps field to XML attribute instead of element |
| `element name` | Custom XML element name (overrides field name) |
| `required` | Marks field as required in the request |

### Output Record Fields

| Attribute | Description |
|-----------|-------------|
| `attribute name` | Maps field from XML attribute instead of element |
| `element name` | Custom XML element name to read from response |
| `repeating` | Indicates field represents an array/collection |

## Examples

### Example 1: Amazon Web Services SOAP Interface

```lpl
AmazonECommerce is a WebserviceInterface
    wsdlURL is "http://webservices.amazon.com/AWSECommerceService/AWSECommerceService.wsdl"
    endpoint is "http://soap.amazon.com/onca/soap?Service=AWSECommerceService"
    service is "AWSECommerceService"
    service style is SOAP_V2
    authentication is basic
    
    ItemSearch is a Find Action
        Input Record
            namespace is "http://webservices.amazon.com/AWSECommerceService/2006-11-14"
            SearchIndex is a String
                element name is "SearchIndex"
                required
            Keywords is a String
                element name is "Keywords"
                required
            ResponseGroup is a String
                element name is "ResponseGroup"
        Output Record
            Items is a String
                element name is "Items"
                repeating
            TotalResults is an Integer
                element name is "TotalResults"
            TotalPages is an Integer
                element name is "TotalPages"
```

### Example 2: REST GET Weather Service

```lpl
WeatherAPI is a WebserviceInterface
    wsdlURL is "http://api.weather.com/service?wsdl"
    endpoint is "http://api.weather.com/v1/forecast"
    service is "WeatherService"
    service style is RESTGET
    authentication is token
    
    GetForecast is an Instance Action
        Input Record
            namespace is "http://api.weather.com/v1"
            ZipCode is a String
                element name is "zip"
                required
            Units is a String
                element name is "units"
        Output Record
            Temperature is a Decimal
                element name is "temp"
            Humidity is an Integer
                element name is "humidity"
            Conditions is a String
                element name is "conditions"
            ForecastDate is a Date
                element name is "date"
```

### Example 3: REST POST Customer Creation

```lpl
CustomerService is a WebserviceInterface
    wsdlURL is "http://crm.example.com/api/customers?wsdl"
    endpoint is "http://crm.example.com/api/customers"
    service is "CustomerManagement"
    service style is RESTPOST
    authentication is basic
    
    CreateCustomer is a Create Action
        Input Record
            namespace is "http://crm.example.com/api/v2"
            FirstName is a String
                element name is "firstName"
                required
            LastName is a String
                element name is "lastName"
                required
            Email is a String
                element name is "email"
                required
            Phone is a String
                element name is "phone"
            CompanyName is a String
                element name is "company"
        Output Record
            CustomerId is a String
                element name is "id"
            Status is a String
                element name is "status"
            CreatedDate is a Timestamp
                element name is "createdAt"
```

### Example 4: XMLHTTP Proprietary Service

```lpl
InventorySync is a WebserviceInterface
    wsdlURL is "http://warehouse.internal.com/inventory/service.wsdl"
    endpoint is "http://warehouse.internal.com/inventory/sync"
    service is "InventoryService"
    service style is XMLHTTP
    authentication is token
    
    UpdateStock is an Update Action
        method is "POST"
        Input Record
            namespace is "http://warehouse.internal.com/inventory/v1"
            ItemCode is a String
                element name is "itemCode"
                required
            Quantity is an Integer
                element name is "qty"
                required
            Location is a String
                element name is "location"
                required
            TransactionType is a String
                element name is "txnType"
        Output Record
            Success is a Boolean
                element name is "success"
            NewQuantity is an Integer
                element name is "newQty"
            Message is a String
                element name is "message"
```

### Example 5: List Action with Repeating Elements

```lpl
ProductCatalog is a WebserviceInterface
    wsdlURL is "http://catalog.example.com/products?wsdl"
    endpoint is "http://catalog.example.com/api/products"
    service is "ProductService"
    service style is SOAP_V2
    authentication is basic
    
    ListProducts is a List Action
        Input Record
            namespace is "http://catalog.example.com/api/v1"
            Category is a String
                element name is "category"
            MinPrice is a Decimal
                element name is "minPrice"
            MaxPrice is a Decimal
                element name is "maxPrice"
        Output Record
            ProductId is a String
                element name is "productId"
                repeating
            ProductName is a String
                element name is "name"
                repeating
            Price is a Decimal
                element name is "price"
                repeating
            InStock is a Boolean
                element name is "inStock"
                repeating
```

### Example 6: Delete Action with Minimal Response

```lpl
OrderManagement is a WebserviceInterface
    wsdlURL is "http://orders.example.com/service?wsdl"
    endpoint is "http://orders.example.com/api/orders"
    service is "OrderService"
    service style is RESTPOST
    authentication is token
    
    CancelOrder is a Delete Action
        Input Record
            namespace is "http://orders.example.com/api/v1"
            OrderNumber is a String
                element name is "orderNumber"
                required
            Reason is a String
                element name is "cancellationReason"
        Output Record
            Success is a Boolean
                element name is "success"
            Message is a String
                element name is "message"
```

### Example 7: XML Attributes vs Elements

```lpl
ShippingService is a WebserviceInterface
    wsdlURL is "http://shipping.example.com/rates?wsdl"
    endpoint is "http://shipping.example.com/api/rates"
    service is "ShippingRates"
    service style is SOAP_V2
    authentication is basic
    
    GetRate is an Instance Action
        Input Record
            namespace is "http://shipping.example.com/api/v1"
            ShipmentId is a String
                attribute name is "id"
                required
            Origin is a String
                element name is "origin"
                required
            Destination is a String
                element name is "destination"
                required
            Weight is a Decimal
                element name is "weight"
                required
        Output Record
            RateId is a String
                attribute name is "id"
            Cost is a Decimal
                element name is "cost"
            Currency is a String
                attribute name is "currency"
            DeliveryDays is an Integer
                element name is "deliveryDays"
```

### Example 8: Facebook API Integration

```lpl
FacebookIntegration is a WebserviceInterface
    wsdlURL is "https://graph.facebook.com/v12.0?wsdl"
    endpoint is "https://graph.facebook.com/v12.0"
    service is "FacebookGraph"
    service style is FACEBOOK
    authentication is token
    
    PostMessage is a Create Action
        Input Record
            namespace is "https://graph.facebook.com/v12.0"
            Message is a String
                element name is "message"
                required
            Link is a String
                element name is "link"
            PageId is a String
                element name is "pageId"
                required
        Output Record
            PostId is a String
                element name is "id"
            CreatedTime is a Timestamp
                element name is "created_time"
```

### Example 9: Multiple Actions in One Interface

```lpl
PaymentGateway is a WebserviceInterface
    wsdlURL is "https://payments.example.com/gateway?wsdl"
    endpoint is "https://payments.example.com/api/v2"
    service is "PaymentProcessor"
    service style is SOAP_V2
    authentication is token
    
    AuthorizePayment is a Create Action
        Input Record
            namespace is "https://payments.example.com/api/v2"
            Amount is a Decimal
                element name is "amount"
                required
            Currency is a String
                element name is "currency"
                required
            CardNumber is a String
                element name is "cardNumber"
                required
            CVV is a String
                element name is "cvv"
                required
        Output Record
            AuthorizationCode is a String
                element name is "authCode"
            TransactionId is a String
                element name is "transactionId"
            Status is a String
                element name is "status"
    
    CapturePayment is an Update Action
        Input Record
            namespace is "https://payments.example.com/api/v2"
            TransactionId is a String
                element name is "transactionId"
                required
            Amount is a Decimal
                element name is "amount"
                required
        Output Record
            CaptureId is a String
                element name is "captureId"
            Status is a String
                element name is "status"
            CapturedAmount is a Decimal
                element name is "capturedAmount"
    
    RefundPayment is a Delete Action
        Input Record
            namespace is "https://payments.example.com/api/v2"
            TransactionId is a String
                element name is "transactionId"
                required
            RefundAmount is a Decimal
                element name is "refundAmount"
        Output Record
            RefundId is a String
                element name is "refundId"
            Status is a String
                element name is "status"
```

### Example 10: Complex Nested Response Structure

```lpl
AddressValidation is a WebserviceInterface
    wsdlURL is "http://validation.usps.com/service?wsdl"
    endpoint is "http://validation.usps.com/api/validate"
    service is "AddressValidationService"
    service style is SOAP_V2
    authentication is basic
    
    ValidateAddress is an Instance Action
        Input Record
            namespace is "http://validation.usps.com/api/v1"
            Street1 is a String
                element name is "address1"
                required
            Street2 is a String
                element name is "address2"
            City is a String
                element name is "city"
                required
            State is a String
                element name is "state"
                required
            ZipCode is a String
                element name is "zip"
                required
        Output Record
            IsValid is a Boolean
                element name is "valid"
            StandardizedStreet1 is a String
                element name is "standardizedAddress1"
            StandardizedStreet2 is a String
                element name is "standardizedAddress2"
            StandardizedCity is a String
                element name is "standardizedCity"
            StandardizedState is a String
                element name is "standardizedState"
            StandardizedZip is a String
                element name is "standardizedZip"
            Plus4 is a String
                element name is "zip4"
            DeliveryPoint is a String
                element name is "deliveryPoint"
            ErrorMessage is a String
                element name is "error"
```


### Example 11: Token Authentication with Headers

```lpl
CloudStorage is a WebserviceInterface
    wsdlURL is "https://storage.cloud.com/api?wsdl"
    endpoint is "https://storage.cloud.com/api/v3/files"
    service is "FileStorageService"
    service style is RESTPOST
    authentication is token
    
    UploadFile is a Create Action
        Input Record
            namespace is "https://storage.cloud.com/api/v3"
            FileName is a String
                element name is "fileName"
                required
            FileContent is a String
                element name is "content"
                required
            FolderPath is a String
                element name is "path"
            ContentType is a String
                element name is "contentType"
        Output Record
            FileId is a String
                element name is "fileId"
            DownloadUrl is a String
                element name is "downloadUrl"
            Size is an Integer
                element name is "size"
            UploadedAt is a Timestamp
                element name is "uploadedAt"
```

### Example 12: Find Action with Search Criteria

```lpl
EmployeeDirectory is a WebserviceInterface
    wsdlURL is "http://hr.internal.com/directory?wsdl"
    endpoint is "http://hr.internal.com/api/employees/search"
    service is "EmployeeService"
    service style is SOAP_V2
    authentication is basic
    
    SearchEmployees is a Find Action
        Input Record
            namespace is "http://hr.internal.com/api/v1"
            FirstName is a String
                element name is "firstName"
            LastName is a String
                element name is "lastName"
            Department is a String
                element name is "department"
            Location is a String
                element name is "location"
            Status is a String
                element name is "status"
        Output Record
            EmployeeId is a String
                element name is "employeeId"
                repeating
            FullName is a String
                element name is "fullName"
                repeating
            Email is a String
                element name is "email"
                repeating
            Department is a String
                element name is "department"
                repeating
            Title is a String
                element name is "title"
                repeating
```

### Example 13: Custom Method Specification

```lpl
NotificationService is a WebserviceInterface
    wsdlURL is "http://notify.example.com/service?wsdl"
    endpoint is "http://notify.example.com/api/notifications"
    service is "NotificationAPI"
    service style is XMLHTTP
    authentication is token
    
    SendNotification is a Create Action
        method is "PUT"
        Input Record
            namespace is "http://notify.example.com/api/v1"
            RecipientId is a String
                element name is "recipient"
                required
            Subject is a String
                element name is "subject"
                required
            Body is a String
                element name is "body"
                required
            Priority is a String
                element name is "priority"
            Channel is a String
                element name is "channel"
        Output Record
            NotificationId is a String
                element name is "notificationId"
            Status is a String
                element name is "status"
            SentAt is a Timestamp
                element name is "sentAt"
```

### Example 14: Minimal Interface for Simple Service

```lpl
HealthCheck is a WebserviceInterface
    wsdlURL is "http://api.example.com/health?wsdl"
    endpoint is "http://api.example.com/health"
    service is "HealthService"
    service style is RESTGET
    authentication is basic
    
    CheckStatus is an Instance Action
        Input Record
            namespace is "http://api.example.com/v1"
        Output Record
            Status is a String
                element name is "status"
            Timestamp is a Timestamp
                element name is "timestamp"
            Version is a String
                element name is "version"
```

### Example 15: Complex Data Types with Multiple Namespaces

```lpl
TaxCalculation is a WebserviceInterface
    wsdlURL is "https://tax.service.com/calculate?wsdl"
    endpoint is "https://tax.service.com/api/v2/calculate"
    service is "TaxService"
    service style is SOAP_V2
    authentication is token
    
    CalculateTax is an Instance Action
        Input Record
            namespace is "https://tax.service.com/api/v2"
            TransactionId is a String
                attribute name is "id"
                required
            Amount is a Decimal
                element name is "amount"
                required
            Currency is a String
                element name is "currency"
                required
            FromCountry is a String
                element name is "fromCountry"
                required
            ToCountry is a String
                element name is "toCountry"
                required
            ProductCategory is a String
                element name is "category"
            TaxExempt is a Boolean
                element name is "exempt"
        Output Record
            TaxAmount is a Decimal
                element name is "taxAmount"
            TaxRate is a Decimal
                element name is "taxRate"
            TaxType is a String
                element name is "taxType"
            TaxJurisdiction is a String
                element name is "jurisdiction"
            CalculationDate is a Date
                element name is "calculationDate"
            BreakdownItems is a String
                element name is "breakdown"
                repeating
```

## Use Cases

### Integration Scenarios

1. **E-Commerce Integration**: Connect to payment gateways, shipping providers, and product catalogs
2. **CRM Synchronization**: Sync customer data with external CRM systems
3. **Financial Services**: Process payments, validate accounts, calculate taxes
4. **Logistics**: Track shipments, calculate rates, validate addresses
5. **Social Media**: Post updates, retrieve analytics, manage campaigns
6. **Data Validation**: Verify addresses, validate emails, check credit
7. **Notification Services**: Send emails, SMS, push notifications
8. **Document Management**: Upload, download, and manage documents
9. **Analytics**: Submit data to analytics platforms, retrieve reports
10. **Authentication**: Integrate with OAuth providers, SSO systems

### Service Style Selection

- **RESTGET**: Use for simple read operations, public APIs, caching-friendly requests
- **RESTPOST**: Use for create/update operations with simple data structures
- **SOAP_V2**: Use for enterprise services, complex data types, strict contracts
- **SOAP_V1**: Legacy support only (deprecated)
- **XMLHTTP**: Use for proprietary services that don't follow REST or SOAP standards
- **FACEBOOK**: Specialized for Facebook Graph API integration

## Best Practices

### Interface Design

1. **Use Descriptive Names**: Name interfaces and actions clearly to indicate their purpose
2. **Group Related Actions**: Keep related operations in the same interface
3. **Namespace Consistency**: Use consistent namespace patterns across actions
4. **Version in URLs**: Include API version in endpoint URLs for stability
5. **Document Requirements**: Comment required fields and validation rules

### Field Mapping

1. **Match Data Types**: Ensure LPL data types match service expectations
2. **Use Element Names**: Override field names when service uses different conventions
3. **Mark Required Fields**: Always mark required fields to catch errors early
4. **Handle Repeating Data**: Use repeating attribute for arrays and collections
5. **Attribute vs Element**: Use attributes for metadata, elements for data

### Error Handling

1. **Check Output Status**: Always include status or success fields in output
2. **Capture Error Messages**: Include error message fields in output records
3. **Validate Before Calling**: Validate input data before making service calls
4. **Handle Timeouts**: Consider service timeout scenarios in your logic
5. **Log Failures**: Implement logging for failed service calls

### Security

1. **Secure Endpoints**: Always use HTTPS for production endpoints
2. **Protect Credentials**: Store authentication tokens securely
3. **Validate Responses**: Validate data received from external services
4. **Limit Exposure**: Only expose necessary fields in input/output records
5. **Monitor Usage**: Track service calls for unusual patterns

### Performance

1. **Minimize Calls**: Batch operations when possible to reduce service calls
2. **Cache Results**: Cache frequently accessed, slowly changing data
3. **Async When Possible**: Consider asynchronous patterns for long operations
4. **Timeout Configuration**: Set appropriate timeouts for service calls
5. **Connection Pooling**: Reuse connections when making multiple calls

### Testing

1. **Test with Mock Services**: Use mock services during development
2. **Validate Mappings**: Test all field mappings thoroughly
3. **Error Scenarios**: Test error conditions and edge cases
4. **Load Testing**: Test performance under expected load
5. **Version Changes**: Test when service versions change

## Common Patterns

### Pattern 1: CRUD Operations Interface

```lpl
ResourceManagement is a WebserviceInterface
    wsdlURL is "http://api.example.com/resources?wsdl"
    endpoint is "http://api.example.com/api/resources"
    service is "ResourceService"
    service style is RESTPOST
    authentication is token
    
    CreateResource is a Create Action
        // Input/Output definitions
    
    GetResource is an Instance Action
        // Input/Output definitions
    
    UpdateResource is an Update Action
        // Input/Output definitions
    
    DeleteResource is a Delete Action
        // Input/Output definitions
    
    ListResources is a List Action
        // Input/Output definitions
```

### Pattern 2: Search and Retrieve Pattern

```lpl
DataService is a WebserviceInterface
    // Connection details
    
    SearchData is a Find Action
        // Search criteria in input
        // Result IDs in output (repeating)
    
    GetDetails is an Instance Action
        // ID in input
        // Full details in output
```

### Pattern 3: Validation Service Pattern

```lpl
ValidationService is a WebserviceInterface
    // Connection details
    
    ValidateData is an Instance Action
        // Data to validate in input
        // Validation results and errors in output
```

### Pattern 4: Batch Processing Pattern

```lpl
BatchService is a WebserviceInterface
    // Connection details
    
    SubmitBatch is a Create Action
        // Multiple items in input (repeating)
        // Batch ID in output
    
    CheckBatchStatus is an Instance Action
        // Batch ID in input
        // Status and results in output
```

## Troubleshooting

### Common Issues

**Issue**: Service call fails with authentication error
- **Solution**: Verify authentication type matches service requirements
- **Solution**: Check that credentials are properly configured
- **Solution**: Ensure token is not expired for token authentication

**Issue**: XML parsing errors in response
- **Solution**: Verify namespace matches service documentation
- **Solution**: Check element names match actual response structure
- **Solution**: Use attribute name for XML attributes, not elements

**Issue**: Required field validation fails
- **Solution**: Ensure all fields marked as required have values
- **Solution**: Check data type compatibility
- **Solution**: Verify field names match service expectations

**Issue**: Repeating elements not returned correctly
- **Solution**: Add repeating attribute to output fields
- **Solution**: Verify service returns array structure
- **Solution**: Check namespace and element name mappings

**Issue**: Service timeout errors
- **Solution**: Increase timeout configuration
- **Solution**: Optimize request payload size
- **Solution**: Check service availability and performance

**Issue**: SOAP_V1 deprecated warnings
- **Solution**: Migrate to SOAP_V2 service style
- **Solution**: Update service endpoint if available
- **Solution**: Contact service provider for updated WSDL

**Issue**: Data type mismatch errors
- **Solution**: Verify LPL data types match service schema
- **Solution**: Use appropriate type conversions
- **Solution**: Check decimal precision requirements

**Issue**: Namespace conflicts
- **Solution**: Use unique namespaces for each action
- **Solution**: Match namespace to service version
- **Solution**: Verify namespace in service documentation

## Related Concepts

- [Actions](actions.md) - Action definitions and execution
- [Field Definition](field-definition.md) - Field structure and data types
- [Data Field Types](data-field-types.md) - Available data types
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Rules](rules.md) - Business rules and validation
- [Messages](messages.md) - Error and status messages
- [Conditions](conditions.md) - Conditional logic

## Summary

Webservice Interface definitions enable seamless integration between LPL applications and external services. By properly configuring service styles, authentication, and field mappings, you can reliably exchange data with REST, SOAP, and custom XML-based services. Follow best practices for security, error handling, and performance to build robust integrations.
