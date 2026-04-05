# ⚡ Building Dynamic SQL Pipelines with Parameters

## Overview

Running the same SQL transformation repeatedly with small manual changes (dates, filters, regions) is inefficient and risky. Modern data workflows solve this by using **parameterized SQL**, turning fixed queries into flexible templates that adapt automatically during execution.

This approach is the backbone of scalable data pipelines—enabling automation, reuse, and adaptability across environments and business needs.

---

## 🔑 Key Concepts

### 1. Static vs Parameterized SQL

#### Static SQL (Problem)

* Hardcoded values (dates, filters)
* Requires manual edits before each run
* Not reusable

```sql
SELECT *
FROM sales
WHERE sale_date = '2024-01-01';
```

---

#### Parameterized SQL (Solution)

* Uses variables instead of fixed values
* Values injected at runtime

```sql
SELECT *
FROM sales
WHERE sale_date = {{ start_date }};
```

✅ Same query works for any date without modification

---

### 2. Types of Parameters

#### 📅 Date Parameters

Used for time-based filtering and incremental logic

```sql
WHERE sale_date >= {{ start_date }}
AND sale_date < {{ end_date }}
```

* Supports relative calculations (e.g., last 7 days)
* Enables automated scheduling

---

#### 🌍 Environment Parameters

Adapt queries across environments:

* Development
* Staging
* Production

```sql
FROM {{ env }}_sales_data
```

✅ Same SQL runs everywhere with different inputs

---

#### 📊 Business Logic Parameters

Control filtering and grouping dynamically:

```sql
WHERE region = {{ region }}
AND product_category = {{ category }}
```

✅ One query → multiple business use cases

---

## 🧱 Template Design Patterns

### 1. Incremental Loading Template

Process only new or updated data:

```sql
SELECT *
FROM orders
WHERE updated_at >= {{ last_run_date }};
```

#### Why it matters:

* Avoids full table scans
* Improves performance
* Supports near real-time pipelines

---

### 2. Configuration-Driven Templates

Separate logic from configuration:

```sql
WHERE revenue > {{ sales_threshold }}
AND region IN ({{ active_regions }})
```

👉 Parameters stored outside SQL (YAML/JSON)

---

### 3. Conditional Logic Templates

Dynamically change query behavior:

```sql
SELECT *
FROM sales

{% if include_historical %}
LEFT JOIN historical_data USING (id)
{% endif %}
```

✅ One template → multiple execution modes

---

### 4. Loop-Based Processing

Iterate over dynamic lists:

```sql
{% for region in region_list %}
SELECT '{{ region }}' AS region, SUM(sales)
FROM sales_data
WHERE region = '{{ region }}'
{% endfor %}
```

✅ Eliminates repetitive queries

---

### 5. Schema & Table Parameterization

```sql
SELECT *
FROM {{ source_schema }}.{{ table_prefix }}_sales
```

✅ Enables environment-agnostic pipelines

---

### 6. Macro Integration

Reusable logic wrapped in functions:

```sql
{{ generate_date_series(start_date, end_date) }}
```

✅ Clean, reusable, and scalable SQL design

---

## ⚙️ Step-by-Step Transformation

### Step 1: Identify Hardcoded Queries

Example:

* Fixed dates
* Static filters
* Repeated scripts

---

### Step 2: Replace with Parameters

```sql
WHERE sale_date = {{ execution_date }}
```

---

### Step 3: Add Incremental Logic

```sql
WHERE updated_at > {{ last_run_date }}
```

---

### Step 4: Externalize Configuration

Move parameters to config:

```yaml
start_date: '2026-01-01'
region: 'EU'
```

---

### Step 5: Generalize for Reuse

* Support multiple regions
* Adapt to different time ranges
* Handle multiple environments

---

## 💻 Practical SQL Examples

### Example 1: Dynamic Reporting Query

```sql
SELECT 
    region,
    SUM(revenue) AS total_revenue
FROM sales
WHERE sale_date BETWEEN {{ start_date }} AND {{ end_date }}
GROUP BY region;
```

---

### Example 2: Incremental Pipeline Query

```sql
SELECT *
FROM transactions
WHERE updated_at > (
    SELECT MAX(updated_at) FROM processed_transactions
);
```

---

### Example 3: Conditional Logic

```sql
SELECT *
FROM orders

{% if include_returns %}
LEFT JOIN returns USING (order_id)
{% endif %}
```

---

## 🔧 Practical Implementation

### 1. Define Parameters (Config File)

```yaml
# config.yml
start_date: '2026-01-01'
end_date: '2026-01-31'
region_list: ['EU', 'US']
include_returns: true
```

---

### 2. Build Parameterized SQL Model

```sql
SELECT *
FROM orders
WHERE order_date BETWEEN {{ var('start_date') }} AND {{ var('end_date') }}
AND region IN ({{ var('region_list') }})
```

---

### 3. Add Incremental Processing

```sql
{{ config(materialized='incremental') }}

SELECT *
FROM orders

{% if is_incremental() %}
WHERE updated_at > (SELECT MAX(updated_at) FROM {{ this }})
{% endif %}
```

---

### 4. Automate Execution

* Schedule daily runs
* Inject parameters dynamically
* Track pipeline execution logs

---

### 5. Enable Multi-Environment Deployment

```sql
FROM {{ var('env') }}_orders
```

---

## 🌍 Real-World Use Case

### Automated Sales Reporting Pipeline

#### Before:

* Analysts manually updated:

  * Dates
  * Filters
* Re-ran queries repeatedly
* Frequent errors

---

#### After:

* Parameterized pipeline:

  * Auto-adjusts date ranges
  * Supports multiple regions
  * Runs on schedule

#### Outcome:

* Reports generated automatically
* Same pipeline supports:

  * Daily dashboards
  * Monthly summaries
  * Quarterly insights

---

## ✅ Best Practices

* Avoid hardcoding values in SQL
* Use **parameter-driven templates**
* Store configs externally (YAML/JSON)
* Implement **incremental loading**
* Use macros for reusable logic
* Design for **multi-environment deployment**
* Build flexible pipelines, not one-off queries

---

## 🧠 Summary

Parameterized SQL transforms data workflows from manual processes into **automated, scalable systems**:

* 🔄 Queries adapt dynamically at runtime
* ⚡ Pipelines run automatically without manual edits
* 📈 Systems scale with growing data complexity

By combining **date parameters**, **environment configs**, and **business logic variables**, you create reusable pipeline components that power modern analytics and real-time decision-making.

---

## 📁 File Name

`parameterized-sql-dynamic-pipeline-templates.md`
