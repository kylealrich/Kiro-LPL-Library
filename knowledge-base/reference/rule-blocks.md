# Rule Blocks

## Overview

Rule Blocks are named blocks of rules that can be included (copied) in multiple places in the LPL code. They provide a mechanism for code reuse, allowing common rule logic to be defined once and referenced multiple times. Rule Blocks support replacement variables for parameterization, making them flexible and adaptable to different contexts.

Rule Blocks can be defined either within a business class or in separate Rule Blocks Definition Files (.ruleblocks extension) for sharing across multiple business classes.

## Syntax

### Defining a Rule Block (In Business Class)

```
Rule Blocks
    <RuleBlockName>
        <Rule>
        …
```

### Defining a Rule Block (In .ruleblocks File)

```
<RuleBlocks> is a RuleBlocks
    Rule Blocks
        <RuleBlockName>
            <Rule>
            …
```

### Including a Rule Block

```
include <RuleBlockName>
```

### Rule Block with Replacement Variables

```
Rule Blocks
    <RuleBlockName>
        <Rule using {VARIABLE_NAME}>
        …

// When including:
include <RuleBlockName>
    {VARIABLE_NAME} = <ActualValue>
```

## How Rule Blocks Work

1. **Definition** - Rule Block is defined in Rule Blocks section (business class or .ruleblocks file)
2. **Inclusion** - `include` statement references the Rule Block
3. **Compilation** - Block is copied into place during compilation
4. **Execution** - Rules execute as if written in-line

## Rule Blocks Definition Files

### File Structure

Rule Blocks can be defined in separate files with `.ruleblocks` extension:

```
<RuleBlocks> is a RuleBlocks
    Rule Blocks
        <RuleBlockName>
            <Rule>
            …
        <AnotherRuleBlockName>
            <Rule>
            …
```

### Benefits of Separate Files

- **Reusability** - Share Rule Blocks across multiple business classes
- **Organization** - Keep common logic in centralized location
- **Maintainability** - Single point of change for shared logic
- **Modularity** - Separate concerns and responsibilities

### File Naming

- Files have `.ruleblocks` extension
- Follow standard LPL naming conventions
- Example: `CommonValidations.ruleblocks`

### Usage

Rule Blocks from .ruleblocks files are included the same way as inline Rule Blocks:

```
include <RuleBlockName>
    {VARIABLE} = value
```

### Example .ruleblocks File

**File: StandardValidations.ruleblocks**
```
STANDARD_VALIDATIONS is a RuleBlocks
    Rule Blocks
        VALIDATE_REQUIRED_FIELDS
            constraint name not empty
                "Name is required"
            constraint description not empty
                "Description is required"
        
        VALIDATE_DATE_RANGE
            constraint {START_DATE} not empty
                "{START_DATE_LABEL} is required"
            constraint {END_DATE} >= {START_DATE}
                "{END_DATE_LABEL} must be on or after {START_DATE_LABEL}"
        
        UPDATE_TIMESTAMP
            {TIMESTAMP_FIELD} = today
            {USER_FIELD} = current.user
```

**Usage in Business Class:**
```
Create Rules
    include VALIDATE_REQUIRED_FIELDS
    include UPDATE_TIMESTAMP
        {TIMESTAMP_FIELD} = created.date
        {USER_FIELD} = created.by
```

## Replacement Variables

Rule Blocks can contain replacement variables:
- **Syntax** - LPLConstructNames bounded by curly braces: `{VARIABLE_NAME}`
- **Required** - All variables must be replaced when including the block
- **Compile-time** - Replacement occurs during compilation
- **Type-safe** - Variables must resolve to valid LPL constructs

## Use Cases

### Code Reuse
- Common validation logic
- Standard calculations
- Repeated patterns
- Shared business rules

### Consistency
- Enforce standard behavior
- Maintain uniform logic
- Reduce duplication
- Simplify maintenance

### Parameterization
- Adapt logic to context
- Use different fields
- Vary behavior
- Flexible implementation

### Maintainability
- Single point of change
- Easier updates
- Reduced errors
- Better organization

## Examples

### Example 1: Simple Rule Block

```
Rule Blocks
    VALIDATE_REQUIRED_FIELDS
        constraint name not empty
            "Name is required"
        constraint description not empty
            "Description is required"
        constraint status not empty
            "Status is required"

// Usage:
Create Rules
    include VALIDATE_REQUIRED_FIELDS
```

Reusable validation logic.

### Example 2: Rule Block with Replacement Variable

```
Rule Blocks
    UPDATE_TIMESTAMP
        {TIMESTAMP_FIELD} = today
        {USER_FIELD} = current.user

// Usage:
Update Action Rules
    include UPDATE_TIMESTAMP
        {TIMESTAMP_FIELD} = last.modified.date
        {USER_FIELD} = last.modified.by
```

Parameterized timestamp update.

### Example 3: Multiple Replacement Variables

```
Rule Blocks
    VALIDATE_DATE_RANGE
        constraint {START_DATE} not empty
            "{START_DATE_LABEL} is required"
        constraint {END_DATE} >= {START_DATE}
            "{END_DATE_LABEL} must be on or after {START_DATE_LABEL}"

// Usage:
Field Rules
    include VALIDATE_DATE_RANGE
        {START_DATE} = effective.date
        {END_DATE} = expiration.date
        {START_DATE_LABEL} = "Effective date"
        {END_DATE_LABEL} = "Expiration date"
```

Flexible date range validation.

### Example 4: Calculation Rule Block

```
Rule Blocks
    CALCULATE_TOTAL
        {TOTAL_FIELD} = 0
        for each {LINE_ITEMS}
            {TOTAL_FIELD} += each.{AMOUNT_FIELD}

// Usage:
Commit Rules
    include CALCULATE_TOTAL
        {TOTAL_FIELD} = order.total
        {LINE_ITEMS} = order.line set
        {AMOUNT_FIELD} = line.amount
```

Reusable calculation logic.

### Example 5: Audit Logging Block

```
Rule Blocks
    LOG_CHANGE
        log "{ACTION} performed on {RECORD_TYPE} " + record.id + 
            " by " + current.user
        
        invoke CREATE.AUDIT_LOG
            invoked.record.id = record.id
            invoked.action = "{ACTION}"
            invoked.user = current.user
            invoked.timestamp = current timestamp

// Usage:
Update Action Rules
    include LOG_CHANGE
        {ACTION} = "Update"
        {RECORD_TYPE} = "Customer"
```

Audit logging with parameters.

### Example: PurchaseOrder - Complex IDM XML Configuration Rule Block

From the PurchaseOrder business class, demonstrating a complex rule block for IDM XML generation:

```
Rule Blocks
    SetIDMXMLDefinitionValues
        initialize IDMXMLDefinition
        IDMXMLDefinition.Busclass = reference to this instance
        IDMXMLDefinition.ListName = "PurchaseOrderForIDM"
        IDMXMLDefinition.DocumentName = "PurchaseOrder"
        IDMXMLDefinition.OutputFileName = "Company_" + Company + "_" + "PurchaseOrder_" + PurchaseOrder + "_" + DerivedCurrentTimestamp

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[1].RelationName = "PrintLinesRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[1].ListName = "PurchaseOrderLinesForIDMDoc"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[1].LevelSection1 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[1].ListTag = "Lines"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[1].ItemTag = "Line"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[2].RelationName = "PrintablePurchaseOrderAndLineAddOnChargesForALineRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[2].ListName = "AOCForPOLineForIDM"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[2].LevelSection1 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[2].LevelSection2 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[2].ListTag = "LineAddOnCharge"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[2].ItemTag = "AddOnCharge"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[3].RelationName = "PrintOnPurchaseOrderLineCommentsRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[3].ListName = "PurchaseOrderLineCommentsForIDM"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[3].LevelSection1 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[3].LevelSection2 = 2
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[3].ListTag = "LineComment"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[3].ItemTag = "Comment"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[4].RelationName = "PurchaseOrderLineSourcesRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[4].ListName = "PurchaseOrderLineSourcesForIDM"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[4].LevelSection1 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[4].LevelSection2 = 3
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[4].ListTag = "LineSource"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[4].ItemTag = "Source"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[5].RelationName = "PrintBillOnlyDetailsRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[5].ListName = "POLinePatientProcedureInformationForIDMDoc"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[5].LevelSection1 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[5].LevelSection2 = 4
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[5].ListTag = "LinePatientProcedureInformation"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[5].ItemTag = "PatientProcedureInformation"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[6].RelationName = "ItemCommentToPrintRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[6].ListName = "ItemCommentIDMList"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[6].LevelSection1 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[6].LevelSection2 = 5
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[6].ListTag = "LineItemComment"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[6].ItemTag = "ItemComment"
        
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[7].RelationName = "ItemCommentAttachmentRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[7].ListName = "ItemCommentAttachmentForIDMList"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[7].LevelSection1 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[7].LevelSection2 = 5
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[7].LevelSection3 = 1
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[7].ListTag = "LineItemCommentDetail"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[7].ItemTag = "ItemCommentDetail"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[8].RelationName = "AddOnChargeForPrintingRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[8].ListName = "AddOnChargesForIDM"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[8].LevelSection1 = 2
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[8].ListTag = "HeaderAddOnCharge"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[8].ItemTag = "AddOnCharge"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[9].RelationName = "PrintOnPurchaseOrderCommentsRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[9].ListName = "PurchaseOrderCommentsForIDM"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[9].LevelSection1 = 3
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[9].ListTag = "PurchaseOrderComment"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[9].ItemTag = "Comment"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[10].RelationName = "TrailerCommentsRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[10].ListName = "PurchaseOrderCommentsForIDM"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[10].LevelSection1 = 4
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[10].ListTag = "PurchaseOrderTrailerComment"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[10].ItemTag = "Comment"

        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[11].RelationName = "HeaderAddOnChargesForPrintingRel"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[11].ListName = "AOCForPOLineForIDM"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[11].LevelSection1 = 5
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[11].ListTag = "PurchaseOrderHeaderAddOnCharges"
        IDMXMLDefinition.IDMXMLChildren.IDMXMLChild[11].ItemTag = "PurchaseOrderHeaderAddOnCharge"
```

This example demonstrates:
- **Complex initialization**: Setting up a multi-level XML structure
- **Array indexing**: Using array notation `IDMXMLChild[1]`, `IDMXMLChild[2]`, etc.
- **Hierarchical structure**: Defining parent-child relationships with LevelSection1, LevelSection2, LevelSection3
- **Dynamic file naming**: Concatenating fields to create unique output file names
- **Relation mapping**: Mapping business class relations to XML structure
- **List and item tags**: Defining XML element names for collections and items
- **Reference to instance**: Using `reference to this instance` for self-reference
- **Derived field usage**: Using DerivedCurrentTimestamp for unique naming
- **Multiple child levels**: Supporting nested XML structures up to 3 levels deep
- **Reusable configuration**: Encapsulating complex XML setup for reuse in multiple actions

Usage pattern:
```
Derived Fields
    DerivedIDMXml is a DerivedField
        type is Boolean
        if (not RetrievedIDMXML)
            include SetIDMXMLDefinitionValues
            constraint (IDMXMLDefinition.GenerateXML)
                "ErrorOccuredInIDMXMLDefinition.GenerateXML"
            RetrievedIDMXML = true
        return true
```

This rule block is used to configure IDM (Infor Document Management) XML generation for purchase order documents, defining the complete structure including lines, add-on charges, comments, and sources.
            invoked.timestamp = today

// Usage:
Update Action Rules
    include LOG_CHANGE
        {ACTION} = "Update"
        {RECORD_TYPE} = "Order"
```

Standard audit logging.

### Example 6: Notification Rule Block

```
Rule Blocks
    SEND_CHANGE_NOTIFICATION
        send notification
            to {RECIPIENT}
            description is "{DESCRIPTION}"
            detail is "{DETAIL_PREFIX}" + record.id
            priority is {PRIORITY}

// Usage:
Create Exit Rules
    include SEND_CHANGE_NOTIFICATION
        {RECIPIENT} = record.owner
        {DESCRIPTION} = "New record created"
        {DETAIL_PREFIX} = "Record created: "
        {PRIORITY} = low
```

Parameterized notifications.

### Example 7: Validation Rule Block

```
Rule Blocks
    VALIDATE_AMOUNT_RANGE
        constraint {AMOUNT_FIELD} >= {MINIMUM}
            "{FIELD_LABEL} must be at least {MINIMUM}"
        constraint {AMOUNT_FIELD} <= {MAXIMUM}
            "{FIELD_LABEL} cannot exceed {MAXIMUM}"

// Usage:
Field Rules
    include VALIDATE_AMOUNT_RANGE
        {AMOUNT_FIELD} = discount.amount
        {MINIMUM} = 0
        {MAXIMUM} = 1000
        {FIELD_LABEL} = "Discount amount"
```

Flexible range validation.

### Example 8: Status Update Block

```
Rule Blocks
    UPDATE_STATUS_FIELDS
        {STATUS_FIELD} = "{NEW_STATUS}"
        {STATUS_DATE_FIELD} = today
        {STATUS_USER_FIELD} = current.user
        
        log "Status changed to {NEW_STATUS}"

// Usage:
Action Rules
    include UPDATE_STATUS_FIELDS
        {STATUS_FIELD} = approval.status
        {NEW_STATUS} = "Approved"
        {STATUS_DATE_FIELD} = approval.date
        {STATUS_USER_FIELD} = approved.by
```

Standard status update pattern.

### Example 9: Counter Update Block

```
Rule Blocks
    INCREMENT_COUNTER
        increment {COUNTER_FIELD}
        {LAST_UPDATE_FIELD} = today
        
        if {COUNTER_FIELD} >= {THRESHOLD}
            {THRESHOLD_REACHED_FLAG} = true

// Usage:
Update Action Rules
    include INCREMENT_COUNTER
        {COUNTER_FIELD} = modification.count
        {LAST_UPDATE_FIELD} = last.modification.date
        {THRESHOLD} = 10
        {THRESHOLD_REACHED_FLAG} = requires.review
```

Reusable counter logic.

### Example 10: Nested Rule Block Usage

```
Rule Blocks
    VALIDATE_AND_LOG
        include VALIDATE_REQUIRED_FIELDS
        include LOG_CHANGE
            {ACTION} = "{CURRENT_ACTION}"
            {RECORD_TYPE} = "{CURRENT_TYPE}"

// Usage:
Create Rules
    include VALIDATE_AND_LOG
        {CURRENT_ACTION} = "Create"
        {CURRENT_TYPE} = "Product"
```

Rule Blocks can include other Rule Blocks.

### Example 11: Conditional Logic Block

```
Rule Blocks
    APPLY_DISCOUNT
        if {AMOUNT_FIELD} > {THRESHOLD}
            {DISCOUNT_FIELD} = {AMOUNT_FIELD} * {DISCOUNT_RATE}
        else
            {DISCOUNT_FIELD} = 0

// Usage:
Commit Rules
    include APPLY_DISCOUNT
        {AMOUNT_FIELD} = order.subtotal
        {THRESHOLD} = 1000
        {DISCOUNT_FIELD} = volume.discount
        {DISCOUNT_RATE} = 0.10
```

Conditional logic with parameters.

### Example 12: Parent Update Block

```
Rule Blocks
    UPDATE_PARENT_TOTALS
        {PARENT}.{TOTAL_FIELD} += {AMOUNT_FIELD}
        {PARENT}.{COUNT_FIELD} += 1
        {PARENT}.last.modified = today

// Usage:
Create Exit Rules
    include UPDATE_PARENT_TOTALS
        {PARENT} = order
        {TOTAL_FIELD} = total.amount
        {AMOUNT_FIELD} = line.amount
        {COUNT_FIELD} = line.count
```

Update parent aggregates.

### Example 13: Security Check Block

```
Rule Blocks
    VALIDATE_SECURITY
        constraint current.user.has.permission("{PERMISSION}")
            "Permission denied: {PERMISSION} required"
        
        constraint {SECURITY_LEVEL_FIELD} <= current.user.security.level
            "Insufficient security clearance"

// Usage:
Delete Rules
    include VALIDATE_SECURITY
        {PERMISSION} = "DeleteRecords"
        {SECURITY_LEVEL_FIELD} = record.security.level
```

Reusable security validation.

### Example 14: Workflow Trigger Block

```
Rule Blocks
    TRIGGER_WORKFLOW
        if {CONDITION}
            initiate {WORKFLOW_NAME} workflow
                Variables
                    record.id
                    {WORKFLOW_VARIABLE} = {WORKFLOW_VALUE}

// Usage:
Update Action Rules
    include TRIGGER_WORKFLOW
        {CONDITION} = status = "Submitted"
        {WORKFLOW_NAME} = APPROVAL_WORKFLOW
        {WORKFLOW_VARIABLE} = approval.level
        {WORKFLOW_VALUE} = required.approval.level
```

Parameterized workflow initiation.

### Example 15: Complex Validation Block

```
Rule Blocks
    VALIDATE_BUSINESS_RULES
        constraint {FIELD1} not empty
            "{LABEL1} is required"
        
        if {FIELD2} not empty
            constraint {FIELD2} >= {FIELD1}
                "{LABEL2} must be greater than or equal to {LABEL1}"
        
        constraint {FIELD3} in {VALID_VALUES}
            "{LABEL3} must be one of the allowed values"

// Usage:
Field Rules
    include VALIDATE_BUSINESS_RULES
        {FIELD1} = start.date
        {FIELD2} = end.date
        {FIELD3} = status
        {LABEL1} = "Start date"
        {LABEL2} = "End date"
        {LABEL3} = "Status"
        {VALID_VALUES} = ("Active", "Pending", "Closed")
```

Complex parameterized validation.

## Best Practices

1. **Use for Common Logic**
   - Repeated validation patterns
   - Standard calculations
   - Common workflows
   - Shared business rules

2. **Choose Appropriate Location**
   - Use inline Rule Blocks for business class-specific logic
   - Use .ruleblocks files for shared logic across classes
   - Organize by functional area or domain
   - Consider reusability when deciding location

3. **Name Descriptively**
   - Use clear, meaningful names
   - Indicate purpose
   - Follow naming conventions
   - Be consistent

4. **Document Parameters**
   - List all replacement variables
   - Explain purpose of each
   - Provide usage examples
   - Note constraints

5. **Keep Blocks Focused**
   - Single responsibility
   - Cohesive logic
   - Reasonable size
   - Clear purpose

6. **Use Meaningful Variable Names**
   - Descriptive variable names
   - Indicate expected type
   - Follow conventions
   - Be consistent

7. **Organize .ruleblocks Files**
   - Group related Rule Blocks
   - Use consistent file naming
   - Document file purpose
   - Maintain file structure

8. **Test Thoroughly**
   - Test with various parameters
   - Verify all usages
   - Check edge cases
   - Validate replacements

9. **Maintain Carefully**
   - Consider impact of changes
   - Update all usages
   - Document changes
   - Version if needed

10. **Version Control**
    - Track changes to .ruleblocks files
    - Document breaking changes
    - Consider backward compatibility
    - Communicate updates to users

## Common Patterns

### Validation Pattern
```
Rule Blocks
    VALIDATE_{ENTITY}
        constraint {FIELD} {OPERATOR} {VALUE}
            "{MESSAGE}"
```

### Calculation Pattern
```
Rule Blocks
    CALCULATE_{RESULT}
        {RESULT_FIELD} = {EXPRESSION}
```

### Logging Pattern
```
Rule Blocks
    LOG_{ACTION}
        log "{MESSAGE}" + {CONTEXT}
```

### Update Pattern
```
Rule Blocks
    UPDATE_{ENTITY}
        {FIELD} = {VALUE}
        {TIMESTAMP} = today
```

### Notification Pattern
```
Rule Blocks
    NOTIFY_{EVENT}
        send notification
            to {RECIPIENT}
            description is "{MESSAGE}"
```

## Replacement Variable Syntax

### Variable Definition
```
{VARIABLE_NAME}
```

### Variable Replacement
```
include RULE_BLOCK_NAME
    {VARIABLE_NAME} = actual.value
    {ANOTHER_VARIABLE} = "literal value"
```

### Multiple Occurrences
The same variable can appear multiple times in the block; all occurrences are replaced with the same value.

## Compilation Behavior

1. **Parse** - Rule Block definition is parsed
2. **Include** - `include` statement is encountered
3. **Replace** - Variables are replaced with actual values
4. **Copy** - Resulting rules are copied in-line
5. **Compile** - Rules compile as if written directly

## Troubleshooting

### Issue: Rule Block not found

**Possible Causes:**
- Typo in Rule Block name
- Rule Block not defined
- Case sensitivity
- Scope issue

**Solutions:**
- Verify Rule Block name
- Check Rule Blocks section
- Match case exactly
- Verify definition exists

### Issue: Variable not replaced

**Possible Causes:**
- Missing variable in include
- Typo in variable name
- Case mismatch
- Syntax error

**Solutions:**
- Provide all required variables
- Check variable names
- Match case exactly
- Verify syntax

### Issue: Compilation error

**Possible Causes:**
- Invalid replacement value
- Type mismatch
- Syntax error in block
- Invalid LPL construct

**Solutions:**
- Verify replacement values
- Check types
- Review block syntax
- Validate constructs

### Issue: Unexpected behavior

**Possible Causes:**
- Wrong variable values
- Logic error in block
- Context mismatch
- Scope issue

**Solutions:**
- Review variable values
- Test block logic
- Verify context
- Check scope

### Issue: Maintenance problems

**Possible Causes:**
- Block used in many places
- Unclear dependencies
- Poor documentation
- Complex logic

**Solutions:**
- Document all usages
- Track dependencies
- Improve documentation
- Simplify logic

## Related Concepts

- [Field Rules](field-rules.md) - Field-level rules
- [Action Rules](action-rules.md) - Action-level rules
- [Commit Rules](commit-rules.md) - Transaction rules
- [Conditions](conditions.md) - Boolean expressions
- [Business Class Definition](business-class-definition.md) - Business class structure

## Notes

- Rule Blocks are copied in-line during compilation
- Replacement variables must be bounded by curly braces
- All replacement variables must be provided when including
- Rule Blocks can include other Rule Blocks
- Variables are replaced at compile-time, not runtime
- Use Rule Blocks for code reuse and consistency
- Keep Rule Blocks focused and well-documented
- Test Rule Blocks with various parameter values
- Consider impact when modifying Rule Blocks
- Rule Blocks improve maintainability and reduce duplication
- Variable names should be descriptive and follow conventions
- Document expected variable types and constraints
- Rule Blocks are a powerful tool for DRY (Don't Repeat Yourself) principle
- Consider creating a library of common Rule Blocks in .ruleblocks files
- Version Rule Blocks if they're widely used and may change
- .ruleblocks files enable sharing Rule Blocks across multiple business classes
- File extension is `.ruleblocks`
- Rule Blocks from files are included the same way as inline blocks
- Organize .ruleblocks files by functional area or domain
- Maintain documentation for shared Rule Blocks files
