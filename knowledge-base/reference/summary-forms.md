# Summary Forms

## Overview

Summary Forms provide a specialized interface for generating printable reports and PDF documents. They support headers, footers, embedded forms and lists, and flexible layouts optimized for printing. Summary Forms are ideal for invoices, reports, statements, and any document that needs to be printed or exported to PDF.

## Basic Syntax

```
<FormName> is a SummaryForm
    [title is <Message>]
    [suppress header]
    [suppress footer]
    [disallow show as pdf]
    [orientation is (portrait | landscape)]
    [font offset is <Number>]
    [First Page Header]
        ( form is <FormName>
        | Layout
            <SummaryLayout>
            …)
    [Page Header]
        [print on first page]
        ( form is <FormName>
        | Layout
            <SummaryLayout>
            …)
    [Page Footer]
        ( form is <FormName>
        | Layout
            <SummaryLayout>
            …)
    Layout
        <SummaryLayout>
        …
```

## Definition

```
SummaryFormDefinition ::=
    <FormName> is a SummaryForm
        [title is <Message>]
        [suppress header]
        [suppress footer]
        [disallow show as pdf]
        [orientation is (portrait | landscape)]
        [font offset is <Number>]
        [First Page Header]
            ( form is <FormName>
            | Layout
                <SummaryLayout>
                …)
        [Page Header]
            [print on first page]
            ( form is <FormName>
            | Layout
                <SummaryLayout>
                …)
        [Page Footer]
            ( form is <FormName>
            | Layout
                <SummaryLayout>
                …)
        Layout
            <SummaryLayout>
            …
```

## Summary Form Attributes

### Title

```
title is <Message>
```

| Attribute | Description | Default |
|-----------|-------------|---------|
| `title is <Message>` | Summary form title | FormName |

### Header and Footer Control

```
suppress header
suppress footer
```

| Attribute | Description |
|-----------|-------------|
| `suppress header` | Hides the header section |
| `suppress footer` | Hides the footer section |

### PDF Control

```
disallow show as pdf
```

Prevents the form from being displayed as PDF. Use when PDF generation is not desired or supported.

### Page Orientation

```
orientation is (portrait | landscape)
```

| Option | Description |
|--------|-------------|
| `portrait` | Portrait orientation (default) |
| `landscape` | Landscape orientation |

### Font Offset

```
font offset is <Number>
```

Adjusts the font size by the specified offset. Positive numbers increase size, negative numbers decrease size.

## Headers and Footers

### First Page Header

```
First Page Header
    ( form is <FormName>
    | Layout
        <SummaryLayout>
        …)
```

Header that appears only on the first page.

**Options:**
- **form is**: Use an existing form for the header
- **Layout**: Define inline layout for the header

### Page Header

```
Page Header
    [print on first page]
    ( form is <FormName>
    | Layout
        <SummaryLayout>
        …)
```

Header that appears on all pages (or all pages except first).

**Attributes:**
- **print on first page**: Prints on first page (not valid if First Page Header is defined)

**Options:**
- **form is**: Use an existing form for the header
- **Layout**: Define inline layout for the header

### Page Footer

```
Page Footer
    ( form is <FormName>
    | Layout
        <SummaryLayout>
        …)
```

Footer that appears on all pages.

**Options:**
- **form is**: Use an existing form for the footer
- **Layout**: Define inline layout for the footer

## Summary Layout

```
SummaryLayout ::=
    ( <FormField>
    | <FormButton>
    | <CheckControl>
    | <ManualRepresentation>
    | <FormText>
    | form is [<RelatedLink>.]<FormName>
      [<Header>]
    | list is [<RelatedLink>.]<ListName>
      [show grid lines]
      [<Header>]
    | <LayoutDirective>
      [<SummaryLayout>…])
```

### Form Fields and Controls

Standard form components:
- **FormField**: Display field values
- **FormButton**: Action buttons
- **CheckControl**: Checkboxes
- **ManualRepresentation**: Custom representations
- **FormText**: Static text

### Embedded Forms

```
form is [<RelatedLink>.]<FormName>
    [<Header>]
```

Embeds another form in the summary. Can take only an OTM (one-to-many) RelatedLink.

**Note:** If RelatedLink is empty, header will not display.

**Use Cases:**
- Detail sections
- Related records
- Nested information
- Repeating sections

### Embedded Lists

```
list is [<RelatedLink>.]<ListName>
    [show grid lines]
    [<Header>]
```

Embeds a list in the summary. Can take only an OTM (one-to-many) RelatedLink.

**Attributes:**
- **show grid lines**: Displays grid lines around list items

**Note:** If RelatedLink is empty, header will not display.

**Use Cases:**
- Line items
- Transaction details
- Related records
- Tabular data

### Layout Directives

All standard layout directives are available:
- Paragraph
- Single/multi-column layouts
- Sections
- Conditional visibility
- Blank lines
- Page breaks

## Examples

### Example 1: Simple Invoice

```
INVOICESUMMARY is a SummaryForm
    title is "Invoice"
    orientation is portrait
    
    First Page Header
        Layout
            single column
                COMPANYLOGO
                    display as portrait image
                COMPANYNAME
                    label is no label
                COMPANYADDRESS
                    label is no label
    
    Page Footer
        Layout
            paragraph centered
                "Page <PageNumber> of <TotalPages>"
    
    Layout
        two column
            INVOICENUMBER
                label is "Invoice Number"
            INVOICEDATE
                label is "Invoice Date"
            CUSTOMER
                label is "Customer"
            DUEDATE
                label is "Due Date"
        
        blank line
        
        single column
            BILLTOADDRESS
                label is "Bill To"
        
        blank line
        
        list is InvoiceLines.INVOICELINELIST
            show grid lines
            "Invoice Lines"
        
        blank line
        
        two column
            SUBTOTAL
                label is "Subtotal"
                display 2 decimals
                currency symbol is "$"
            TAX
                label is "Tax"
                display 2 decimals
                currency symbol is "$"
            TOTAL
                label is "Total"
                display 2 decimals
                currency symbol is "$"
```

### Example 2: Purchase Order

```
POSUMMARY is a SummaryForm
    title is "Purchase Order"
    orientation is portrait
    
    Page Header
        print on first page
        Layout
            two column
                COMPANYNAME
                    label is no label
                PONUMBER
                    label is "PO Number"
    
    Page Footer
        Layout
            paragraph centered
                "Page <PageNumber>"
    
    Layout
        section of HEADER
            two column
                PONUMBER
                    label is "PO Number"
                PODATE
                    label is "PO Date"
                VENDOR
                    label is "Vendor"
                REQUESTEDBY
                    label is "Requested By"
        
        blank line
        
        section of SHIPPING
            single column
                SHIPTOADDRESS
                    label is "Ship To"
        
        blank line
        
        section of ITEMS
            list is POLines.POLINELIST
                show grid lines
                "Purchase Order Lines"
        
        blank line
        
        section of TOTALS
            two column
                SUBTOTAL
                    label is "Subtotal"
                    display 2 decimals
                    currency symbol is "$"
                SHIPPING
                    label is "Shipping"
                    display 2 decimals
                    currency symbol is "$"
                TAX
                    label is "Tax"
                    display 2 decimals
                    currency symbol is "$"
                TOTAL
                    label is "Total"
                    display 2 decimals
                    currency symbol is "$"
```

### Example 3: Statement

```
STATEMENTSUMMARY is a SummaryForm
    title is "Account Statement"
    orientation is portrait
    
    First Page Header
        Layout
            two column
                COMPANYLOGO
                    display as portrait image
                COMPANYNAME
                    label is no label
                COMPANYADDRESS
                    label is no label
                STATEMENTDATE
                    label is "Statement Date"
    
    Page Header
        Layout
            paragraph
                COMPANYNAME
                    label is no label
    
    Page Footer
        Layout
            paragraph centered
                "Page <PageNumber> of <TotalPages>"
    
    Layout
        section of ACCOUNTINFO
            two column
                ACCOUNTNUMBER
                    label is "Account Number"
                CUSTOMER
                    label is "Customer"
                STATEMENTDATE
                    label is "Statement Date"
                DUEDATE
                    label is "Due Date"
        
        blank line
        
        section of SUMMARY
            two column
                PREVIOUSBALANCE
                    label is "Previous Balance"
                    display 2 decimals
                    currency symbol is "$"
                PAYMENTS
                    label is "Payments"
                    display 2 decimals
                    currency symbol is "$"
                CHARGES
                    label is "Charges"
                    display 2 decimals
                    currency symbol is "$"
                CURRENTBALANCE
                    label is "Current Balance"
                    display 2 decimals
                    currency symbol is "$"
        
        blank line
        
        section of TRANSACTIONS
            list is Transactions.TRANSACTIONLIST
                show grid lines
                "Transaction History"
```

### Example 4: Report with Landscape Orientation

```
SALESREPORT is a SummaryForm
    title is "Sales Report"
    orientation is landscape
    
    Page Header
        print on first page
        Layout
            two column
                COMPANYNAME
                    label is no label
                REPORTDATE
                    label is "Report Date"
    
    Page Footer
        Layout
            paragraph centered
                "Page <PageNumber> of <TotalPages>"
    
    Layout
        section of PARAMETERS
            two column
                DATEFROM
                    label is "Date From"
                DATETO
                    label is "Date To"
                REGION
                    label is "Region"
                SALESPERSON
                    label is "Salesperson"
        
        blank line
        
        section of SUMMARY
            three column
                TOTALSALES
                    label is "Total Sales"
                    display 2 decimals
                    currency symbol is "$"
                TOTALORDERS
                    label is "Total Orders"
                AVERAGEORDER
                    label is "Average Order"
                    display 2 decimals
                    currency symbol is "$"
        
        blank line
        
        section of DETAILS
            list is SalesDetails.SALESLIST
                show grid lines
                "Sales Details"
```

### Example 5: Packing Slip

```
PACKINGSLIP is a SummaryForm
    title is "Packing Slip"
    orientation is portrait
    suppress footer
    
    First Page Header
        Layout
            two column
                COMPANYLOGO
                    display as portrait image
                ORDERNUMBER
                    label is "Order Number"
                ORDERDATE
                    label is "Order Date"
    
    Layout
        section of SHIPPING
            single column
                SHIPTO
                    label is "Ship To"
                SHIPTOADDRESS
                    label is no label
                SHIPPINGMETHOD
                    label is "Shipping Method"
                TRACKINGNUMBER
                    label is "Tracking Number"
        
        blank line
        
        section of ITEMS
            list is OrderLines.ORDERLINELIST
                show grid lines
                "Items to Ship"
        
        blank line
        
        section of NOTES
            single column
                SHIPPINGNOTES
                    label is "Shipping Notes"
                    use text area of 5 lines
```

### Example 6: Receipt

```
RECEIPT is a SummaryForm
    title is "Receipt"
    orientation is portrait
    font offset is -1
    
    Page Header
        print on first page
        Layout
            paragraph centered
                COMPANYNAME
                    label is no label
                COMPANYADDRESS
                    label is no label
    
    Layout
        paragraph centered
            RECEIPTNUMBER
                label is "Receipt Number"
            RECEIPTDATE
                label is "Date"
        
        blank line
        
        single column
            CUSTOMER
                label is "Customer"
        
        blank line
        
        list is ReceiptLines.RECEIPTLINELIST
            "Items"
        
        blank line
        
        two column
            SUBTOTAL
                label is "Subtotal"
                display 2 decimals
                currency symbol is "$"
            TAX
                label is "Tax"
                display 2 decimals
                currency symbol is "$"
            TOTAL
                label is "Total"
                display 2 decimals
                currency symbol is "$"
        
        blank line
        
        paragraph centered
            "Thank you for your business!"
```

### Example 7: Certificate

```
CERTIFICATE is a SummaryForm
    title is "Certificate"
    orientation is landscape
    suppress header
    suppress footer
    
    Layout
        blank line
        blank line
        
        paragraph centered
            CERTIFICATETITLE
                label is no label
        
        blank line
        
        paragraph centered
            "This certifies that"
        
        blank line
        
        paragraph centered
            RECIPIENTNAME
                label is no label
        
        blank line
        
        paragraph centered
            CERTIFICATETEXT
                label is no label
                use text area of 3 lines
        
        blank line
        blank line
        
        two column
            ISSUEDATE
                label is "Date"
            ISSUEDBY
                label is "Issued By"
```

### Example 8: Multi-Page Report

```
DETAILEDREPORT is a SummaryForm
    title is "Detailed Report"
    orientation is portrait
    
    First Page Header
        Layout
            two column
                COMPANYLOGO
                    display as portrait image
                REPORTTITLE
                    label is no label
                REPORTDATE
                    label is "Report Date"
    
    Page Header
        Layout
            paragraph
                REPORTTITLE
                    label is no label
    
    Page Footer
        Layout
            two column
                REPORTDATE
                    label is "Date"
                "Page <PageNumber> of <TotalPages>"
    
    Layout
        section of SUMMARY
            two column
                TOTALRECORDS
                    label is "Total Records"
                DATERANGE
                    label is "Date Range"
        
        blank line
        
        section of DETAILS
            form is ReportDetails.DETAILFORM
                "Report Details"
        
        page break
        
        section of APPENDIX
            single column
                APPENDIXTEXT
                    label is "Appendix"
                    use text area of 10 lines
```

### Example 9: Quote

```
QUOTE is a SummaryForm
    title is "Quote"
    orientation is portrait
    
    First Page Header
        Layout
            two column
                COMPANYLOGO
                    display as portrait image
                COMPANYNAME
                    label is no label
                COMPANYADDRESS
                    label is no label
                COMPANYPHONE
                    label is no label
    
    Page Footer
        Layout
            paragraph centered
                "Valid for 30 days from quote date"
                "Page <PageNumber>"
    
    Layout
        section of QUOTEINFO
            two column
                QUOTENUMBER
                    label is "Quote Number"
                QUOTEDATE
                    label is "Quote Date"
                CUSTOMER
                    label is "Customer"
                VALIDUNTIL
                    label is "Valid Until"
        
        blank line
        
        section of CUSTOMERINFO
            single column
                CUSTOMERADDRESS
                    label is "Customer Address"
                CONTACTPERSON
                    label is "Contact Person"
                CONTACTPHONE
                    label is "Phone"
                CONTACTEMAIL
                    label is "Email"
        
        blank line
        
        section of ITEMS
            list is QuoteLines.QUOTELINELIST
                show grid lines
                "Quote Items"
        
        blank line
        
        section of TOTALS
            two column
                SUBTOTAL
                    label is "Subtotal"
                    display 2 decimals
                    currency symbol is "$"
                DISCOUNT
                    label is "Discount"
                    display 2 decimals
                    currency symbol is "$"
                TAX
                    label is "Tax"
                    display 2 decimals
                    currency symbol is "$"
                TOTAL
                    label is "Total"
                    display 2 decimals
                    currency symbol is "$"
        
        blank line
        
        section of TERMS
            single column
                PAYMENTTERMS
                    label is "Payment Terms"
                DELIVERYTERMS
                    label is "Delivery Terms"
                NOTES
                    label is "Notes"
                    use text area of 5 lines
```

### Example 10: Work Order

```
WORKORDER is a SummaryForm
    title is "Work Order"
    orientation is portrait
    
    Page Header
        print on first page
        Layout
            two column
                COMPANYNAME
                    label is no label
                WORKORDERNUMBER
                    label is "Work Order #"
    
    Page Footer
        Layout
            paragraph centered
                "Page <PageNumber>"
    
    Layout
        section of WORKORDERINFO
            two column
                WORKORDERNUMBER
                    label is "Work Order Number"
                WORKORDERDATE
                    label is "Date"
                CUSTOMER
                    label is "Customer"
                PRIORITY
                    label is "Priority"
                ASSIGNEDTO
                    label is "Assigned To"
                DUEDATE
                    label is "Due Date"
        
        blank line
        
        section of DESCRIPTION
            single column
                WORKDESCRIPTION
                    label is "Work Description"
                    use text area of 5 lines
        
        blank line
        
        section of TASKS
            list is WorkOrderTasks.TASKLIST
                show grid lines
                "Tasks"
        
        blank line
        
        section of MATERIALS
            list is WorkOrderMaterials.MATERIALLIST
                show grid lines
                "Materials"
        
        blank line
        
        section of LABOR
            two column
                ESTIMATEDHOURS
                    label is "Estimated Hours"
                ACTUALHOURS
                    label is "Actual Hours"
                LABORRATE
                    label is "Labor Rate"
                    display 2 decimals
                    currency symbol is "$"
                LABORCOST
                    label is "Labor Cost"
                    display 2 decimals
                    currency symbol is "$"
```

## Use Cases

### Invoices and Billing
- Sales invoices
- Service invoices
- Credit memos
- Statements

### Orders and Quotes
- Purchase orders
- Sales orders
- Quotes
- Packing slips

### Reports
- Financial reports
- Sales reports
- Inventory reports
- Custom reports

### Receipts and Confirmations
- Payment receipts
- Order confirmations
- Shipping confirmations
- Delivery receipts

### Certificates and Documents
- Certificates
- Licenses
- Permits
- Official documents

## Best Practices

### Form Design

1. **Layout Organization**
   - Use clear sections
   - Group related information
   - Balance density
   - Consider page breaks

2. **Headers and Footers**
   - Include company information
   - Add page numbers
   - Show document title
   - Include dates

3. **Branding**
   - Include company logo
   - Use consistent styling
   - Apply brand colors
   - Maintain professional appearance

### Content

1. **Field Selection**
   - Include essential information
   - Remove unnecessary fields
   - Use appropriate labels
   - Format values properly

2. **Lists and Details**
   - Use embedded lists for line items
   - Show grid lines for clarity
   - Include headers
   - Handle empty lists

3. **Totals and Calculations**
   - Display clearly
   - Format consistently
   - Show currency symbols
   - Align properly

### Printing

1. **Page Orientation**
   - Portrait for most documents
   - Landscape for wide tables
   - Test both orientations
   - Consider content width

2. **Page Breaks**
   - Use strategically
   - Keep related content together
   - Test multi-page documents
   - Avoid orphaned content

3. **Font Sizing**
   - Use readable fonts
   - Adjust with font offset
   - Test printed output
   - Consider accessibility

### PDF Generation

1. **PDF Settings**
   - Test PDF output
   - Verify formatting
   - Check page breaks
   - Validate content

2. **File Size**
   - Optimize images
   - Limit embedded content
   - Test generation time
   - Monitor performance

### Headers and Footers

1. **First Page Header**
   - Use for detailed header
   - Include logo and company info
   - Make it distinctive
   - Test appearance

2. **Page Header**
   - Keep concise
   - Include essential info
   - Consistent across pages
   - Test with multiple pages

3. **Page Footer**
   - Include page numbers
   - Add contact information
   - Show document date
   - Keep minimal

### Embedded Content

1. **Forms**
   - Use for repeating sections
   - Handle empty relations
   - Test with various data
   - Provide headers

2. **Lists**
   - Use for line items
   - Show grid lines
   - Format columns
   - Handle pagination

### Testing

1. **Print Testing**
   - Test actual printing
   - Verify page breaks
   - Check alignment
   - Validate formatting

2. **Data Testing**
   - Test with minimal data
   - Test with maximum data
   - Test empty sections
   - Test edge cases

## Common Patterns

### Invoice Pattern

```
<EntityName>INVOICE is a SummaryForm
    title is "Invoice"
    orientation is portrait
    
    First Page Header
        Layout
            <company info>
    
    Page Footer
        Layout
            <page numbers>
    
    Layout
        <invoice header>
        blank line
        <bill to>
        blank line
        list is Lines.<LineList>
            show grid lines
        blank line
        <totals>
```

### Report Pattern

```
<ReportName>REPORT is a SummaryForm
    title is "<Report Name>"
    orientation is (portrait | landscape)
    
    Page Header
        print on first page
        Layout
            <report header>
    
    Page Footer
        Layout
            <page numbers>
    
    Layout
        <report parameters>
        blank line
        <summary>
        blank line
        list is Details.<DetailList>
            show grid lines
```

### Certificate Pattern

```
<CertificateName>CERT is a SummaryForm
    title is "Certificate"
    orientation is landscape
    suppress header
    suppress footer
    
    Layout
        <centered content>
        <recipient info>
        <certification text>
        <signatures>
```

## Comparison with Other Forms

| Feature | Summary Form | Standard Form | Action Form |
|---------|--------------|---------------|-------------|
| Purpose | Printing/PDF | Data entry | Action execution |
| Headers/Footers | Supported | Not supported | Not supported |
| Page Breaks | Supported | Not applicable | Not applicable |
| Embedded Lists | Supported | Limited | Not supported |
| Orientation | Configurable | Not applicable | Not applicable |
| Use Case | Documents | CRUD | Actions |

## Troubleshooting

### Issue: Content Not Printing

**Possible Causes:**
- Header/footer suppressed
- Content outside margins
- Page break issues
- Empty sections

**Solutions:**
- Check suppress settings
- Adjust layout
- Review page breaks
- Handle empty content

### Issue: Page Breaks Wrong

**Possible Causes:**
- Content too large
- No page break directives
- List pagination
- Section sizing

**Solutions:**
- Add page breaks
- Adjust content size
- Configure list pagination
- Test with various data

### Issue: Headers/Footers Not Showing

**Possible Causes:**
- Suppressed
- Empty content
- Configuration errors
- Print settings

**Solutions:**
- Remove suppress
- Add content
- Fix configuration
- Check print settings

### Issue: PDF Generation Failing

**Possible Causes:**
- Complex layout
- Large images
- Embedded content errors
- System resources

**Solutions:**
- Simplify layout
- Optimize images
- Fix embedded content
- Check resources

### Issue: Embedded Lists Not Displaying

**Possible Causes:**
- Empty relation
- List errors
- Header issues
- Permissions

**Solutions:**
- Check relation
- Validate list
- Fix headers
- Review permissions

## Related Concepts

- [Forms](forms.md) - Standard form definitions
- [Lists](lists.md) - List definitions
- [Form Base Definitions](form-base-definitions.md) - Form components
- [Business Class Definition](business-class-definition.md) - Business class structure
- [Field Definition](field-definition.md) - Field structure
- [Relations](relations.md) - Relationships
- [UI Definition](ui-definition.md) - UI components

## Summary

Summary Forms provide specialized interfaces for generating printable documents and PDF reports. With support for headers, footers, page breaks, embedded forms and lists, and flexible layouts, Summary Forms are ideal for invoices, reports, statements, and any document requiring professional printed output. Proper configuration of page orientation, headers/footers, and embedded content enables high-quality document generation.

**Key Features:**
- Print-optimized layout
- Headers and footers
- First page header
- Page orientation
- Font offset
- Embedded forms
- Embedded lists
- Page breaks
- Grid lines
- PDF generation

**Best For:**
- Invoices
- Purchase orders
- Statements
- Reports
- Receipts
- Quotes
- Certificates
- Work orders
- Packing slips
- Official documents

**Advantages:**
- Professional output
- Print optimization
- Flexible layout
- Header/footer support
- Multi-page documents
- PDF generation
- Embedded content
- Brand consistency
