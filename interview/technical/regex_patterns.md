# 🧩 Regex Cheatsheet

A concise reference of regular expression operators with patterns for parsing, validation, and extraction tasks. Includes Python and PySpark usage to support ETL, log parsing, and data cleaning workflows.

## Operators

### 🔣 Basic Character Classes

| Operator | Meaning | Example | Matches |
|---------|---------|---------|---------|
| `.`     | Any character except newline | `a.b` | `'acb'`, `'a1b'` |
| `\d`    | Digit (0–9) | `\d+` | `'123'`, `'42'` |
| `\w`    | Word character (a–z, A–Z, 0–9, _) | `\w+` | `'hello'`, `'user_1'` |
| `\s`    | Whitespace | `\s+` | `' '`, `'\t'` |
| `\D`    | Non-digit | `\D+` | `'abc'`, `'--'` |
| `\W`    | Non-word character | `\W+` | `'@#$'`, `' '` |
| `\S`    | Non-whitespace | `\S+` | `'abc'`, `'123'` |

---

### 🔁 Quantifiers

| Operator | Meaning | Example | Matches |
|----------|---------|---------|---------|
| `*`      | 0 or more | `ab*c` | `'ac'`, `'abc'`, `'abbc'` |
| `+`      | 1 or more | `ab+c` | `'abc'`, `'abbc'` |
| `?`      | 0 or 1 | `ab?c` | `'ac'`, `'abc'` |
| `{n}`    | Exactly n | `\d{4}` | `'2024'` |
| `{n,}`   | n or more | `\d{2,}` | `'42'`, `'123'` |
| `{n,m}`  | Between n and m | `\d{2,4}` | `'42'`, `'2024'` |

---

### 🧭 Anchors

| Operator | Meaning | Example | Matches |
|----------|---------|---------|---------|
| `^`      | Start of string | `^Hello` | `'Hello world'` |
| `$`      | End of string | `world$` | `'Hello world'` |
| `\b`     | Word boundary | `\bcat\b` | `'cat'`, not `'catalog'` |
| `\B`     | Non-boundary | `\Bcat\B` | `'educate'` |

---

### 🧪 Grouping & Alternation

| Operator | Meaning | Example | Matches |
|----------|---------|---------|---------|
| `()`     | Group | `(ab)+` | `'ab'`, `'abab'` |
| `|`      | OR | `cat|dog` | `'cat'`, `'dog'` |
| `(?:...)`| Non-capturing group | `(?:ab)+` | `'ab'`, `'abab'` |

---

### 🧬 Character Sets & Ranges

| Operator | Meaning | Example | Matches |
|----------|---------|---------|---------|
| `[abc]`  | a, b, or c | `[aeiou]` | `'a'`, `'e'` |
| `[^abc]` | Not a, b, or c | `[^0-9]` | `'a'`, `'%'` |
| `[a-z]`  | Range | `[A-Z]` | `'B'`, `'Z'` |

---

### 🧼 Escaping Special Characters

| Operator | Meaning | Example | Matches |
|----------|---------|---------|---------|
| `\.`     | Literal dot | `a\.b` | `'a.b'` |
| `\\`     | Literal backslash | `\\d` | `'\\d'` |

---

### 🧪 Python Usage Examples

```python
import re

re.findall(r"\d{4}-\d{2}-\d{2}", "Date: 2024-08-14")  # ['2024-08-14']
re.match(r"^user_\w+", "user_123")  # Match object
re.sub(r"\s+", "_", "hello world")  # 'hello_world'
```

---

## Patterns

### 🔢 Pattern: Extract Digits

**Regex:** `\d+`  
**Use Case:** Extract numeric values from strings

**Python**

```python
import re
re.findall(r"\d+", "Order123 shipped on 2024-01-01")  # ['123', '2024', '01', '01']
```

**PySpark**

```python
df.withColumn("digits", F.regexp_extract("text", r"\d+", 0))
```

---

### 🔤 Pattern: Extract Words

**Regex:** `\b\w+\b`  
**Use Case:** Tokenize sentence into words

**Python**

```python
re.findall(r"\b\w+\b", "Hello world, this is regex!")  # ['Hello', 'world', 'this', 'is', 'regex']
```

**PySpark**

```python
df.withColumn("word", F.regexp_extract("text", r"\b\w+\b", 0))
```

---

### 📧 Pattern: Validate Email

**Regex:** `^[\w\.-]+@[\w\.-]+\.\w+$`  
**Use Case:** Email format validation

**Python**

```python
re.match(r"^[\w\.-]+@[\w\.-]+\.\w+$", "user@example.com")  # Match object
```

**PySpark**

```python
df.withColumn("is_valid", F.col("email").rlike(r"^[\w\.-]+@[\w\.-]+\.\w+$"))
```

---

### 📅 Pattern: Extract Date (YYYY-MM-DD)

**Regex:** `\d{4}-\d{2}-\d{2}`  
**Use Case:** Extract ISO date strings

**Python**

```python
re.findall(r"\d{4}-\d{2}-\d{2}", "Logged on 2023-12-31")  # ['2023-12-31']
```

**PySpark**

```python
df.withColumn("date", F.regexp_extract("text", r"\d{4}-\d{2}-\d{2}", 0))
```

---

### 🔗 Pattern: Extract URL

**Regex:** `https?://[^\s]+`  
**Use Case:** Extract links from text

**Python**

```python
re.findall(r"https?://[^\s]+", "Visit https://example.com now!")  # ['https://example.com']
```

**PySpark**

```python
df.withColumn("url", F.regexp_extract("text", r"https?://[^\s]+", 0))
```

---

### 🧼 Pattern: Remove Special Characters

**Regex:** `[^a-zA-Z0-9 ]`  
**Use Case:** Clean strings for normalization

**Python**

```python
re.sub(r"[^a-zA-Z0-9 ]", "", "Hello@# World!!")  # 'Hello World'
```

**PySpark**

```python
df.withColumn("cleaned", F.regexp_replace("text", r"[^a-zA-Z0-9 ]", ""))
```

---

### 🧪 Pattern: Match Specific Prefix

**Regex:** `^prefix_\w+`  
**Use Case:** Filter strings with known prefix

**Python**

```python
re.match(r"^prefix_\w+", "prefix_user123")  # Match object
```

**PySpark**

```python
df.filter(F.col("id").rlike(r"^prefix_\w+"))
```

---

### 🧬 Pattern: Capture Named Groups


**Regex:** `(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})`  
**Use Case:** Structured date parsing

**Python**

```python
match = re.match(r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})", "2024-08-14")
match.groupdict()  # {'year': '2024', 'month': '08', 'day': '14'}
```

**PySpark**

_Named groups not supported directly; use multiple `regexp_extract` calls._



