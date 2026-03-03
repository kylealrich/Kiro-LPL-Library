# Dates and Time Stamps

## Create Date and Effective Date

### Create Date
```
CreateDate ::= create date
```
Returns the most recent create date from the current 'as of' date.

### Create Effective Date
```
CreateEffectiveDate ::= create effective date
```
Returns the most recent create effective date from the current 'as of' date.

## Create Stamp

```
CreateStamp ::= create stamp[.actor]
```

References the CreateStamp in the business class:
- **By itself**: Returns the actual TimeStamp when the instance was created
- **With .actor**: References the actor who created this instance

Example:
- `create stamp` → Returns timestamp
- `create stamp.actor` → Returns the creating actor

## Current Date and Time Functions

```
CurrentDateTime ::= ( current [(corporate | user)] time
                    | [system] current [(corporate | user)] date
                    | [system] current [(corporate | user)] year
                    | [system] current [corporate] period
                    | [system] current timestamp
                    | [system] current [(corporate | user)] anniversary)
```

### Available Functions

#### Time
- `current time` - Current time (default context)
- `current corporate time` - Current time in corporate context
- `current user time` - Current time in user context

#### Date
- `current date` - Current date (default context)
- `system current date` - System current date
- `current corporate date` - Current date in corporate context
- `system current corporate date` - System current date in corporate context
- `current user date` - Current date in user context
- `system current user date` - System current date in user context

#### Year
- `current year` - Current year (default context)
- `system current year` - System current year
- `current corporate year` - Current year in corporate context
- `system current corporate year` - System current year in corporate context
- `current user year` - Current year in user context
- `system current user year` - System current year in user context

#### Period
- `current period` - Current period (corporate context only)
- `system current period` - System current period
- `current corporate period` - Current period in corporate context
- `system current corporate period` - System current period in corporate context

#### Timestamp
- `current timestamp` - Current timestamp
- `system current timestamp` - System current timestamp

#### Anniversary
- `current anniversary` - Current anniversary (default context)
- `system current anniversary` - System current anniversary
- `current corporate anniversary` - Current anniversary in corporate context
- `system current corporate anniversary` - System current anniversary in corporate context
- `current user anniversary` - Current anniversary in user context
- `system current user anniversary` - System current anniversary in user context

### Context Modifiers

- **system**: Uses system-level date/time
- **corporate**: Uses corporate context date/time
- **user**: Uses user context date/time
- **default** (no modifier): Uses default context

## Duration Variables

```
DurationVars ::= ( duration begin date
                 | duration end date)
```

- **duration begin date**: Start date of a duration
- **duration end date**: End date of a duration

## Time Zone Variables

```
TimeZoneVar ::= ( user time zone
                | corporate time zone)
```

- **user time zone**: The user's configured time zone
- **corporate time zone**: The corporate time zone setting

## Update Stamp

```
UpdateStamp ::= update stamp[.actor]
```

References the UpdateStamp in the business class:
- **By itself**: Returns the actual TimeStamp when the instance was last updated
- **With .actor**: References the actor who last updated this instance

Example:
- `update stamp` → Returns timestamp of last update
- `update stamp.actor` → Returns the actor who performed the last update

## Summary of Stamp Fields

| Field | Description | With .actor |
|-------|-------------|-------------|
| `create stamp` | Timestamp when record was created | Actor who created the record |
| `update stamp` | Timestamp when record was last updated | Actor who last updated the record |
| `create date` | Most recent create date from current 'as of' date | N/A |
| `create effective date` | Most recent create effective date from current 'as of' date | N/A |
