# Text Search Fields

Text Search Fields specify which fields should be indexed for full-text search capabilities in a business class.

## Purpose

Text Search Fields are used to:
- Enable full-text search on specific fields
- Configure search indexing behavior
- Support faceted search
- Control translation indexing
- Optimize search performance

## Syntax

```
Text Search Fields
    (<FullFieldName> | <FieldGroupName>)…
        [is a facet]
        [(index words | index phrase)]
        [(index data translations | index base data only)]
```

**Note:** Will text search index persistent fields only.

## Field Attributes

### Is a Facet

```
[is a facet]
```

Marks the field as a facet for faceted search. Facets allow users to filter search results by specific field values.

### Index Mode

```
[(index words | index phrase)]
```

Controls how the field content is indexed:
- **index words** (default): Indexes individual words for word-based searching
- **index phrase**: Indexes the entire phrase for exact phrase matching

### Translation Indexing

```
[(index data translations | index base data only)]
```

Controls whether translations are indexed:
- **index data translations** (default): Indexes all language translations
- **index base data only**: Indexes only the base language data

## Examples

### Simple Text Search Fields

```
CUSTOMER is a BusinessClass
    Persistent Fields
        CUSTOMER is Customer
        CUSTOMERNAME is Alpha size 50
            text searchable
        DESCRIPTION is Alpha size 255
            text searchable
        NOTES is Text
            text searchable
    
    Text Search Fields
        CUSTOMERNAME
        DESCRIPTION
        NOTES
```

### Text Search with Facets

```
PRODUCT is a BusinessClass
    Persistent Fields
        PRODUCT is Product
        PRODUCTNAME is Alpha size 100
            text searchable
        DESCRIPTION is Alpha size 255
            text searchable
        CATEGORY is ProductCategory
            text searchable
        BRAND is Alpha size 50
            text searchable
    
    Text Search Fields
        PRODUCTNAME
        DESCRIPTION
        CATEGORY
            is a facet
        BRAND
            is a facet
```

### Text Search with Index Modes

```
DOCUMENT is a BusinessClass
    Persistent Fields
        DOCUMENT is Document
        TITLE is Alpha size 200
            text searchable
        CONTENT is Text
            text searchable
        KEYWORDS is Alpha size 500
            text searchable
    
    Text Search Fields
        TITLE
            index phrase
        CONTENT
            index words
        KEYWORDS
            index words
```

### Text Search with Translation Control

```
ARTICLE is a BusinessClass
    Persistent Fields
        ARTICLE is Article
        TITLE is Alpha size 200
            text searchable
            translatable
        BODY is Text
            text searchable
            translatable
        AUTHOR is Alpha size 100
            text searchable
    
    Text Search Fields
        TITLE
            index data translations
        BODY
            index data translations
        AUTHOR
            index base data only
```

### Text Search with Field Groups

```
EMPLOYEE is a BusinessClass
    Persistent Fields
        EMPLOYEE is Employee
        FIRSTNAME is Alpha size 50
            text searchable
        LASTNAME is Alpha size 50
            text searchable
        EMAIL is Alpha size 100
            text searchable
        DEPARTMENT is Department
            text searchable
        JOBTITLE is Alpha size 100
            text searchable
    
    Field Groups
        NameFields
            FIRSTNAME
            LASTNAME
        
        ContactFields
            EMAIL
    
    Text Search Fields
        NameFields
        ContactFields
        DEPARTMENT
            is a facet
        JOBTITLE
            is a facet
```

### Comprehensive Text Search Configuration

```
KNOWLEDGEBASE is a BusinessClass
    Persistent Fields
        ARTICLE is Article
        TITLE is Alpha size 200
            text searchable
            translatable
        SUMMARY is Alpha size 500
            text searchable
            translatable
        CONTENT is Text
            text searchable
            translatable
        CATEGORY is Alpha size 50
            text searchable
        TAGS is Alpha size 200
            text searchable
        AUTHOR is Alpha size 100
            text searchable
        LANGUAGE is Alpha size 10
    
    Text Search Fields
        TITLE
            index phrase
            index data translations
        
        SUMMARY
            index words
            index data translations
        
        CONTENT
            index words
            index data translations
        
        CATEGORY
            is a facet
            index words
            index base data only
        
        TAGS
            index words
            index base data only
        
        AUTHOR
            is a facet
            index base data only
```

## Use Cases

### Product Catalog Search

```
Text Search Fields
    PRODUCTNAME
        index phrase
    DESCRIPTION
        index words
    CATEGORY
        is a facet
    BRAND
        is a facet
    PRICE
        is a facet
```

### Document Management

```
Text Search Fields
    TITLE
        index phrase
        index data translations
    CONTENT
        index words
        index data translations
    DOCUMENTTYPE
        is a facet
    AUTHOR
        is a facet
```

### Customer Search

```
Text Search Fields
    CUSTOMERNAME
        index phrase
    EMAIL
        index words
    PHONE
        index words
    CUSTOMERTYPE
        is a facet
    REGION
        is a facet
```

### Knowledge Base

```
Text Search Fields
    TITLE
        index phrase
        index data translations
    KEYWORDS
        index words
    CONTENT
        index words
        index data translations
    CATEGORY
        is a facet
    TOPIC
        is a facet
```

## Best Practices

### Field Selection

- Include fields users will search
- Include fields with meaningful text content
- Exclude fields with codes or IDs unless needed
- Consider field size and content
- Balance comprehensiveness vs performance

### Facet Design

- Use facets for categorical fields
- Limit number of facets for usability
- Choose facets with reasonable cardinality
- Test facet performance
- Consider user filtering needs

### Index Mode Selection

- Use `index words` for general text content
- Use `index phrase` for titles and names
- Consider search patterns
- Test search relevance
- Balance precision vs recall

### Translation Indexing

- Index translations for multilingual content
- Use base data only for non-translatable fields
- Consider storage implications
- Test search in all languages
- Monitor index size

### Performance

- Limit number of indexed fields
- Consider field sizes
- Monitor index size and growth
- Test search performance
- Optimize as needed

### Maintenance

- Review indexed fields periodically
- Update as search needs change
- Monitor search usage patterns
- Test search quality
- Adjust indexing strategy

## Faceted Search

### What are Facets?

Facets are field values that can be used to filter search results. They provide:
- Category-based filtering
- Drill-down capabilities
- Result refinement
- Improved user experience

### Facet Examples

```
Text Search Fields
    CATEGORY
        is a facet
    BRAND
        is a facet
    PRICE
        is a facet
    RATING
        is a facet
```

Users can then filter results by:
- Category: Electronics, Clothing, Books
- Brand: Apple, Samsung, Sony
- Price: $0-$50, $50-$100, $100+
- Rating: 5 stars, 4 stars, 3 stars

### Facet Best Practices

- Choose fields with limited distinct values
- Avoid high-cardinality fields
- Use meaningful field values
- Test facet performance
- Consider user filtering patterns

## Index Modes

### Index Words

**Behavior:**
- Breaks text into individual words
- Indexes each word separately
- Supports word-based searching
- Better for general text content

**Best For:**
- Descriptions
- Content
- Notes
- Comments

**Example:**
```
Text Search Fields
    DESCRIPTION
        index words
```

Search "blue widget" finds:
- "This is a blue widget"
- "Widget available in blue"
- "Blue color widget"

### Index Phrase

**Behavior:**
- Indexes entire phrase
- Supports exact phrase matching
- Better for titles and names
- More precise matching

**Best For:**
- Titles
- Names
- Product codes
- Exact phrases

**Example:**
```
Text Search Fields
    PRODUCTNAME
        index phrase
```

Search "Blue Widget" finds:
- "Blue Widget Pro"
- "The Blue Widget"
But not:
- "Widget in Blue"

## Translation Indexing

### Index Data Translations

**Behavior:**
- Indexes all language translations
- Supports multilingual search
- Increases index size
- Better for global applications

**Example:**
```
Text Search Fields
    TITLE
        index data translations
```

Indexes:
- English: "Product"
- French: "Produit"
- German: "Produkt"

### Index Base Data Only

**Behavior:**
- Indexes only base language
- Smaller index size
- Faster indexing
- Better for single-language content

**Example:**
```
Text Search Fields
    AUTHOR
        index base data only
```

Indexes only the base language value.

## Performance Considerations

### Index Size

Factors affecting index size:
- Number of indexed fields
- Field sizes
- Number of records
- Translation indexing
- Index mode (words vs phrase)

### Search Performance

Factors affecting search performance:
- Index size
- Number of facets
- Query complexity
- Result set size
- Hardware resources

### Optimization Strategies

- Index only necessary fields
- Use appropriate index modes
- Limit facet fields
- Monitor index size
- Regular index maintenance
- Consider index partitioning

## Common Patterns

### Basic Search Pattern

```
Text Search Fields
    NAME
    DESCRIPTION
```

### Faceted Search Pattern

```
Text Search Fields
    NAME
    DESCRIPTION
    CATEGORY
        is a facet
    TYPE
        is a facet
```

### Multilingual Search Pattern

```
Text Search Fields
    TITLE
        index phrase
        index data translations
    CONTENT
        index words
        index data translations
```

### Mixed Mode Pattern

```
Text Search Fields
    TITLE
        index phrase
    DESCRIPTION
        index words
    KEYWORDS
        index words
```

## Related Concepts

### Text Searchable Attribute

Fields must be marked as `text searchable` in their definition to be eligible for text search indexing.

### Persistent Fields

Only persistent fields can be text search indexed.

### Field Groups

Field groups can be used to organize text search fields.

### Translatable Fields

Translatable fields can have their translations indexed.

## Troubleshooting

### Common Issues

1. **Field not searchable**: Ensure field is marked `text searchable`
2. **Search not finding results**: Check index mode and query
3. **Performance issues**: Reduce number of indexed fields
4. **Index too large**: Use base data only for some fields
5. **Facets not working**: Verify field is marked as facet

### Debugging Tips

- Verify fields are persistent
- Check `text searchable` attribute
- Test search queries
- Monitor index size
- Review search logs
- Test with various data
- Check translation indexing

## Validation Checklist

- [ ] All indexed fields are persistent
- [ ] All indexed fields are marked `text searchable`
- [ ] Appropriate index modes selected
- [ ] Facets chosen wisely
- [ ] Translation indexing configured
- [ ] Performance tested
- [ ] Index size monitored
- [ ] Search quality validated

## Summary

Text Search Fields provide:
- Full-text search capabilities
- Faceted search support
- Flexible indexing options
- Translation support
- Performance optimization

Proper configuration of Text Search Fields enables powerful search functionality while maintaining good performance and manageable index sizes.
