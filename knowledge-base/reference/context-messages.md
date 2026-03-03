# Context Messages

## Overview

Context Messages provide contextual information and notifications to users within the user interface. They enable integration with Infor Workspace and other systems by sending structured messages that can display relevant information, alerts, and actions based on the current business context. Context messages enhance user experience by providing timely, relevant information.

## Syntax

### Context Message Definition

```
ContextMessageDefinition ::=
    <ContextMessage> is a[n] <InforContextMessage>
    Property Mapping
        <MessageProperty> = <RelatedValue>
        …
```

### Infor Context Message

```
InforContextMessage ::= <Literal>
```

The type of Infor context message. Available context messages and their properties are defined in the Infor documentation:
http://wiki.infor.com:8080/confluence/display/companyon/Infor10+Workspace+-+Standard+Context+Message+Definitions

### Message Property

```
MessageProperty ::= <Literal>
```

Property name for the context message. Properties vary by message type.

### Context Message Invocation

```
ContextMessageInvocation ::=
    send [<RelatedLink>.][<FullFieldName>.]<ContextMessage>
        [on (all lists | all forms | <ListName> list | <FormName> form)]
        …
```

Specifies where and when to send the context message.

**Valid on:** Business class definition only

**Scope:**
- `all lists` - Send on all lists
- `all forms` - Send on all forms
- `<ListName> list` - Send on specific list
- `<FormName> form` - Send on specific form

## Components

### Context Message Name

Unique identifier for the context message, following LPL naming conventions.

### Infor Context Message Type

The standard Infor context message type that defines the structure and purpose of the message.

**Common Types:**
- Customer context
- Product context
- Order context
- Invoice context
- Employee context
- Custom context types

### Property Mapping

Maps context message properties to business class field values.

```
Property Mapping
    <MessageProperty> = <RelatedValue>
    …
```

Each property corresponds to a field in the Infor context message structure.

### Message Invocation

Specifies where the context message should be sent in the UI.

**Targeting:**
- All lists in the business class
- All forms in the business class
- Specific named list
- Specific named form

**Field-Specific:**
Can send context message for specific field values using `<FullFieldName>.<ContextMessage>`

**Related Link:**
Can send context message from related business class using `<RelatedLink>.<ContextMessage>`

## Use Cases

### Customer Context
- Display customer information
- Show customer status
- Provide customer actions
- Link to customer records

### Product Context
- Show product details
- Display inventory status
- Provide product actions
- Link to product information

### Order Context
- Display order status
- Show order details
- Provide order actions
- Link to order records

### Employee Context
- Show employee information
- Display employee status
- Provide employee actions
- Link to employee records

### Financial Context
- Display account information
- Show financial status
- Provide financial actions
- Link to financial records

## Examples

### Example 1: Simple Customer Context

```
CUSTOMER_CONTEXT is a CustomerContext
    Property Mapping
        customerId = customer.id
        customerName = customer.name
        customerStatus = customer.status
```

Basic customer context message.

### Example 2: Context Message Invocation

```
Context Message Invocations
    send CUSTOMER_CONTEXT
        on all forms
        on CUSTOMER_LIST list
```

Sends customer context on all forms and specific list.

### Example 3: Product Context

```
PRODUCT_CONTEXT is a ProductContext
    Property Mapping
        productId = product.id
        productName = product.name
        productDescription = product.description
        productCategory = product.category
        unitPrice = product.price
        quantityOnHand = product.quantity
```

Product context with inventory information.

### Example 4: Order Context

```
ORDER_CONTEXT is a OrderContext
    Property Mapping
        orderId = order.number
        orderDate = order.date
        orderStatus = order.status
        customerId = customer.id
        customerName = customer.name
        orderTotal = order.total
```

Order context with customer information.

### Example 5: Field-Specific Context

```
Context Message Invocations
    send customer.CUSTOMER_CONTEXT
        on ORDER_FORM form
    
    send product.PRODUCT_CONTEXT
        on ORDER_LINE_LIST list
```

Sends context from related business classes.

### Example 6: Multiple Context Messages

```
EMPLOYEE_CONTEXT is a EmployeeContext
    Property Mapping
        employeeId = employee.id
        employeeName = employee.name
        department = employee.department
        title = employee.title

DEPARTMENT_CONTEXT is a DepartmentContext
    Property Mapping
        departmentId = department.id
        departmentName = department.name
        manager = department.manager

Context Message Invocations
    send EMPLOYEE_CONTEXT
        on all forms
    
    send department.DEPARTMENT_CONTEXT
        on EMPLOYEE_FORM form
```

Multiple context messages for different entities.

### Example 7: Invoice Context

```
INVOICE_CONTEXT is a InvoiceContext
    Property Mapping
        invoiceId = invoice.number
        invoiceDate = invoice.date
        dueDate = invoice.due.date
        invoiceAmount = invoice.amount
        amountDue = invoice.balance
        customerId = customer.id
        customerName = customer.name
        invoiceStatus = invoice.status
```

Invoice context with payment information.

### Example 8: Conditional Context Properties

```
ACCOUNT_CONTEXT is a AccountContext
    Property Mapping
        accountId = account.number
        accountName = account.name
        accountType = account.type
        balance = account.balance
        status = account.status
        creditLimit = if account.type = "Credit" then credit.limit else 0
```

Context with conditional property values.

### Example 9: Asset Context

```
ASSET_CONTEXT is a AssetContext
    Property Mapping
        assetId = asset.id
        assetName = asset.name
        assetType = asset.type
        serialNumber = asset.serial.number
        location = asset.location
        assignedTo = asset.assigned.employee
        purchaseDate = asset.purchase.date
        value = asset.current.value
```

Asset tracking context.

### Example 10: Project Context

```
PROJECT_CONTEXT is a ProjectContext
    Property Mapping
        projectId = project.id
        projectName = project.name
        projectStatus = project.status
        startDate = project.start.date
        endDate = project.end.date
        projectManager = project.manager.name
        budget = project.budget
        actualCost = project.actual.cost
```

Project management context.

### Example 11: Selective Form Invocation

```
Context Message Invocations
    send CUSTOMER_CONTEXT
        on CUSTOMER_FORM form
        on ORDER_FORM form
        on INVOICE_FORM form
```

Sends context on specific forms only.

### Example 12: List-Specific Context

```
Context Message Invocations
    send ORDER_CONTEXT
        on CUSTOMER_ORDER_LIST list
        on PENDING_ORDER_LIST list
```

Sends context on specific lists.

### Example 13: Related Entity Context

```
SUPPLIER_CONTEXT is a SupplierContext
    Property Mapping
        supplierId = supplier.id
        supplierName = supplier.name
        contactName = supplier.contact.name
        contactEmail = supplier.contact.email
        contactPhone = supplier.contact.phone

Context Message Invocations
    send product.supplier.SUPPLIER_CONTEXT
        on PRODUCT_FORM form
```

Context from nested related entity.

### Example 14: Comprehensive Context

```
ORDER_DETAIL_CONTEXT is a OrderContext
    Property Mapping
        orderId = order.number
        orderDate = order.date
        orderStatus = order.status
        customerId = customer.id
        customerName = customer.name
        customerEmail = customer.email
        customerPhone = customer.phone
        shippingAddress = shipping.address.full
        billingAddress = billing.address.full
        subtotal = order.subtotal
        tax = order.tax
        shipping = order.shipping.cost
        total = order.total
        paymentMethod = payment.method
        paymentStatus = payment.status

Context Message Invocations
    send ORDER_DETAIL_CONTEXT
        on all forms
        on ORDER_LIST list
        on PENDING_ORDER_LIST list
```

Comprehensive order context with multiple invocations.

### Example 15: Custom Context Type

```
CUSTOM_WORKFLOW_CONTEXT is a WorkflowContext
    Property Mapping
        workflowId = workflow.id
        workflowName = workflow.name
        workflowStatus = workflow.status
        currentStep = workflow.current.step
        assignedTo = workflow.assigned.user
        dueDate = workflow.due.date
        priority = workflow.priority

Context Message Invocations
    send CUSTOM_WORKFLOW_CONTEXT
        on WORKFLOW_FORM form
```

Custom workflow context.

## Best Practices

1. **Map Relevant Properties**
   - Include essential information
   - Map to appropriate fields
   - Provide complete context
   - Avoid unnecessary data

2. **Use Standard Message Types**
   - Follow Infor standards
   - Use documented message types
   - Maintain consistency
   - Enable integration

3. **Target Appropriately**
   - Send on relevant forms/lists
   - Avoid over-sending
   - Consider performance
   - Test invocations

4. **Provide Complete Information**
   - Map all required properties
   - Include identifying information
   - Provide status indicators
   - Add relevant details

5. **Consider Related Entities**
   - Send context from related objects
   - Provide hierarchical context
   - Enable drill-down
   - Support navigation

6. **Test Context Messages**
   - Verify property mapping
   - Test on target forms/lists
   - Validate data values
   - Check integration

7. **Document Context Usage**
   - Explain message purpose
   - Note property mappings
   - Describe invocations
   - Document integration points

## Context Message Integration

### Infor Workspace

Context messages integrate with Infor Workspace to:
- Display contextual information
- Enable cross-application navigation
- Provide related actions
- Support collaboration

### External Systems

Context messages can be consumed by:
- Third-party applications
- Custom integrations
- Reporting tools
- Analytics platforms

## Property Mapping Considerations

### Required Properties

Each Infor context message type has required properties that must be mapped.

### Optional Properties

Additional properties enhance context but may not be required.

### Data Types

Ensure mapped values match expected property data types.

### Null Handling

Consider how null or empty values are handled in context messages.

## Troubleshooting

### Issue: Context message not appearing

**Possible Causes:**
- Invocation not configured
- Wrong form/list specified
- Property mapping incomplete
- Integration not enabled

**Solutions:**
- Verify invocation configuration
- Check form/list names
- Complete property mapping
- Enable Workspace integration

### Issue: Incorrect data in context

**Possible Causes:**
- Wrong field mapped
- Data type mismatch
- Null values
- Calculation error

**Solutions:**
- Verify field mappings
- Check data types
- Handle null values
- Test calculations

### Issue: Context not updating

**Possible Causes:**
- Caching issue
- Refresh not triggered
- Data not changed
- Integration delay

**Solutions:**
- Clear cache
- Force refresh
- Verify data changes
- Check integration timing

### Issue: Performance degradation

**Possible Causes:**
- Too many context messages
- Complex property mappings
- Frequent updates
- Large data volumes

**Solutions:**
- Reduce message frequency
- Simplify mappings
- Optimize queries
- Batch updates

## Related Concepts

- [UI Definition](ui-definition.md) - Overall UI structure
- [Messages](messages.md) - User messages
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Relations](relations.md) - Related entity access
- [Field Definition](field-definition.md) - Field structure

## Notes

- Context messages are valid on business class definitions only
- Available message types defined in Infor documentation
- Property mappings connect business data to message structure
- Invocations specify where messages are sent
- Can send context from related business classes
- Can send field-specific context messages
- Support all lists, all forms, or specific targets
- Enable integration with Infor Workspace
- Enhance user experience with contextual information
- Test context messages in target environment
- Follow Infor standards for message types
- Document property mappings clearly
- Consider performance impact of context messages
- Verify integration configuration
- Test with various data scenarios
