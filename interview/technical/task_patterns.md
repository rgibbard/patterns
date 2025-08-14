# 🧰 Task Patterns

A curated set of lightweight, interview-ready Python task patterns for data parsing, cleaning, aggregation, and transformation. Each snippet is modular, annotated, and designed to illustrate common analysis, preprocessing and ETL operations.


## 1. 🧹 Data Cleaning & Normalization

🔍 **Use Case:** Standardize inconsistent formats

```python
def normalize_phone(phone):
    digits = re.sub(r"\D", "", phone)
    if len(digits) == 10:
        return f"({digits[:3]}) {digits[3:6]}-{digits[6:]}"
    return None

print(normalize_phone("+1 (404) 555-1234"))  # (404) 555-1234
```


## 2. 🧾 CSV Parsing and Filtering

🔍 **Use Case:** Read structured data and filter rows

```python
import csv

with open("logs.csv") as f:
    reader = csv.DictReader(f)
    for row in reader:
        if row["status"] == "ERROR":
            print(row["timestamp"], row["message"])
```


## 3. 🧮 Aggregation by Key

🔍 **Use Case:** Count or group values

```python
from collections import defaultdict

logs = ["US", "US", "CA", "CA", "CA", "MX"]
counts = defaultdict(int)

for region in logs:
    counts[region] += 1

print(dict(counts))  # {'US': 2, 'CA': 3, 'MX': 1}
```


## 4. 🧪 Lightweight ETL Simulation

🔍 **Use Case:** Transform JSON records and write to CSV

```python
import json, csv

data = [
    {"id": 1, "name": "Alice"},
    {"id": 2, "name": "Bob"}
]

with open("output.csv", "w", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["id", "name"])
    writer.writeheader()
    writer.writerows(data)
```


## 5. 🧭 Timestamp Filtering

🔍 **Use Case:** Filter events by recency

```python
from datetime import datetime, timedelta

now = datetime.now()
cutoff = now - timedelta(hours=24)

events = [
    {"ts": "2025-08-12 15:00:00"},
    {"ts": "2025-08-13 14:00:00"}
]

for e in events:
    ts = datetime.strptime(e["ts"], "%Y-%m-%d %H:%M:%S")
    if ts > cutoff:
        print("Recent:", e)
```


## 6. 🧠 Deduplication by Key

🔍 **Use Case:** Remove duplicates while preserving order

```python
seen = set()
deduped = []

records = [{"id": 1}, {"id": 2}, {"id": 1}, {"id": 3}]
for r in records:
    if r["id"] not in seen:
        seen.add(r["id"])
        deduped.append(r)

print(deduped)  # [{'id': 1}, {'id': 2}, {'id': 3}]
```


## 7. 🧮 Top-N by Frequency

🔍 **Use Case:** Find most common elements

```python
from collections import Counter

words = ["apple", "banana", "apple", "orange", "banana", "apple"]
top_n = Counter(words).most_common(2)

print(top_n)  # [('apple', 3), ('banana', 2)]
```


## 8. 🧪 JSON Flattening

🔍 **Use Case:** Convert nested JSON into flat records

```python
def flatten_json(d, parent_key='', sep='_'):
    items = {}
    for k, v in d.items():
        new_key = f"{parent_key}{sep}{k}" if parent_key else k
        if isinstance(v, dict):
            items.update(flatten_json(v, new_key, sep=sep))
        else:
            items[new_key] = v
    return items

nested = {"user": {"id": 1, "name": "Alice"}, "active": True}
flat = flatten_json(nested)
print(flat)  # {'user_id': 1, 'user_name': 'Alice', 'active': True}
```

## 9. 📜 Log Parsing with Regex

🔍 **Use Case:** Extract structured fields from raw logs

```python
import re

log = "2025-08-13 12:34:56 ERROR [client 192.168.1.1] Connection failed"
pattern = r"(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) ERROR \[client (\d+\.\d+\.\d+\.\d+)\]"

match = re.search(pattern, log)
if match:
    timestamp, ip = match.groups()
    print(f"Timestamp: {timestamp}, IP: {ip}")
```