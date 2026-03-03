# URL Panels

## Overview

URL Panels embed external web content within panels on Pages and Composite Forms. They display content from HTTP/HTTPS URLs, enabling integration with external websites, documentation, help systems, and third-party applications. URL Panels support context-aware URLs that can reference business class data or pane selections.

## Basic Syntax

```
URLPanel ::=
    url is [(<BusinessClass> | pane <PaneNumber>).]<HttpURL>
```

## URL Specification

### Static URL

```
url is <HttpURL>
```

Displays content from a static URL.

**Example:**
```
url is "https://www.example.com/help"
url is "https://docs.example.com/api"
```

### Business Class Context URL

```
url is <BusinessClass>.<HttpURL>
```

URL in the context of a business class instance. Can reference business class fields in the URL.

**Example:**
```
url is Customer."https://www.example.com/customer?id=<Customer>"
url is Order."https://api.example.com/order/<OrderNumber>"
```

### Pane Context URL

```
url is pane <PaneNumber>.<HttpURL>
```

URL in the context of a pane selection in a Multi-Pane Panel.

**Pane Numbers:**
- **1**: Upper left
- **2**: Lower left
- **3**: Upper right
- **4**: Lower right

**Example:**
```
url is pane 1."https://www.example.com/details?id=<Customer>"
url is pane 2."https://api.example.com/data/<RecordId>"
```

## URL Construction

### Field References

URLs can include field references using angle brackets:

```
"https://www.example.com/page?param=<FieldName>"
```

**Example:**
```
url is Customer."https://www.example.com/customer?id=<Customer>&name=<CustomerName>"
```

### Multiple Parameters

```
"https://www.example.com/page?param1=<Field1>&param2=<Field2>"
```

**Example:**
```
url is Order."https://api.example.com/order?order=<OrderNumber>&customer=<Customer>&date=<OrderDate>"
```

### URL Encoding

Field values are automatically URL-encoded when inserted into the URL.

## Examples

### Example 1: Static Help URL

```
url is "https://help.example.com/documentation"
```

### Example 2: Customer Details URL

```
url is Customer."https://www.example.com/customer/details?id=<Customer>"
```

### Example 3: Order Tracking URL

```
url is Order."https://tracking.example.com/track?order=<OrderNumber>&carrier=<ShippingCarrier>"
```

### Example 4: Product Information URL

```
url is Product."https://www.example.com/products/<ProductCode>"
```

### Example 5: Pane-Referenced URL

```
url is pane 1."https://www.example.com/details?id=<RecordId>&type=<RecordType>"
```

### Example 6: API Integration URL

```
url is Customer."https://api.example.com/v1/customers/<Customer>/summary"
```

### Example 7: Documentation URL with Context

```
url is BusinessClass."https://docs.example.com/<BusinessClassName>/help"
```

### Example 8: External Report URL

```
url is "https://reports.example.com/dashboard?user=<actor>&department=<Department>"
```

### Example 9: Map Integration URL

```
url is Location."https://maps.example.com/map?address=<Address>&city=<City>&state=<State>&zip=<ZipCode>"
```

### Example 10: Third-Party Tool URL

```
url is Project."https://tools.example.com/project?id=<ProjectId>&name=<ProjectName>"
```

### Example 11: Multi-Parameter URL

```
url is Invoice."https://www.example.com/invoice?invoice=<InvoiceNumber>&customer=<Customer>&date=<InvoiceDate>&amount=<Total>"
```

### Example 12: Pane Coordination URL

```
WORKSPACE is a MultiPanePanel
    pane 1
        CUSTOMERLIST
    
    pane 3
        url is pane 1."https://www.example.com/customer/<Customer>/details"
```

### Example 13: Help Context URL

```
url is "https://help.example.com/context?page=<CurrentPage>&section=<CurrentSection>"
```

### Example 14: Analytics Dashboard URL

```
url is "https://analytics.example.com/dashboard?company=<Company>&period=<FiscalPeriod>&year=<FiscalYear>"
```

### Example 15: External System Integration

```
url is Order."https://external-system.example.com/integration?entity=order&id=<OrderNumber>&action=view"
```

## Use Cases

### External Documentation
- Help systems
- User guides
- API documentation
- Technical references

### Third-Party Integration
- External applications
- Partner systems
- Cloud services
- SaaS applications

### Reporting and Analytics
- External dashboards
- Analytics platforms
- BI tools
- Reporting systems

### Maps and Location Services
- Map displays
- Location tracking
- Route planning
- Geographic visualization

### Customer Portals
- Customer self-service
- Order tracking
- Account management
- Support portals

### API Integration
- REST API calls
- Web services
- Data visualization
- External data sources

## Best Practices

### URL Design

1. **URL Structure**
   - Use clear, meaningful URLs
   - Follow REST conventions
   - Use HTTPS when possible
   - Validate URL format

2. **Parameter Naming**
   - Use descriptive parameter names
   - Follow conventions
   - Be consistent
   - Document parameters

3. **Field References**
   - Use appropriate fields
   - Validate field availability
   - Handle missing values
   - Test with various data

### Security

1. **HTTPS Usage**
   - Use HTTPS for sensitive data
   - Validate certificates
   - Secure connections
   - Protect credentials

2. **Data Exposure**
   - Minimize sensitive data in URLs
   - Use POST when appropriate
   - Validate permissions
   - Audit access

3. **URL Validation**
   - Validate URL format
   - Check for injection
   - Sanitize inputs
   - Test security

### Context Management

1. **Business Class Context**
   - Ensure instance selected
   - Handle missing context
   - Validate field values
   - Test with various records

2. **Pane Context**
   - Coordinate pane selection
   - Handle empty panes
   - Test pane interactions
   - Validate references

### Performance

1. **External Content**
   - Consider load times
   - Handle timeouts
   - Provide feedback
   - Cache when appropriate

2. **URL Construction**
   - Optimize field references
   - Minimize parameters
   - Test performance
   - Monitor response times

### User Experience

1. **Content Display**
   - Ensure responsive content
   - Handle different screen sizes
   - Test in iframe context
   - Provide fallbacks

2. **Navigation**
   - Clear purpose
   - Easy to understand
   - Consistent behavior
   - Test usability

### Error Handling

1. **URL Errors**
   - Handle invalid URLs
   - Provide error messages
   - Offer alternatives
   - Log errors

2. **External Content Errors**
   - Handle load failures
   - Timeout handling
   - Network errors
   - Provide feedback

### Testing

1. **Functional Testing**
   - Test URL construction
   - Test field substitution
   - Test with various data
   - Verify external content

2. **Integration Testing**
   - Test with external systems
   - Test pane coordination
   - Test context changes
   - Verify behavior

## Common Patterns

### Static URL Pattern

```
url is "<StaticURL>"
```

### Business Class URL Pattern

```
url is <BusinessClass>."<BaseURL>?id=<KeyField>"
```

### Multi-Parameter URL Pattern

```
url is <BusinessClass>."<BaseURL>?param1=<Field1>&param2=<Field2>&param3=<Field3>"
```

### Pane-Coordinated URL Pattern

```
url is pane <PaneNumber>."<BaseURL>?id=<KeyField>"
```

### API Integration Pattern

```
url is <BusinessClass>."<APIBaseURL>/<ResourcePath>/<KeyField>"
```

## Integration Examples

### Help System Integration

```
HELPPAGE is a Page
    title is "Help"
    
    HELPCONTENT is a Panel
        title is "Help Content"
        url is "https://help.example.com/documentation"
```

### Customer Portal Integration

```
CUSTOMERPORTAL is a Page
    title is "Customer Portal"
    
    PORTALCONTENT is a Panel
        title is "Portal"
        url is Customer."https://portal.example.com/customer?id=<Customer>"
```

### Multi-Pane Coordination

```
DETAILVIEW is a Page
    title is "Detail View"
    
    WORKSPACE is a MultiPanePanel
        title is "Workspace"
        
        pane 1
            RECORDLIST
        
        pane 3
            url is pane 1."https://www.example.com/details?id=<RecordId>"
```

### Analytics Dashboard

```
ANALYTICS is a Page
    title is "Analytics"
    
    DASHBOARD is a Panel
        title is "Dashboard"
        url is "https://analytics.example.com/dashboard?company=<Company>&period=<FiscalPeriod>"
```

### Map Integration

```
LOCATIONVIEW is a Page
    title is "Location View"
    
    MAPVIEW is a Panel
        title is "Map"
        url is Location."https://maps.example.com/map?address=<Address>&city=<City>&state=<State>"
```

## Troubleshooting

### Issue: URL Not Loading

**Possible Causes:**
- Invalid URL format
- External site unavailable
- Network issues
- CORS restrictions

**Solutions:**
- Validate URL format
- Check external site status
- Test network connectivity
- Review CORS configuration

### Issue: Field Values Not Substituting

**Possible Causes:**
- Field name incorrect
- Context not available
- Field value empty
- Syntax error

**Solutions:**
- Verify field names
- Ensure context set
- Handle empty values
- Check syntax

### Issue: Pane Context Not Working

**Possible Causes:**
- Wrong pane number
- Pane not populated
- Selection not made
- Context mismatch

**Solutions:**
- Verify pane number
- Ensure pane has data
- Select record in pane
- Align contexts

### Issue: Security Errors

**Possible Causes:**
- Mixed content (HTTP/HTTPS)
- Certificate issues
- CORS restrictions
- Security policies

**Solutions:**
- Use HTTPS consistently
- Validate certificates
- Configure CORS
- Review security policies

### Issue: Content Not Displaying

**Possible Causes:**
- Iframe restrictions
- X-Frame-Options header
- Content Security Policy
- External site blocking

**Solutions:**
- Check iframe compatibility
- Review X-Frame-Options
- Check CSP headers
- Contact external site

### Issue: Performance Problems

**Possible Causes:**
- Slow external site
- Large content
- Network latency
- Multiple requests

**Solutions:**
- Optimize external content
- Reduce content size
- Improve network
- Minimize requests

## Security Considerations

### Data Protection

1. **Sensitive Data**
   - Avoid sensitive data in URLs
   - Use POST for sensitive operations
   - Encrypt connections (HTTPS)
   - Validate permissions

2. **URL Parameters**
   - Minimize exposed data
   - Validate parameter values
   - Sanitize inputs
   - Audit access

### External Content

1. **Trusted Sources**
   - Use trusted external sites
   - Validate SSL certificates
   - Review security policies
   - Monitor for changes

2. **Content Validation**
   - Validate external content
   - Check for malicious code
   - Review regularly
   - Implement CSP

### Access Control

1. **Authentication**
   - Validate user authentication
   - Pass credentials securely
   - Use tokens when appropriate
   - Audit access

2. **Authorization**
   - Verify permissions
   - Restrict access appropriately
   - Log access attempts
   - Review regularly

## Related Concepts

- [Panel Definitions](panel-definitions.md) - Panel types
- [Pages](pages.md) - Page definitions
- [Composite Forms](composite-forms.md) - Multi-panel forms
- [List Panels](list-panels.md) - List panel definitions
- [Menu Panels](menu-panels.md) - Menu panel definitions
- [Search Panels](search-panels.md) - Search panel definitions
- [Business Class Definition](business-class-definition.md) - Business class structure

## Summary

URL Panels embed external web content within panels on Pages and Composite Forms. With support for static URLs, business class context URLs, and pane-coordinated URLs, URL Panels enable seamless integration with external websites, documentation, help systems, and third-party applications. Field references in URLs allow dynamic content based on business data, while pane coordination enables master-detail views with external content.

**Key Features:**
- External content embedding
- Static URLs
- Business class context URLs
- Pane context URLs
- Field reference substitution
- Dynamic URL construction
- Multi-parameter support
- Automatic URL encoding

**URL Types:**
- Static URL
- Business class context URL
- Pane context URL

**Best For:**
- External documentation
- Third-party integration
- Reporting and analytics
- Maps and location services
- Customer portals
- API integration
- Help systems
- External dashboards

**Advantages:**
- Easy integration
- Dynamic content
- Context-aware URLs
- Flexible parameters
- Pane coordination
- Simple configuration
- No custom code required

**Important Notes:**
- Use HTTPS for security
- Validate field availability
- Handle missing context
- Test with various data
- Consider CORS restrictions
- Review X-Frame-Options
- Monitor external site availability
- Protect sensitive data
- Validate permissions
- Audit access

**Security Best Practices:**
- Use HTTPS consistently
- Minimize sensitive data in URLs
- Validate external sources
- Implement proper authentication
- Review security policies
- Monitor access
- Test thoroughly
