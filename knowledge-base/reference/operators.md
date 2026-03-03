# Operators

## As Of Date Operator

```
AsOfDate ::= ( as of (<RelatedValue> | all dates)
             | after <RelatedValue>
             | before <RelatedValue>
             | between <RelatedValue> and <RelatedValue>)
// RelatedValue must be a Date or TimeStamp

AsOfOperator ::= <Parens><AsOfDate><Parens>
```

## Constants

```
Constant ::= ( true
             | false
             | blank
             | high value)
```

## Create or Update Mode

```
CreateOrUpdateMode ::= mode
// valid only in the title of a Form; results in 'Create' or 'Update'
```

## Field Operators

```
FieldOperator ::= ( not              // valid only with a Condition
                  | !                // "
                  | no               // "
                  | old
                  | any              // valid only with multi-valued (array, otm) RelatedField or RelatedCondition
                  | all              // "
                  | first            // "
                  | last             // "
                  | sum              // "
                  | avg              // "
                  | min              // "
                  | max              // "
                  | floor
                  | ceiling
                  | pending
                  | instance count of // valid only with multi-valued fields
                  | sizeofarray
                  | (first | last) iteration of // valid with a OTM RelatedLink only
                  | next             // valid on Iteration fields only
                  | previous         // "
                  | reference to     // valid only with a OTO RelatedLink only – returns the BusinessObjectReference of the RelatedLink
                  | target of)       // valid only with an "is related value…" designated field
```

### Field Operator Categories

#### Logical Operators
- `not`, `!`, `no` - Negation (valid only with Condition)

#### Historical Data
- `old` - Previous value of a field

#### Multi-valued Field Operators
Valid only with multi-valued (array, OTM) RelatedField or RelatedCondition:
- `any` - True if any element matches
- `all` - True if all elements match
- `first` - First element
- `last` - Last element
- `sum` - Sum of all elements
- `avg` - Average of all elements
- `min` - Minimum value
- `max` - Maximum value

#### Mathematical Operators
- `floor` - Round down to nearest integer
- `ceiling` - Round up to nearest integer

#### Array Operators
- `sizeofarray` - Size of the array
- `instance count of` - Count of instances

#### Iteration Operators
Valid with OTM RelatedLink:
- `first iteration of` - First iteration
- `last iteration of` - Last iteration

Valid on Iteration fields only:
- `next` - Next iteration
- `previous` - Previous iteration

#### Reference Operators
- `reference to` - Returns BusinessObjectReference (valid with OTO RelatedLink only)
- `target of` - Returns target (valid with "is related value..." designated field)

#### Status Operators
- `pending` - Pending value

## Link Operators

```
LinkOperator ::= ( first
                 | last)
```

## Period Operators

```
PeriodOperator ::= ( [(prior | next) year [<#>]] [(prior | next)] (period[s] | week[s] | month[s] | quarter[s] | [all] year[s]) [<#> [thru <#>]]
                   | [(prior | next) year [<#>]] [(prior | next)] (period | week | month | quarter | year) [<#>] (beginning | ending) [ytd] balance)

PeriodLabel ::= period label
// valid only in the context of a period in a CubeView
```

### Period Operator Examples
- `prior year period 3` - Period 3 of the previous year
- `next quarter 2` - Quarter 2 of the next year
- `period 1 thru 6` - Periods 1 through 6
- `month beginning balance` - Beginning balance for the month
- `year ending ytd balance` - Year-to-date ending balance

## Total Operators

```
TotalOperator ::= ( running total
                  | percent of total)
```

## Type Operators

```
TypeOperator ::= ( decimals           // valid with a Decimal type field only
                 | type               // field type – returns string of <PrimitiveType; e.g., Alpha, Decimal, Numeric…
                 | size               // size of the 'string' value of a field
                 | uppercase
                 | lowercase
                 | year               // valid with Date and TimeStamp fields only
                 | month              // " – 1-based month number (1-12)
                 | month [short] name // "
                 | day                // " – 1-based day number (1-31)
                 | day [short] name   // "
                 | week day           // " – 1-based week day number (1-7)
                 | corporate week day // week day based on corporate TimeZone
                 | week day using <RelatedValue> // week day based on RelatedValue TimeZone (must be a TimeZone)
                 | year day           // " – 1-based year day number (1-366)
                 | week               // " – 1-based week number (1-52)
                 | week year          // " – year corresponding to week operator (12/31/15) = week 1, week year 16
                 | days in month      // " – number of days in the date's month (28-31)
                 | days in year       // " – number of days in the date's year (either 365 or 366)
                 | as years           // valid with Numeric type field only – treats value as number of years
                 | as months          // valid with Numeric type field only – treats value as number of months
                 | as days            // valid with Numeric type field only – treats value as number of days
                 | hours              // valid with Time type fields only – Decimal total number of hours since day beginning
                 | minutes            // " – Decimal total number of minutes since beginning of day
                 | seconds            // " – Decimal total number of seconds since beginning of day
                 | date               // valid with TimeStamp type fields only (returns Date in GMT time zone)
                 | corporate date     // valid with TimeStamp type fields only (returns Date in corporate time zone)
                 | system date        // valid with TimeStamp type fields only (returns Date in system's default time zone)
                 | date using <RelatedValue> // valid with TimeStamp type fields only, RelatedValue must be a TimeZone
                 | time               // valid with TimeStamp type fields only
                 | anniversary        // valid with Date and TimeStamp type fields only
                 | period             // valid with Date and TimeStamp type fields only
                 | levels             // valid with array fields only. Defines number of entries up to and including last entered value
                 | level <#>          // valid with key field that is a hierarchy; specifies which level in the hierarchy to address
                 | entries            // valid with array fields only. Defines number of entered fields in the array
                 | compact format     // valid only with a field that implements CompactFormat
                 | version            // valid only with a key field that implements Versioning; will address the version field
                 | label              // returns the translated label for the field
                 | [full] name        // returns the LPL name or full name of the field
                 | text               // valid only with a field that has Text Variable; retrieves the text with the variables replaced
                 | [exact] translation // returns the data translation for the current locale – "exact" will prevent standard defaulting
                 | mime type          // valid with BinaryDocument and BinaryObject only
                 | in base64          // valid with BinaryDocument and BinaryObject only
                 | translations       // returns the set of data translation for a field; valid on right side of assignment operator only
                 | document location  // returns <document location.(Local, S3, External)>; can be modified
                 | (local | external) document // returns the local or external document; external includes AWSS3 location
                 | select <SelectStatement>
                 | select line <SelectLineStatement>
                 | document [for <RelatedLink>]
                   // valid only with a field that is a 'document template'. This will retrieve the fully replaced
                   // document if no related link is specified then it is presumed to be based on 'this instance'
                   // if RelatedLink is an action request id, document will be replaced based on the action request
                 | compute value [for <RelatedLink>]
                   // valid with a field that is a BusinessClassCompute only
                 | as of <RelatedValue> // related value must be a Date or TimeStamp
                 | (date | audit entry) (first | last | next) changed [(from | to)<RelatedValue>]
                 | days was <RelatedValue> [while <Condition>] [between <RelatedValue> and <RelatedValue>]
                 | weighted average [between <RelatedValue> and <RelatedValue>]
                 | <PeriodOperator>
                 | cube dimension value [using year of <RelatedValue>]
                   // a dimension value in a cube is typically different than the BusinessClass value
                   // a Period dimension that is not satisfied with a date must have a year specified
                 | input value        // always returns the input value on a transient field with a derived value
                 | plain text         // strips all formatting (except new line) from RichText fields
                 | split [on <Literal>] // splits string into an array of strings using 'Literal' as separator (comma is default)
                 | using <JavaFormat> [using <RelatedValue>]
                   // RelatedValue valid only with TimeStamp and must be a TimeZone
                 | (short | medium | long) timestamp [with day [short] name][(show | hide) time zone][(show | hide) seconds][using <RelatedValue>]
                   // valid with TimeStamp – returns a String
                   // RelatedValue valid only with TimeStamp and must be a TimeZone
                 | (short | medium | long) date [with day [short] name][using <RelatedValue>]
                   // valid with TimeStamp and Date – returns a String
                   // RelatedValue valid only with TimeStamp and must be a TimeZone
                 | (short | medium | long) anniversary [with day [short] name][using <RelatedValue>]
                   // valid with TimeStamp, Date, and Anniversary – returns a String
                   // RelatedValue valid only with TimeStamp and must be a TimeZone
                 | (short | medium | long) period[using <RelatedValue>])
                   // valid with TimeStamp, Date, and Period – returns a String
                   // RelatedValue valid only with TimeStamp and must be a TimeZone
```

### Type Operator Categories

#### Field Metadata
- `type` - Returns the primitive type as a string
- `size` - Size of the string value
- `label` - Translated label for the field
- `name` / `full name` - LPL name of the field
- `decimals` - Number of decimal places (Decimal fields only)

#### String Operations
- `uppercase` - Convert to uppercase
- `lowercase` - Convert to lowercase
- `plain text` - Strip formatting from RichText (preserves newlines)
- `split [on <Literal>]` - Split string into array (default separator: comma)

#### Date/Time Operations
Valid with Date and TimeStamp fields:
- `year` - Year component
- `month` - Month number (1-12)
- `month name` / `month short name` - Month name
- `day` - Day of month (1-31)
- `day name` / `day short name` - Day name
- `week day` - Day of week (1-7)
- `corporate week day` - Week day in corporate timezone
- `week day using <RelatedValue>` - Week day in specified timezone
- `year day` - Day of year (1-366)
- `week` - Week number (1-52)
- `week year` - Year corresponding to week
- `days in month` - Number of days in month (28-31)
- `days in year` - Days in year (365 or 366)
- `anniversary` - Anniversary representation
- `period` - Period representation

#### TimeStamp Specific Operations
- `date` - Extract date in GMT
- `corporate date` - Extract date in corporate timezone
- `system date` - Extract date in system timezone
- `date using <RelatedValue>` - Extract date in specified timezone
- `time` - Extract time component
- `hours` - Total hours since day beginning
- `minutes` - Total minutes since day beginning
- `seconds` - Total seconds since day beginning

#### Numeric Time Conversions
Valid with Numeric fields:
- `as years` - Treat value as number of years
- `as months` - Treat value as number of months
- `as days` - Treat value as number of days

#### Array Operations
- `levels` - Number of entries up to last entered value
- `entries` - Number of entered fields in array
- `level <#>` - Specific level in hierarchy

#### Document Operations
- `mime type` - MIME type (BinaryDocument/BinaryObject)
- `in base64` - Base64 encoding (BinaryDocument/BinaryObject)
- `document location` - Location type (Local, S3, External)
- `local document` / `external document` - Retrieve document
- `document [for <RelatedLink>]` - Document template with replacements

#### Translation Operations
- `translation` / `exact translation` - Data translation for current locale
- `translations` - Set of all data translations (assignment only)

#### Formatting Operations
- `using <JavaFormat>` - Format using Java format string
- `short` / `medium` / `long` + `timestamp` / `date` / `anniversary` / `period` - Formatted date/time strings

#### Special Operations
- `compact format` - Compact format representation
- `version` - Version field (versioned keys only)
- `text` - Text with variables replaced (Text Variable fields)
- `input value` - Input value on transient derived fields
- `compute value [for <RelatedLink>]` - Computed value (BusinessClassCompute)
- `as of <RelatedValue>` - Historical value
- `cube dimension value` - Dimension value in cube context

#### Audit Operations
- `(date | audit entry) (first | last | next) changed` - Change tracking
- `days was <RelatedValue>` - Duration calculation
- `weighted average` - Weighted average calculation

## References

For Java formatting:
- [Java Formatter Documentation](http://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html)
- [Java DateFormat Documentation](http://docs.oracle.com/javase/7/docs/api/java/text/DateFormat.html)
- [Java Date Formatting Tutorial](https://docs.oracle.com/javase/tutorial/i18n/format/dateFormat.html)
