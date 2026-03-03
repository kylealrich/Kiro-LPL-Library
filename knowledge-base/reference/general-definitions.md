# General Definitions

## Syntax Rules

```
// Scope is defined by indent level. An indent level is 4 spaces or 1 tab
// Comments are denoted by a double forward slash
// @SuppressWarnings in a comment will suppress all warnings that the LPL line generates
// @External with a comment will suppress dead code detection/reporting
```

## Base Definitions

### LPL Construct Name

```
LPLConstructName ::= <uppercase character>[<alphanumeric characters>…]
// up to 255 characters
```

### Action Definitions

```
ActionName ::= <LPLConstructName>
ActionTag ::= <LPLConstructName>
```

### Business Definitions

```
BusinessClass ::= <LPLConstructName>
BusinessTask ::= <LPLConstructName>
```

### View Definitions

```
CardViewName ::= <LPLConstructName>
ChartName ::= <LPLConstructName>
PeriodViewName ::= <LPLConstructName>
```

### Condition and Criteria

```
ConditionName ::= <LPLConstructName>
CriteriaName ::= <LPLConstructName>
```

### Relation Definitions

```
ColumnarRelationName ::= <LPLConstructName>
CubeLinkName ::= <LPLConstructName>
CubeRelationName ::= <LPLConstructName>
RelationName ::= <LPLConstructName>
```

### Section and Detail Definitions

```
DetailSectionName ::= <LPLConstructName>
SectionName ::= <LPLConstructName>
```

### Drill Definitions

```
DrillName ::= <LPLConstructName>
DrillListName ::= <LPLConstructName>
```

### Field and Group Definitions

```
FieldGroupName ::= <LPLConstructName>
FieldName ::= <LPLConstructName>
```

### Form Definitions

```
FormInvokeName ::= <LPLConstructName>
FormName ::= <LPLConstructName>
MatrixForm ::= <LPLConstructName>
```

### State Definitions

```
FullStateName ::= <StateName>[.<StateName>…]
StateName ::= (<LPLConstructName> | <#>)
StateCycleName ::= <LPLConstructName>
StateFieldName ::= <LPLConstructName>
```

### Hierarchy and Navigation

```
GroupName ::= <LPLConstructName>
HierarchyName ::= <LPLConstructName>
NavigationName ::= <LPLConstructName>
```

### UI Component Definitions

```
ImageMapName ::= <LPLConstructName>
ListName ::= <LPLConstructName>
MenuItemName ::= <LPLConstructName>
MenuName ::= <LPLConstructName>
PageName ::= <LPLConstructName>
PanelName ::= <LPLConstructName>
PaneName ::= <LPLConstructName>
WidgetListName ::= <LPLConstructName>
```

### System and Process Definitions

```
MajorSystemProcess ::= <LPLConstructName>
ProgramName ::= <LPLConstructName>
ParameterName ::= <LPLConstructName>
```

### Interface Definitions

```
M3Interface ::= <LPLConstructName>
WebserviceInterface ::= <LPLConstructName>
```

### Service Definitions

```
PFlowServiceName ::= <LPLConstructName>
```

### Report and Rule Definitions

```
ReportName ::= <LPLConstructName>
RuleBlockName ::= <LPLConstructName>
RuleBlocks ::= <LPLConstructName>
```

### Security Definitions

```
SecurityClaim ::= <LPLConstructName>
SecurityClassName ::= <LPLConstructName>
```

### Set and Subject Definitions

```
SetName ::= <LPLConstructName>
Subject ::= <LPLConstructName>
```

### Static Java Definitions

```
StaticJavaWS ::= <LPLConstructName>
StaticJavaPD ::= <LPLConstructName>
```

### Table and Context Definitions

```
TableName ::= <LPLConstructName>
ContextMessage ::= <LPLConstructName>
TextVariable ::= <LPLConstructName>
```

### Web Application

```
WebAppName ::= <LPLConstructName>
```

### Miscellaneous

```
Parens ::= ( '('| ')')
```

## Module Definitions

```
ModuleName ::= <alphanumeric characters>
ModuleDescription ::= <Literal>
```

## Classic Naming Conventions

```
ClassicPrefix ::= prefix is <string of 2 to 5 alphanumeric characters>

ClassicName ::= classic name is <Literal>
// literal must be all uppercase, no spaces

ClassicNameForField ::= classic name for <FullFieldName> is <Literal>
```

## RPG Naming

```
RpgName ::= rpg name is <Literal>
```

## SQL Naming Conventions

```
SqlPrefix ::= sql prefix is <string of 2 to 5 alphanumeric characters>
// no spaces

SqlName ::= sql name is <Literal>
// no leading or trailing spaces allowed

SqlNameForField ::= sql name for <FullFieldName> is <Literal>
```
