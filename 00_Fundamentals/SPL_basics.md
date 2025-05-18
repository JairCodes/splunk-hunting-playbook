# SPL Basics

> A reference for core splunk search concepts & syntax.
> Adding new entries as we discover them 🔎

---

## 📝 Table of Contents
1. [Search Syntax](#search-syntax)

---

## Search Syntax

**Definition**
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
