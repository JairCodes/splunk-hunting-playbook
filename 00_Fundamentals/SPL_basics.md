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
6. [The rename command](#the-rename-command)
7. [The dedup command](#the-dedup-command)
8. [The sort command](#the-sort-command)
9. [The stats command](#the-stats-command)
10. [The chart command](#the-chart-command)
11. [The eval command](#the-eval-command)
12. [The rex command](#the-rex-command)

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

## The rename command

**Definition:**<br>
The **rename** command renames a field in the search results. <br>

### Example <br>
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
| rename Image as Process
```
This command **renames** the Image field to Process in the search results. Image field in Sysmon logs represents the name of the <br>
executable file for the process. By renaming it, all the subsequent references to Process would now refer to what was originally the <br>
Image field. <br>

## The dedup command

**Definition:**<br>
The 'dedup' command removes duplicate events. <br>

### Example <br>
```spl
index="main" sourcetype="WinLogEvent=Sysmon" EvenCode=1
| dedup Image
```
The dedup command removes duplicate entries based on the Image field from the process creation events. This means if the same <br>
process (Image) is created multiple times, it will appear only once in the results, effectively removing repetition. <br>

## The sort command

**Definition:**<br>
The **sort** command sorts the search results. <br>

### Example <br>
```spl
index="main" sourcetype="WinEventLog=Sysmon" EventCode=1
| sort - _time
```
This command sorts all process creation events in the main index in descending order of their timestamps (_time), i.e., the most recent <br>
events are shown first. <br>

## The stats command

**Definition:**<br>
The stats command performs statistical operations. <br>

### Example <br>
```spl
index="main" sourcetype="WinLogEvent=Sysmon" EvenCode=3
| stats count by _time, Image
```
**Retrieves** a table where each row represents a unique combination of a timestamp (_time) and a process (Image). The count <br>
column indicates the number of network connection events that occurred for that specific process at that specific time. <br>
<br>
**However**, it's challenging to visualize this data over time for each process because the data for each process is interspersed throughout <br>
the table. We'd need to manually filter by process (Image) to see the counts over time for each one. <br>

## The chart command

**Definition:**<br>
The chart command creates a data visualization based on statistical operations. 

### Example <br>
```spl
index="main" sourcetype="WinLogEvent=Sysmon" EvenCode=3
| chart count by _time, Image
```
**Retrieves** a table where each row represents a unique timestamp (_time) and each column represents a unique process <br>
(Image). The cell values indicate the number of network connection events that occurred for each process at each specific time. <br>
<br>
With the chart command, you can easily visualize the data over time for each process because each process has its own column. You <br>
can quickly see at a glance the count of network connection events over time for each process. <br>

## The eval command

**Definition:**<br>
The **eval** command creates or redefines fields.

### Example <br>
```spl
index="main" sourcetype="WinLogEvent=Sysmon" EvenCode=1
| eval Process_Path=lower(Image)
```
This command creates a new field Process_Path which contains the lowercase version of the Image field. It doesn't change the actual <br>
Image field, but creates a new field that can be used in subsequent operations or for display purposes. <br>

## The rex command

**Definition:**<br>
The rex command extracts new fields from existing ones using regular expressions.

### Example <br>
```spl
index="main" EventCode=4662
| rex max_match=0 "[^%](?<guid>{.*})"
| table guid
```
- index="main" EventCode=4662 filters the events to those in the main index with the EventCode equal to 4662. <br>
This narrows down the search to specific events with the specified EventCode. <br>
- rex max_match=0 "[^%](?<guid>{.*})" uses the rex command to extract values matching the pattern from the <br>
events' fields. The regex pattern {.*} looks for substrings that begin with { and end with }. The [^%] <br>
part ensures that the match does not begin with a % character. The captured value within the curly <br>
braces is assigned to the named capture group guid. <br>
- table guid displays the extracted GUIDs in the output. This command is used to format the results and <br>
display only the guid field. <br>
- The max_match=0 option ensures that all occurrences of the pattern are extracted from each event. By <br>
default, the rex command only extracts the first occurrence. <br>

## The lookup command

**Definition:**<br>
The **lookup** command enriches the data with external sources. <br>

### Example <br>
Suppose the following CSV file called malware_lookup.csv. <br>
```spl
filename, is_malware
notepad.exe, false
cmd.exe, false
powershell.exe, false
sharphound.exe, true
randomfile.exe, true
```
we can do the following, <br>
```spl
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
 | rex field=Image "(?P<filename>[^\\\]+)$"
 | eval filename=lower(filename)
 | lookup malware_lookup.csv filename OUTPUTNEW is_malware
 | table filename, is_malware
```
- index="main" sourcetype="WinEventLog:Sysmon" EventCode=1: This is the search criteria. It's looking for <br>
Sysmon logs (as identified by the sourcetype) with an EventCode of 1 (which represents process <br>
creation events) in the "main" index. <br>
- | rex field=Image "(?P<filename>[^\\\]+)$": This command is using the regular expression (regex) to <br>
extract a part of the Image field. The Image field in Sysmon EventCode=1 logs typically contains the <br>
full file path of the process. This regex is saying: Capture everything after the last backslash <br>
(which should be the filename itself) and save it as filename. <br>
- | eval filename=lower(filename): This command is taking the filename that was just extracted and <br>
converting it to lowercase. The lower() function is used to ensure the search is case-insensitive. <br>
- | lookup malware_lookup.csv filename OUTPUTNEW is_malware: This command is performing a lookup operation <br>
using the filename as a key. The lookup table (malware_lookup.csv) is expected to contain a list of <br>
filenames of known malicious executables. If a match is found in the lookup table, the new field <br>
is_malware is added to the event, which indicates whether or not the process is considered malicious <br>
based on the lookup table. <-- filename in this part of the query is the first column title in the CSV. <br>
- | table filename, is_malware: This command is formatting the output to show only the fields filename and <br>
is_malware. If is_malware is not present in a row, it means that no match was found in the lookup <br>
table for that filename. <br>
<br>
In summary, this query is extracting the filenames of newly created processes, converting them to lowercase, comparing them against <br>
a list of known malicious filenames, and presenting the findings in a table. <br>
