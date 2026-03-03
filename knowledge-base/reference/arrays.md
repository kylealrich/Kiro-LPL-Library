# Arrays

## Array Component

```
ArrayComponent ::= ( <array RelatedField>
                   | <array RelationName>
                   | <ArrayRangeAddress>
                   | <special index SingleValueArrayAddress>)
```

### Array Component Types

A Field or a Relation can be an array:

#### Array Relation
A Relation becomes an Array Relation when one of the Values being mapped to the index value is an array (this is done using the `each` operator).

#### Array Field
A Field becomes an array when:
- It is an ArrayField, or
- It is a derived field that contains an ArrayField without a single subscript denoting a specific occurrence

#### Simple Alpha Field
An array RelatedField can also be a simple Alpha field which then implicitly addresses each character.

### Array Expressions

The RelatedField expression `(A + each B)` where B is an ArrayField of size 10 returns an array of size 10 where A is added to each occurrence of B.

### Array Range Address
An ArrayRangeAddress is a subset of an array that can result in a single occurrence or even no occurrence.

### Special Index Array Address
A special index SingleValueArrayAddress is an array with a specific special index variable associated with it.

### Default Index Variable

By default, all ArrayComponents are associated with the special index variable `i` except when two arrays are used in an expression where it is ambiguous as to how to associate their respective occurrences.

**Example:** `(Array1 * Array2)` - Is the result a single dimension array or a two dimension array?

## Array Range Address

```
ArrayRangeAddress ::= <ArrayComponent>'['[<I>=]<from IndexVariable>:<to IndexVariable>']'
```

This expression defines a subset range of the ArrayComponent denoted by two index variables:
- **from IndexVariable**: Starting index
- **to IndexVariable**: Ending index (inclusive)

### Array Range Size

The size of this array is:
```
(to IndexVariable - from IndexVariable) + 1
```

### Examples

```
MyArray[1:5]        // First 5 elements
MyArray[i=3:7]      // Elements 3 through 7, explicitly using index i
MyArray[10:15]      // Elements 10 through 15
```

## Special Index Variables (I)

```
I ::= ( i | j | k )
```

These are internal array indexing variables for special use only within array expressions.

### Purpose

Their purpose is to allow the explicit mapping of multiple array occurrences in a single expression.

### The `i` Variable

The `i` variable is implicitly used and is accessible in any array expression:

```
for each <ArrayComponent>
```

is equivalent to:

```
for each <ArrayComponent>[i=1:arraysize]
```

The special index variable `i` can be used to explicitly address an occurrence within the for loop.

### Comparing Occurrences

This can be useful for comparing the current occurrence with the next occurrence:

```
if (A[i] == A[i+1])
```

### Array Component with Special Index

Any Array indexed with a special index is still considered an ArrayComponent as it implicitly means all occurrences in the absence of any array occurrence context.

### Multiple Index Variables

Use `j` and `k` when working with nested arrays or multiple array expressions:

```
// Example with two arrays
for each Array1[i=1:sizeofarray Array1]
    for each Array2[j=1:sizeofarray Array2]
        Result[i][j] = Array1[i] * Array2[j]
```

## Index Variable

```
IndexVariable ::= ( <RelatedValue>
                  | <I>
                  | <SizeOfArray>)
```

### Array Indexing Rules

- Arrays are indexed beginning with **1**
- The first occurrence of an array is denoted with an index of **1**
- An index variable can be:
  - A RelatedValue that is not an array
  - The special index variables `i`, `j`, and `k`
  - The size of an array using `sizeofarray`

### Examples

```
MyArray[1]                    // First element
MyArray[5]                    // Fifth element
MyArray[i]                    // Current index in loop
MyArray[Counter]              // Index from a field value
MyArray[sizeofarray MyArray]  // Last element
```

## Single Value Array Address

```
SingleValueArrayAddress ::= <ArrayComponent>'['<IndexVariable>']'
```

A specific value in an array is addressed by using hard brackets `[ ]` to denote the index of the specific occurrence.

### Special Index Variable Context

The special index variables only have meaning when used within an array context:
- An ArrayExpression
- A `for each <ArrayComponent>` scoped statement

### Examples

```
// Access specific elements
Employee[1]           // First employee
Employee[i]           // Current employee in loop
Employee[i+1]         // Next employee
Employee[LastIndex]   // Employee at LastIndex position

// In a for each loop
for each LineItems
    if LineItems[i].Amount > 1000
        // Process high-value line items
```

## Size of Array

```
SizeOfArray ::= sizeofarray <ArrayComponent>
```

Returns the size of the first array component in the ArrayComponent (an ArrayComponent can be made up of several ArrayComponents).

### Examples

```
sizeofarray MyArray              // Returns the size of MyArray
sizeofarray Employee.PhoneNumbers // Returns number of phone numbers

// Using in expressions
for each MyArray[i=1:sizeofarray MyArray]
    // Process each element

// Accessing last element
LastElement = MyArray[sizeofarray MyArray]

// Checking array bounds
if Index <= sizeofarray MyArray
    Value = MyArray[Index]
```

## Array Usage Examples

### Basic Array Iteration

```
for each LineItems
    Total = Total + LineItems[i].Amount
```

### Array Range Processing

```
// Process first 10 items
for each LineItems[1:10]
    // Process item

// Process items from index 5 to end
for each LineItems[5:sizeofarray LineItems]
    // Process item
```

### Comparing Adjacent Elements

```
for each Prices[i=1:sizeofarray Prices - 1]
    if Prices[i] > Prices[i+1]
        // Price decreased
```

### Multi-dimensional Array Operations

```
for each Matrix1[i=1:sizeofarray Matrix1]
    for each Matrix2[j=1:sizeofarray Matrix2]
        Result[i][j] = Matrix1[i] * Matrix2[j]
```

### Array Expressions

```
// Add scalar to each array element
Result = 10 + each MyArray

// Multiply arrays element-wise (requires explicit indexing)
for each Array1[i=1:sizeofarray Array1]
    Result[i] = Array1[i] * Array2[i]

// Sum of array elements
Total = 0
for each Values
    Total = Total + Values[i]
```

### Character Array (Alpha Field)

```
// An Alpha field can be treated as a character array
Name = "JOHN"
for each Name  // Iterates through each character
    if Name[i] == "O"
        // Found the letter O
```

## Best Practices

### Index Variable Usage

- Use `i` for single array operations
- Use `j` and `k` for nested array operations
- Always check array bounds before accessing elements

### Array Size

- Use `sizeofarray` to get the current size
- Don't hardcode array sizes when possible
- Check for empty arrays before processing

### Performance Considerations

- Minimize nested array loops when possible
- Use array ranges to process subsets efficiently
- Consider the size of arrays in expressions

### Clarity

- Use meaningful variable names even with special indices
- Comment complex array expressions
- Break down complex multi-dimensional operations into simpler steps
