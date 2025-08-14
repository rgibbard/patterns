# 🔍 SQL Patterns

A modular, annotated reference for solving advanced SQL interview problems using reusable patterns. Covers 20+ scenarios including Top-N queries, sessionization, gap detection, histogram bucketing, state transitions, and recursive hierarchies. Each entry includes:

- ✅ Recognition cues to identify the pattern
- 🧩 Problem framing and use cases
- ⏱️ Complexity notes
- 🧠 SQL and PySpark implementations

Designed for fast recall, onboarding, and mastery in technical interviews and real-world engineering.

<br>

## 1. 🔢 Top-N / Deduplication / Latest

🧩 **Problem:** Retrieve the most recent or highest-ranked record(s) per group

✅ **Recognize When:** 
- You need “top 3 spenders per region”
- You need to deduplicate on a key
- You need "latest event per user"

⏱️ **Complexity:**

• Window function: O(n)

• Filtering: O(n)

🧠 **MySQL:**

```sql
SELECT *
FROM (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY score DESC) AS rn
  FROM user_scores
) ranked
WHERE rn = 1;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, row_number

window = Window.partitionBy("user_id").orderBy(col("score").desc())

df.withColumn("rn", row_number().over(window)).filter(col("rn") == 1)
```


## 2. 🧮 Running Totals / Cumulative Sum

🧩 **Problem:** Compute running total over time

✅ **Recognize When:** “daily cumulative sales”

⏱️ **Complexity:**

• Window function: O(n)

🧠 **MySQL:**

```sql
SELECT order_date,
       SUM(total) OVER (ORDER BY order_date) AS cumulative_sales
FROM orders;
```

🧠 **PostgreSQL:** (Same syntax):

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import sum

window = Window.orderBy("order_date")

df.withColumn("cumulative_sales", sum("total").over(window))
```


## 3. 🧪 Gap Detection / Missing Records

🧩 **Problem:** Identify missing dates or IDs in a sequence

✅ **Recognize When:**

- “days with no orders”
- “missing log entries”
- “gaps between events > threshold”

⏱️ **Complexity:**

• Window function: O(n)

• Filter: O(n)


🧠 **MySQL** (Calendar Join Variant):

```sql
SELECT d.date
FROM calendar d
LEFT JOIN orders o ON d.date = DATE(o.order_date)
WHERE o.order_id IS NULL;
```

🧠 **PostgreSQL** (Calendar Join Variant):

```sql
SELECT d::date
FROM generate_series('2023-01-01', '2023-01-31', interval '1 day') d
LEFT JOIN orders o ON d = DATE(o.order_date)
WHERE o.order_id IS NULL;

```
🧠 **PySpark** (Calendar Join Variant):

```python
from pyspark.sql.functions import expr

calendar = spark.range(0, 31).withColumn("date", expr("date_add('2023-01-01', id)")).drop("id")
calendar.join(orders, calendar.date == orders.order_date, "left_anti")
```


🧠 **MySQL** (LEAD-based Gap Detection):

```sql
SELECT order_id, order_date,
       LEAD(order_date) OVER (ORDER BY order_date) AS next_date,
       DATEDIFF(LEAD(order_date) OVER (ORDER BY order_date), order_date) AS gap_days
FROM orders
WHERE DATEDIFF(LEAD(order_date) OVER (ORDER BY order_date), order_date) > 1;

```

🧠 **PostgreSQL** (LEAD-based Gap Detection):

```sql
SELECT order_id, order_date,
       LEAD(order_date) OVER (ORDER BY order_date) AS next_date,
       LEAD(order_date) OVER (ORDER BY order_date) - order_date AS gap_days
FROM orders
WHERE LEAD(order_date) OVER (ORDER BY order_date) - order_date > interval '1 day';

```

🧠 **PySpark** (LEAD-based Gap Detection):

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, lead, datediff

window = Window.orderBy("order_date")

orders.withColumn("next_date", lead("order_date").over(window)) \
      .withColumn("gap_days", datediff("next_date", "order_date")) \
      .filter(col("gap_days") > 1)
```


## 4. 🧵 Sessionization

🧩 **Problem:** Break activity into sessions based on time gaps

✅ **Recognize When:** “group events into sessions”

⏱️ **Complexity:**

• Window function: O(n)

• Optional cumulative sum: O(n)

🧠 **MySQL:**

```sql
SELECT *,
       CASE 
	       WHEN TIMESTAMPDIFF(
								MINUTE,
			          LAG(event_time) OVER (
				          PARTITION BY user_id ORDER BY event_time),
                event_time
         ) > 30
		     THEN 1 ELSE 0 
		   END AS new_session_flag
FROM events;
```

🧠 **PostgreSQL:**

```sql
SELECT *,
       CASE 
	       WHEN event_time - LAG(event_time) OVER (PARTITION BY user_id ORDER BY event_time) > interval '30 minutes'
            THEN 1 ELSE 0 END AS new_session_flag
FROM events;
```

🧠 **SQL** (Start/End Pairing):

```sql
WITH starts AS (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY event_time) AS rn
  FROM events
  WHERE event_type = 'start'
),
ends AS (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY event_time) AS rn
  FROM events
  WHERE event_type = 'end'
)
SELECT
  s.user_id,
  s.event_time AS start_time,
  e.event_time AS end_time
FROM starts s
JOIN ends e
  ON s.user_id = e.user_id AND s.rn = e.rn
```

🧠 **PySpark** (Gap-based):
```python
from pyspark.sql import Window
from pyspark.sql.functions import col, lag, expr, unix_timestamp

window = Window.partitionBy("user_id").orderBy("event_time")

events.withColumn("prev_time", lag("event_time").over(window)) \
      .withColumn("new_session_flag", expr("IF(prev_time IS NULL OR (unix_timestamp(event_time) - unix_timestamp(prev_time)) > 1800, 1, 0)"))
```

🧠 **PySpark** (Start/End Pairing):

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, row_number

window = Window.partitionBy("user_id").orderBy("event_time")

starts = events.filter(col("event_type") == "start").withColumn("rn", row_number().over(window))
ends = events.filter(col("event_type") == "end").withColumn("rn", row_number().over(window))

starts.join(ends, ["user_id", "rn"]).select(
    "user_id",
    col("starts.event_time").alias("start_time"),
    col("ends.event_time").alias("end_time")
)
```


## 5. 🧵 Session Duration Calculation

🧩 **Problem:** Compute duration of each session

✅ **Recognize When:** “how long was each user session?”

⏱️ **Complexity:**

• Window function: O(n)

🧠 **MySQL:**

```sql
SELECT session_id,
       MIN(event_time) AS session_start,
       MAX(event_time) AS session_end,
       TIMESTAMPDIFF(MINUTE, MIN(event_time), MAX(event_time)) AS duration_minutes
FROM events
GROUP BY session_id;
```

🧠 **PostgreSQL:**

```sql
SELECT session_id,
       MIN(event_time) AS session_start,
       MAX(event_time) AS session_end,
       EXTRACT(EPOCH FROM MAX(event_time) - MIN(event_time)) / 60 AS duration_minutes
FROM events
GROUP BY session_id;
```

🧠 **PySpark:**

```python
from pyspark.sql.functions import min, max, unix_timestamp

events.groupBy("session_id") \
      .agg(
          min("event_time").alias("session_start"),
          max("event_time").alias("session_end")
      ) \
      .withColumn("duration_minutes", 
                  (unix_timestamp("session_end") - unix_timestamp("session_start")) / 60)
```

### 6. 🔁 Consecutive Event Grouping

🧩 **Problem:** Group consecutive rows with the same value

✅ **Recognize When:** “group consecutive errors” or “track uninterrupted status”

⏱️ **Complexity:**

• Window + difference logic: O(n)

🧠 **MySQL:**

```sql
SELECT *,
       ROW_NUMBER() OVER (ORDER BY event_time) -
       ROW_NUMBER() OVER (PARTITION BY status ORDER BY event_time) AS group_id
FROM system_logs;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, row_number

w_all = Window.orderBy("event_time")
w_status = Window.partitionBy("status").orderBy("event_time")

logs.withColumn("rn_all", row_number().over(w_all)) \
    .withColumn("rn_status", row_number().over(w_status)) \
    .withColumn("group_id", col("rn_all") - col("rn_status"))
```


## 7. 🧮 First Event After Condition

🧩 **Problem:** Find the first event after a specific trigger

✅ **Recognize When:** “first purchase after signup” or “first login after password reset”

⏱️ **Complexity:**

• Join + filter: O(n log n)

🧠 **MySQL:**

```sql
SELECT e.*
FROM events e
JOIN (
  SELECT user_id, MIN(event_time) AS reset_time
  FROM events
  WHERE event_type = 'password_reset'
  GROUP BY user_id
) r ON e.user_id = r.user_id
WHERE e.event_time > r.reset_time
  AND e.event_type = 'login'
ORDER BY e.event_time
LIMIT 1;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
from pyspark.sql.functions import col, min

reset_times = events.filter(col("event_type") == "password_reset") \
                    .groupBy("user_id") \
                    .agg(min("event_time").alias("reset_time"))

events.join(reset_times, "user_id") \
      .filter((col("event_time") > col("reset_time")) & (col("event_type") == "login")) \
      .orderBy("event_time") \
      .limit(1)
```


## 8. 🧠 Inter-Event Time Calculation

🧩 **Problem:** Measure time between consecutive events

✅ **Recognize When:** “time between clicks” or “gap between logins”

⏱️ **Complexity:**

• Window function: O(n)

🧠 **MySQL:**

```sql
SELECT *,
       TIMESTAMPDIFF(SECOND,
                     LAG(event_time) OVER (PARTITION BY user_id ORDER BY event_time),
                     event_time) AS seconds_since_last
FROM user_events;
```

🧠 **PostgreSQL:**

```sql
SELECT *,
       EXTRACT(EPOCH FROM event_time - LAG(event_time) OVER (PARTITION BY user_id ORDER BY event_time)) AS seconds_since_last
FROM user_events;
```

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, lag, unix_timestamp

window = Window.partitionBy("user_id").orderBy("event_time")

user_events.withColumn("prev_time", lag("event_time").over(window)) \
           .withColumn("seconds_since_last", 
                       unix_timestamp("event_time") - unix_timestamp("prev_time"))
```


## 9. 🧠 Event Sequence Matching

🧩 **Problem:** Detect specific ordered patterns in event streams

✅ **Recognize When:** “user viewed → added to cart → purchased”

⏱️ **Complexity:**

• Self-join or window logic: O(n²) worst-case

🧠 **MySQL (simplified):**

```sql
SELECT user_id
FROM events
WHERE event_type IN ('view', 'add_to_cart', 'purchase')
GROUP BY user_id
HAVING COUNT(DISTINCT event_type) = 3;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark (simplified):**

```python
from pyspark.sql.functions import col, countDistinct

events.filter(col("event_type").isin("view", "add_to_cart", "purchase")) \
      .groupBy("user_id") \
      .agg(countDistinct("event_type").alias("distinct_events")) \
      .filter(col("distinct_events") == 3)
```


## 10. 🧠 Time-Bounded JOIN

🧩 **Problem:** Match records based on time ranges

✅ **Recognize When:** “orders matched to active promotions”

⏱️ **Complexity:**

• Join: O(n × m)

• Indexed range: O(n log m)

🧠 **MySQL:**

```sql
SELECT o.order_id, p.promo_code
FROM orders o
JOIN promotions p
  ON o.order_date BETWEEN p.start_date AND p.end_date;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
orders.join(promotions, 
            (orders.order_date >= promotions.start_date) & 
            (orders.order_date <= promotions.end_date), 
            "inner") \
      .select("order_id", "promo_code")
```


## 11. 🧮 Conditional Aggregation

🧩 **Problem:** Aggregate values based on conditions

✅ **Recognize When:** “sales by product type” or “active vs inactive counts”

⏱️ **Complexity:**

• Scan: O(n)

🧠 **MySQL:**

```sql
SELECT
  product_id,
  SUM(CASE WHEN status = 'active' THEN 1 ELSE 0 END) AS active_sales,
  SUM(CASE WHEN status = 'inactive' THEN 1 ELSE 0 END) AS inactive_sales
FROM orders
GROUP BY product_id;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
from pyspark.sql.functions import col, sum, when

orders.groupBy("product_id").agg(
    sum(when(col("status") == "active", 1).otherwise(0)).alias("active_sales"),
    sum(when(col("status") == "inactive", 1).otherwise(0)).alias("inactive_sales")
)
```


## 12. 🏷️ Aggregation of Aggregations
🧩 **Problem:** Aggregate at a lower level, then aggregate those results at a higher level

✅ **Recognize When:** “max department average salary per company”, “average daily sales per month”

⏱️ **Complexity:**
• Two-stage aggregation: O(n)

🧠 **MySQL/PostgreSQL:**

```sql
-- Example: Max department average salary per company
WITH dept_avg AS (
  SELECT company, department, AVG(salary) AS dept_avg_salary
  FROM employees
  GROUP BY company, department
)
SELECT company, MAX(dept_avg_salary) AS max_dept_avg_salary
FROM dept_avg
GROUP BY company;
```

🧠 **PySpark:**

```python
from pyspark.sql.functions import col, avg, max

# First aggregation: average salary per department per company
dept_avg = employees.groupBy("company", "department") \
    .agg(avg("salary").alias("dept_avg_salary"))

# Second aggregation: max of department averages per company
dept_avg.groupBy("company") \
    .agg(max("dept_avg_salary").alias("max_dept_avg_salary"))
```

### 13. 🚫 Anti-Join with NOT EXISTS

🧩 **Problem:** Find unmatched records using NOT EXISTS

✅ **Recognize When:** “products never sold”

⏱️ **Complexity:**

• Subquery: O(n × m)

• Indexed: O(n log m)

🧠 **MySQL:**

```sql
SELECT p.*
FROM products p
WHERE NOT EXISTS (
  SELECT 1 FROM orders o WHERE o.product_id = p.product_id
);
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
products.join(orders, products.product_id == orders.product_id, "left_anti")
```


## 14. 🧠 Boolean Flags to Categories

🧩 **Problem:** Convert multiple boolean columns into a single label

✅ **Recognize When:** “user type based on flags”

⏱️ **Complexity:**

• Scan: O(n)

🧠 **MySQL:**

```sql
SELECT user_id,
       CASE
         WHEN is_admin = 1 THEN 'Admin'
         WHEN is_active = 1 THEN 'Active'
         ELSE 'Inactive'
       END AS user_type
FROM users;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
from pyspark.sql.functions import col, when

users.withColumn("user_type", 
    when(col("is_admin") == 1, "Admin")
    .when(col("is_active") == 1, "Active")
    .otherwise("Inactive")
)
```


## 15. 📊 Percentile Bucketing

🧩 **Problem:** Bucket values into percentiles or deciles

✅ **Recognize When:** “top 10% spenders” or “median salary”

⏱️ **Complexity:**

• Window function: O(n log n)

🧠 **MySQL:**

```sql
SELECT user_id, spend,
       NTILE(10) OVER (ORDER BY spend DESC) AS decile
FROM users;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, ntile

window = Window.orderBy(col("spend").desc())

users.withColumn("decile", ntile(10).over(window))
```


## 16. 🧬 Recursive Hierarchies

🧩 **Problem:** Traverse parent-child relationships

✅ **Recognize When:** “all subordinates of a manager” or “category tree”

⏱️ **Complexity:**

• Recursive scan: O(n × depth)

🧠 **MySQL (8+):**

```sql
WITH RECURSIVE hierarchy AS (
  SELECT employee_id, manager_id, name
  FROM employees
  WHERE manager_id IS NULL
  UNION ALL
  SELECT e.employee_id, e.manager_id, e.name
  FROM employees e
  JOIN hierarchy h ON e.manager_id = h.employee_id
)
SELECT * FROM hierarchy;
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
from pyspark.sql.functions import col

# Initial level (top managers)
hierarchy = employees.filter(col("manager_id").isNull())

# Iteratively join to build hierarchy
for _ in range(max_depth):  # define max_depth to control recursion
    next_level = employees.join(hierarchy, employees.manager_id == hierarchy.employee_id)
    hierarchy = hierarchy.union(next_level).dropDuplicates()
```


## 17. 🧭 State Transition Detection

🧩 **Problem:** Identify when a value changes from one state to another

✅ **Recognize When:** “user went from inactive to active” or “status changed from pending to shipped”

⏱️ **Complexity:**

• Window function: O(n)

• Filter: O(n)

🧠 **MySQL:**

```sql
SELECT *,
       LAG(status) OVER (PARTITION BY user_id ORDER BY event_time) AS prev_status
FROM user_events
WHERE status != LAG(status) OVER (PARTITION BY user_id ORDER BY event_time);
```

🧠 **PostgreSQL:** (Same syntax)

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, lag

window = Window.partitionBy("user_id").orderBy("event_time")

user_events.withColumn("prev_status", lag("status").over(window)) \
           .filter(col("status") != col("prev_status"))
```


## 18. 📊 Rolling Average / Moving Window

🧩 **Problem:** Compute rolling metrics over time windows

✅ **Recognize When:** “7-day moving average” or “rolling count of logins”

⏱️ **Complexity:**

• Window frame logic: O(n)

🧠 **SQL:**

```sql
SELECT *,
       AVG(metric_value) OVER (
         PARTITION BY user_id
         ORDER BY event_date
         ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
       ) AS rolling_avg_7d
FROM user_metrics;
```

> Use RANGE BETWEEN for date-based windows if needed.
> 

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, avg

window = Window.partitionBy("user_id").orderBy("event_date").rowsBetween(-6, 0)

user_metrics.withColumn("rolling_avg_7d", avg("metric_value").over(window))
```


## 19. 🛒 Event Funnel Completion

🧩 **Problem:** Identify users who completed a specific event sequence

✅ **Recognize When:** “view → add_to_cart → purchase”

⏱️ **Complexity:**

• Aggregation + filtering: O(n log n)

🧠 **SQL:**

```sql
SELECT user_id
FROM events
WHERE event_type IN ('view', 'add_to_cart', 'purchase')
GROUP BY user_id
HAVING COUNT(DISTINCT event_type) = 3;
```

> For strict ordering, use ROW_NUMBER() or recursive CTEs.
> 

🧠 **PySpark:**

```python
from pyspark.sql.functions import col, countDistinct

events.filter(col("event_type").isin("view", "add_to_cart", "purchase")) \
      .groupBy("user_id") \
      .agg(countDistinct("event_type").alias("distinct_events")) \
      .filter(col("distinct_events") == 3)
```


## 20. 🚨 Anomaly Detection

### 🧠 Z-Score
---

🧩 **Problem:** Flag outliers based on deviation from mean

✅ **Recognize When:** “detect spikes in usage” or “flag unusual behavior”

⏱️ **Complexity:**

• Window stats + filter: O(n)

🧠 **SQL:**

```sql
SELECT *,
       (metric_value - AVG(metric_value) OVER (PARTITION BY user_id)) /
       STDDEV(metric_value) OVER (PARTITION BY user_id) AS z_score
FROM user_metrics;
```

> Filter with WHERE ABS(z_score) > 3 to isolate anomalies.
> 

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, avg, stddev, abs

window = Window.partitionBy("user_id")

user_metrics.withColumn("mean", avg("metric_value").over(window)) \
            .withColumn("stddev", stddev("metric_value").over(window)) \
            .withColumn("z_score", (col("metric_value") - col("mean")) / col("stddev")) \
            .filter(abs(col("z_score")) > 3)
```


### 🧠 Percentile-Based Outlier
---

🧩 **Problem:** Flag values above a dynamic percentile threshold

✅ **Recognize When:** “flag top 5% usage” or “detect unusually high spenders”

⏱️ **Complexity:**

• Window percentile: O(n log n)

🧠 **PostgreSQL:**

```sql
SELECT *,
       PERCENT_RANK() OVER (PARTITION BY user_id ORDER BY metric_value DESC) AS percentile
FROM user_metrics
WHERE PERCENT_RANK() OVER (PARTITION BY user_id ORDER BY metric_value DESC) > 0.95;

```

🧠 **MySQL Workaround:**

MySQL doesn’t support `PERCENT_RANK()` directly, but you can simulate it:

```sql
SELECT *,
       (ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY metric_value DESC) - 1) /
       (COUNT(*) OVER (PARTITION BY user_id) - 1) AS percentile
FROM user_metrics
WHERE ((ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY metric_value DESC) - 1) /
       (COUNT(*) OVER (PARTITION BY user_id) - 1)) > 0.95;

```

> This flags the top 5% of values per user.
> 

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, row_number, count

window = Window.partitionBy("user_id").orderBy(col("metric_value").desc())
window_no_order = Window.partitionBy("user_id")

user_metrics.withColumn("row_num", row_number().over(window)) \
            .withColumn("total", count("*").over(window_no_order)) \
            .withColumn("percentile", (col("row_num") - 1) / (col("total") - 1)) \
            .filter(col("percentile") > 0.95)
```


### 🧠 Rolling Window Spike
---

🧩 **Problem:** Detect sudden spikes in a time series relative to recent history

✅ **Recognize When:** “spike in traffic” or “burst in error rate”

⏱️ **Complexity:**

• Window frame logic: O(n)

🧠 **SQL:**

```sql
SELECT *,
       metric_value,
       AVG(metric_value) OVER (
         PARTITION BY user_id
         ORDER BY event_time
         ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
       ) AS rolling_avg,
       CASE
         WHEN metric_value > 2 * AVG(metric_value) OVER (
           PARTITION BY user_id
           ORDER BY event_time
           ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
         ) THEN 1
         ELSE 0
       END AS is_spike
FROM user_metrics;

```

> This flags values that are 2× the rolling average over the past 7 events.
> 

🧠 **PySpark:**

```python
from pyspark.sql import Window
from pyspark.sql.functions import col, avg, when

window = Window.partitionBy("user_id").orderBy("event_time").rowsBetween(-6, 0)

user_metrics.withColumn("rolling_avg", avg("metric_value").over(window)) \
            .withColumn("is_spike", when(col("metric_value") > 2 * col("rolling_avg"), 1).otherwise(0))
```


## 21. 🧱 Merging Overlapping Intervals

🧩 **Problem:** Consolidate overlapping or adjacent time ranges

✅ **Recognize When:** “merge overlapping bookings” or “deduplicate time blocks”

⏱️ **Complexity:**

• Recursive or procedural logic: O(n log n)

🧠 **PostgreSQL (recursive CTE):**

```sql
WITH ordered AS (
  SELECT *, ROW_NUMBER() OVER (ORDER BY start_time) AS rn
  FROM intervals
),
merged AS (
  SELECT rn, start_time, end_time
  FROM ordered
  WHERE rn = 1

  UNION ALL

  SELECT o.rn,
         LEAST(m.start_time, o.start_time),
         GREATEST(m.end_time, o.end_time)
  FROM merged m
  JOIN ordered o ON o.rn = m.rn + 1
  WHERE o.start_time <= m.end_time
)
SELECT * FROM merged;

```

> This pattern is essential for calendar deduplication, log compression, and range normalization.
> 

🧠 **PySpark:**

```python
# No native recursive CTE support — use sort, collect_list, and reduce logic in Python or UDFs for merging.
```


## 22. 🧠 Group-Wise Max with Join

**Use Case:** Get full row for max value per group

**Pattern:** Join with aggregated subquery

```sql
SELECT e.*
FROM employees e
JOIN (
  SELECT department_id, MAX(salary) AS max_salary
  FROM employees
  GROUP BY department_id
) m ON e.department_id = m.department_id AND e.salary = m.max_salary;

```

🧠 **PySpark:**

```python
from pyspark.sql.functions import max

max_salaries = employees.groupBy("department_id") \
                        .agg(max("salary").alias("max_salary"))

employees.join(max_salaries, 
               (employees.department_id == max_salaries.department_id) & 
               (employees.salary == max_salaries.max_salary))
```


## 23. 📊 Histogram / Bucketing

⚒️ **Use Case:** Group values into ranges, analyze frequency distributions, bin numeric or time data

⏰ **Time Complexity:** Depends on indexing and grouping; typically O(n)

✅ **Recognize When:**

- Need to group by numeric or time ranges
- Problem involves “bucket”, “bin”, “histogram”, or “distribution”
- Asked to summarize or visualize spread of values

🔍 **Common Prompts:**

- "Group transactions by amount range"
- "Bucket timestamps into hourly intervals"
- "Count users by age group"


### 🧠 Numeric Bucketing (e.g. transaction amounts)
---

```sql
SELECT
  FLOOR(amount / 100) * 100 AS bucket_floor,
  COUNT(*) AS count
FROM transactions
GROUP BY FLOOR(amount / 100) * 100
ORDER BY bucket_floor;
```

- Buckets: 0–99, 100–199, 200–299, etc.
- Use `FLOOR()` or `CASE` for custom ranges


### 🧠 Time Bucketing (e.g. hourly sessions)
---

```sql
SELECT
  DATE_FORMAT(start_time, '%Y-%m-%d %H:00:00') AS hour_bucket,
  COUNT(*) AS session_count
FROM sessions
GROUP BY DATE_FORMAT(start_time, '%Y-%m-%d %H:00:00')
ORDER BY hour_bucket;

```

- Buckets timestamps into hourly intervals
- Use `DATE_TRUNC()` in PostgreSQL or `DATE_FORMAT()` in MySQL


### 🧠 Date Difference Bucketing
---

**Use Case:** Bucket users by time since last activity

**Pattern:** `DATEDIFF()` or `TIMESTAMPDIFF()`

```sql
SELECT user_id,
       CASE
         WHEN DATEDIFF(CURRENT_DATE, last_login) <= 7 THEN 'Active'
         WHEN DATEDIFF(CURRENT_DATE, last_login) <= 30 THEN 'Dormant'
         ELSE 'Inactive'
       END AS activity_bucket
FROM users;
```


### 🧠 Custom Buckets (e.g. age groups)
---

```sql
SELECT
  CASE
    WHEN age < 18 THEN 'Under 18'
    WHEN age BETWEEN 18 AND 24 THEN '18–24'
    WHEN age BETWEEN 25 AND 34 THEN '25–34'
    WHEN age BETWEEN 35 AND 49 THEN '35–49'
    ELSE '50+'
  END AS age_group,
  COUNT(*) AS user_count
FROM users
GROUP BY age_group
ORDER BY MIN(age);

```

- Use `CASE` for non-uniform or labeled buckets
- Can be extended to income, scores, durations, etc.
