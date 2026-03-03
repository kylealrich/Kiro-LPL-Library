# LPL Library - Landmark Pattern Language Knowledge Base

A comprehensive knowledge base and reference library for Infor Landmark Pattern Language (LPL) and the Configuration Console.

## Overview

This repository serves as a complete reference and learning resource for developers working with Infor Landmark applications. It contains extensive documentation, real-world examples, and an AI-powered expert assistant to help you master LPL development.

### What is LPL?

Landmark Pattern Language (LPL) is the declarative language used to define business classes, user interfaces, business logic, and configurations in Infor Landmark applications. LPL enables:

- Business class definitions with fields, relations, and rules
- User interface components (forms, pages, panels, lists)
- Business logic through rules and actions
- Security and audit configurations
- Web service interfaces
- Multi-language support

### What is the Configuration Console?

The Configuration Console is a tool in the Infor Rich Client that allows administrators to customize Landmark applications without modifying source code. Changes are stored as overlays that take effect immediately, preserving the integrity of base application files.

## Repository Structure

```
├── knowledge-base/          # Complete LPL documentation
│   ├── docs/               # Getting started guides
│   ├── reference/          # Technical reference (85+ topics)
│   ├── examples/           # Code examples and patterns
│   ├── guides/             # Step-by-step tutorials
│   └── api/                # API documentation
├── Reference/              # Sample business class files
│   └── business class/     # Real .busclass examples
├── Output/                 # Generated code and examples
└── .kiro/                  # AI agent configuration
```

## Quick Start

### For New Users

1. Start with [Introduction to LPL](knowledge-base/docs/introduction.md)
2. Follow the [Getting Started Guide](knowledge-base/docs/getting-started.md)
3. Explore [Reference Materials](knowledge-base/reference/README.md)
4. Review [Examples](knowledge-base/examples/README.md)

### For Experienced Developers

- Browse the [Reference Directory](knowledge-base/reference/) for specific topics
- Examine real business classes in [Reference/business class/](Reference/business%20class/)
- Use the AI assistant for code generation and analysis

## Key Features

### Comprehensive Documentation

85+ reference documents covering every aspect of LPL:

**Core Concepts**
- Business class definitions and ontology
- Field types (persistent, transient, derived, local, context)
- Relations and references
- Actions and rules
- Patterns and best practices

**UI Components**
- Forms (action, search, summary, matrix, composite)
- Pages, panels, and lists
- Card views and drills
- Navigation and menus

**Business Logic**
- Field rules and validation
- Action rules (create, commit, delete, attach)
- Rule blocks and conditions
- State cycles
- Audit and security

**Advanced Topics**
- Web service interfaces
- Data source mapping
- Multi-threading patterns
- Translation and multi-language support
- Cube relations and analytics

### Real-World Examples

The `Reference/business class/` directory contains 40+ production business class files demonstrating:
- Asset management systems
- Financial transactions
- Contract management
- User and security management
- Batch processing patterns
- Integration patterns

### AI-Powered Assistant

This repository includes an LPL Expert Agent that can:

- Answer questions about LPL syntax and concepts
- Generate complete, working LPL code
- Analyze existing business classes
- Teach LPL patterns and best practices
- Update the knowledge base with new learnings
- Provide context-aware suggestions

## Common Use Cases

### Learning LPL

```
"Explain how to create a business class with persistent fields"
"What are the different types of rules in LPL?"
"Show me examples of relations between business classes"
```

### Code Generation

```
"Generate a business class for managing customer orders"
"Create an action to update user status with validation"
"Build a search form for finding invoices by date range"
```

### Analysis and Documentation

```
"Analyze the Asset.busclass file"
"What patterns are used in the Contract business class?"
"Document the field rules in PayablesInvoice"
```

### Troubleshooting

```
"Why is my constraint rule not working?"
"How do I fix 'field not found' errors?"
"What's the correct syntax for derived fields?"
```

## LPL Syntax Highlights

### Business Class Structure

```lpl
<BusinessClassName> is a BusinessClass
    owned by <ModuleName>
    sql name is <SQL_NAME>
    default label is untranslatable:"Display_Label"
    representative text is "<KeyField> - <Description>"
    
    Ontology
        stereotype is <Type>
        symbolic key is <KeyFields>
    
    Persistent Fields
        <FIELDNAME> is <DataType>
            default label is untranslatable
            required
    
    Relations
        <RelationName> is a <RelationType>
            business class is <TargetClass>
    
    Field Rules
        <FieldName>
            constraint
                <condition>
                    untranslatable:"Error_message"
    
    Actions
        Create is a Create Action
            default label is untranslatable:"Create_Record"
```

### Key Conventions

- Field names: UPPERCASE for persistent, lowercase for local
- Labels: Use `untranslatable` or `untranslatable:"Text_With_Underscores"`
- Indentation: Tab-based hierarchy
- Sections: Tab-indented section headers (e.g., `\tPersistent Fields`)

## Reference Documentation

### Most Used References

- [General Definitions](knowledge-base/reference/general-definitions.md) - LPL syntax fundamentals
- [Business Class Definition](knowledge-base/reference/business-class-definition.md) - Business class structure
- [Persistent Fields](knowledge-base/reference/persistent-fields.md) - Database-persisted fields
- [Actions](knowledge-base/reference/actions.md) - Action types and definitions
- [Field Rules](knowledge-base/reference/field-rules.md) - Validation and business logic
- [Relations](knowledge-base/reference/relations.md) - Business class relationships
- [Forms](knowledge-base/reference/forms.md) - User interface forms

### Complete Reference Index

See [knowledge-base/reference/README.md](knowledge-base/reference/README.md) for the complete list of 85+ reference documents.

## Contributing

### Adding Documentation

1. Place new documentation in the appropriate `knowledge-base/` subfolder
2. Follow the existing markdown structure and formatting
3. Update relevant README.md files
4. Cross-reference related topics

### Adding Examples

1. Save generated code to the `Output/` directory
2. Include complete, working examples
3. Add comments explaining key concepts
4. Reference relevant documentation

### Updating the Knowledge Base

The AI assistant automatically updates the knowledge base after each interaction, capturing:
- New patterns and syntax variations
- Better explanations and examples
- Common mistakes and solutions
- Real-world use cases

## Best Practices

### Code Generation

- Always use `untranslatable` or `untranslatable:"Text_With_Underscores"` for labels
- Include validation rules before processing rules
- Add meaningful error messages
- Use representative text instead of display fields
- Follow naming conventions (UPPERCASE for persistent fields)

### Business Class Design

- Define clear ontology with symbolic keys
- Use appropriate field types (persistent vs transient vs derived)
- Implement proper relations to other business classes
- Add constraint rules for data validation
- Include audit rules for tracking changes

### Performance

- Use `bypass field rules` carefully in actions
- Leverage derived fields for calculations
- Implement proper indexing through SQL names
- Use sets efficiently for bulk operations

### Security

- Define appropriate security classes
- Implement audit entry rules for sensitive data
- Use constraint rules to enforce business policies
- Validate user permissions in action rules

## Tools and Utilities

### Configuration Verification

- `cdverify` utility - Validates configuration syntax
- Configuration Console tools - Compare and analyze configurations
- Data menu forms - View and manage configurations

### Development Workflow

1. Design business class structure
2. Define fields and relations
3. Implement validation rules
4. Create actions with proper error handling
5. Build user interface forms
6. Test in provisional environment
7. Deploy to production

## Resources

### Official Documentation

- Infor Landmark Technology User Interface Guides
- Configuration Console documentation
- LPL reference materials (included in this repository)

### This Repository

- [Knowledge Base](knowledge-base/README.md) - Complete documentation
- [Reference Materials](knowledge-base/reference/README.md) - Technical specifications
- [Examples](knowledge-base/examples/README.md) - Code samples
- [Business Class Samples](Reference/business%20class/) - Real-world examples

## Support and Community

### Getting Help

- Use the AI assistant for immediate answers
- Review reference documentation for syntax details
- Examine example business classes for patterns
- Check the knowledge base for best practices

### Reporting Issues

When encountering problems:
1. Check the relevant reference documentation
2. Review similar examples in the Reference directory
3. Verify syntax against general definitions
4. Use the AI assistant for troubleshooting

## License

This knowledge base is provided for educational and reference purposes for Infor Landmark developers.

## Acknowledgments

This repository contains documentation and examples derived from:
- Infor Landmark official documentation
- Real-world production business classes
- Community contributions and best practices
- Continuous learning from developer interactions

---

**Ready to get started?** Begin with the [Introduction](knowledge-base/docs/introduction.md) or ask the AI assistant any question about LPL!

---

***LPL Tip:** Use `representative text` to define how business class instances display throughout the application - it's more maintainable than Display Fields and automatically updates when referenced fields change.*
