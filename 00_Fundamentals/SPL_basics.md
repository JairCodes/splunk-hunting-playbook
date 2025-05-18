# SPL Basics

> A reference for core splunk search concepts & syntax. <br>
> Adding new entries as we discover them 🔎

---

## 📝 Table of Contents
1. [Search Syntax](#search-syntax)
2. [Wildcards](#wildcards)
3. [Fields and Comparison Operators](#fields-and-comparison-operators)
4. [The fields command](#the-fields-command)
5. [The table command](#the-table-command)

---

## Search Syntax

**Definition:**<br>
The most fundamental aspect of SPL is searching. By default, a search returns all events, but it can be narrowed down with keywords <br>
boolean operators, comparison operators, and wildcard characters. For instance, a search for error would return all containing <br>
that word. <br>
The search command is typically implicit at the start of each SPL query and is not usually written out. However, here's an example using <br>
both explicit and implicit search syntax: <br>

```spl
# Implicit
index="main" ERROR

# Explicit
search index="main" ERROR
```

## Wildcards

**Definition:**<br>
Wildcards (*) can replace any number of characters in searches and field values <br>

### Syntax <br>
```spl
field="prefix*"     # matches any value that start with "prefix"
field="*suffix"     # matches any value that ends with "suffix"
field="*middle*"    # matches any value containing "middle"
```
### Example <br>
```spl
search index=main "*UNKOWN*"
```
**Retrieves** all events in the main index whose raw text contains the substring "UNKNOWN" anywhere in the event. <br>

## Fields and Comparison Operators

**Definition:**<br>
Splunk automatically identifies certain data as fields (like source, sourcetype, host, EventCode, etc.), and users can manually define additional fields. <br>
These fields can be used with comparison operators (=, !=, <, >, <=, >=) for more precise searches. <br>

### Example <br>
```spl
index="main" EventCode!=1
```
**Retrieves** the 'main' index for events that do not have an EventCode value of 1. <br>

## The fields command

**Definition:**<br>
The fields command specifies which fields should be included or excluded in the search results. <br>

### Example <br>
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
| fields - User
```
**Retrieving** all process creation events from the 'main' index, the fields command **excludes** the 'User' field from the search results. <br>
Thus, the results will contain all fields normally found in the Sysmon Event ID 1 logs, except for the user that initiated the process. <br>

## The table command

**Definition:**<br>
The **table** command presents search results in a tabular format. <br>

### Example <br>
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
| table _time, host, Image
```
This query **returns** process creation events, then arranges selected fields (_time, host, and Image) in a tabular format. _time is the <br>
timestamp of the event, host is the name of the host where the event occurred, and Image is the name of the executable file that <br>
represents the process. <br>
