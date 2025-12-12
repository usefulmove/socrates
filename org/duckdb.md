# DuckDB Notes

DuckDB is an in-process analytical database optimized for OLAP workloads. Think "SQLite for analytics" - embeddable, zero-dependency, columnar storage, vectorized execution.

**Related:** [[sql-patterns]] for analytical query patterns | [[python-notes]] for Python integration | [[polars-regex]] for data manipulation

## Philosophy & Use Cases

**When to use DuckDB:**
- Local data analysis (CSV, Parquet, JSON files)
- ETL pipelines without infrastructure overhead
- Exploratory data analysis with SQL
- Analytical queries on modest datasets (GBs to low TBs)
- Prototyping before production data warehouse
- Embedded analytics in applications

**Strengths:**
- Extremely fast aggregations and window functions
- Direct querying of Parquet, CSV, JSON without loading
- Native Python integration (pandas/polars interop)
- Zero configuration, zero admin overhead
- Rich SQL dialect with modern features

**Not for:**
- High-concurrency OLTP workloads
- Multi-node distributed queries (single-node only)
- Real-time streaming (batch-oriented)

## Core Workflow Patterns

### Working with Files Directly

```sql
-- Query CSV without loading
select * from 'data.csv'
where revenue > 1000;

-- Query multiple files with glob patterns
select * from 'data/*.parquet'
where date >= '2024-01-01';

-- Query remote files
select * from 'https://example.com/data.parquet';

-- Query compressed files (auto-detection)
select * from 'data.csv.gz';

-- Read specific columns only (projection pushdown)
select user_id, revenue
from 'large_file.parquet'
where date = '2024-01-01';
```

### Creating Tables from Files

```sql
-- Create table from CSV with schema inference
create table users as
select * from read_csv('users.csv', auto_detect=true);

-- Explicit schema
create table users as
select * from read_csv('users.csv',
    columns={'id': 'integer', 'name': 'varchar', 'created_at': 'timestamp'});

-- From Parquet (always has schema)
create table events as
select * from 'events.parquet';

-- From JSON
create table logs as
select * from read_json('logs.json', auto_detect=true);

-- Multiple files into one table
create table all_data as
select * from 'data/*.parquet';
```

### Persistent vs In-Memory Databases

```sql
-- In-memory (default, fast but ephemeral)
-- Just start querying

-- Create persistent database
duckdb mydata.duckdb

-- From CLI or Python, attach database
attach 'mydata.duckdb' as db;
create table db.users as select * from 'users.csv';
```

**Copying in-memory to file** ([reference](https://duckdb.md/docs/stable/guides/snippets/copy_in-memory_database_to_file)):

```sql
attach 'file.duckdb';
-- Tables automatically copied from memory to file
detach file;
```

### Exporting Data

**To Parquet** (most efficient for columnar data):

```sql
copy (select * from table) to
'file.parquet'
(format parquet);

-- With compression (zstd recommended)
copy (select * from table) to
'file.parquet'
(format parquet, compression zstd);

-- Partition by column (creates directory structure)
copy (select * from events) to
'events_partitioned'
(format parquet, partition_by (year, month));
```

**To CSV:**

```sql
copy (select * from table) to 'output.csv'
(header true, delimiter ',');
```

**To JSON:**

```sql
copy (select * from table) to 'output.json'
(array true); -- Single JSON array

copy (select * from table) to 'output.jsonl'
(format json); -- Line-delimited JSON
```

## Data Cleaning & Transformation

### Handling NULLs

```sql
-- Coalesce to default values
select
    coalesce(email, 'unknown@example.com') as email,
    coalesce(age, 0) as age,
    coalesce(status, 'pending') as status
from users;

-- Filter out nulls
select * from users
where email is not null
    and age is not null;

-- Replace nulls in place
update users
set email = 'unknown@example.com'
where email is null;
```

### String Cleaning

```sql
-- Trim whitespace
select
    trim(name) as name,
    lower(trim(email)) as email
from users;

-- Regex replacement
select
    regexp_replace(phone, '[^0-9]', '', 'g') as clean_phone
from users;

-- Extract patterns
select
    regexp_extract(email, '@(.+)$', 1) as domain
from users;

-- Case normalization
select
    upper(state_code) as state,
    lower(email) as email,
    initcap(name) as name -- Title Case
from users;
```

### Date/Time Handling

```sql
-- Parse strings to dates
select
    strptime(date_str, '%Y-%m-%d') as date,
    strptime(timestamp_str, '%Y-%m-%d %H:%M:%S') as timestamp
from raw_data;

-- Extract components
select
    extract(year from created_at) as year,
    extract(month from created_at) as month,
    extract(dow from created_at) as day_of_week, -- 0=Sunday
    date_trunc('day', created_at) as date
from events;

-- Date arithmetic
select
    created_at + interval 7 day as due_date,
    current_date - created_at::date as days_old
from tasks;
```

### Type Casting

```sql
select
    cast(user_id as integer) as user_id,
    user_id::integer as user_id, -- Shorthand
    try_cast(age_str as integer) as age -- Returns NULL on failure
from raw_users;
```

## Window Functions

Window functions operate on sets of rows related to the current row without collapsing them (unlike GROUP BY).

### Ranking & Row Numbers

```sql
-- Rank users by revenue
select
    user_id,
    revenue,
    row_number() over (order by revenue desc) as row_num,
    rank() over (order by revenue desc) as rank,
    dense_rank() over (order by revenue desc) as dense_rank,
    percent_rank() over (order by revenue desc) as percentile
from user_revenue;

-- Partition by category
select
    category,
    product_id,
    revenue,
    row_number() over (partition by category order by revenue desc) as rank_in_category
from products;

-- Top N per group (using qualify)
select * from products
qualify row_number() over (partition by category order by revenue desc) <= 5;
```

### Running Aggregates

```sql
-- Cumulative sum
select
    date,
    revenue,
    sum(revenue) over (order by date) as cumulative_revenue
from daily_sales;

-- Moving average (7-day window)
select
    date,
    revenue,
    avg(revenue) over (
        order by date
        rows between 6 preceding and current row
    ) as moving_avg_7day
from daily_sales;

-- Running count of distinct users
select
    date,
    user_id,
    count(distinct user_id) over (order by date) as total_users_to_date
from signups;
```

### Lead/Lag (Time Series)

```sql
-- Compare to previous row
select
    date,
    revenue,
    lag(revenue) over (order by date) as prev_revenue,
    revenue - lag(revenue) over (order by date) as change,
    (revenue - lag(revenue) over (order by date)) / lag(revenue) over (order by date) as pct_change
from daily_sales;

-- Days since last event
select
    user_id,
    event_date,
    event_date - lag(event_date) over (partition by user_id order by event_date) as days_since_last
from user_events;

-- Next event
select
    user_id,
    event_type,
    event_date,
    lead(event_type) over (partition by user_id order by event_date) as next_event
from user_events;
```

### First/Last Values

```sql
-- First purchase date per user
select
    user_id,
    purchase_date,
    first_value(purchase_date) over (partition by user_id order by purchase_date) as first_purchase
from purchases;

-- Most recent status
select
    user_id,
    date,
    status,
    last_value(status) over (
        partition by user_id
        order by date
        rows between unbounded preceding and unbounded following
    ) as final_status
from user_statuses;
```

## Common Table Expressions (CTEs)

CTEs improve readability and enable recursive queries.

### Basic CTEs

```sql
-- Simple CTE
with active_users as (
    select user_id, name, email
    from users
    where last_login >= current_date - interval 30 day
)
select
    a.user_id,
    a.name,
    count(*) as order_count
from active_users a
join orders o on a.user_id = o.user_id
group by a.user_id, a.name;

-- Multiple CTEs
with
revenue_by_user as (
    select
        user_id,
        sum(amount) as total_revenue
    from orders
    group by user_id
),
user_segments as (
    select
        user_id,
        case
            when total_revenue > 1000 then 'high'
            when total_revenue > 100 then 'medium'
            else 'low'
        end as segment
    from revenue_by_user
)
select segment, count(*) as user_count
from user_segments
group by segment;
```

### Recursive CTEs

```sql
-- Generate date series
with recursive dates(date) as (
    select date '2024-01-01'
    union all
    select date + interval 1 day
    from dates
    where date < '2024-12-31'
)
select * from dates;

-- Hierarchical data (org chart)
with recursive org_tree as (
    -- Base case: top-level employees
    select employee_id, name, manager_id, 0 as level
    from employees
    where manager_id is null

    union all

    -- Recursive case: employees reporting to current level
    select e.employee_id, e.name, e.manager_id, o.level + 1
    from employees e
    join org_tree o on e.manager_id = o.employee_id
)
select * from org_tree order by level, name;
```

## JSON and Nested Data

DuckDB has strong support for semi-structured data.

### Reading JSON

```sql
-- Read JSON file
select * from read_json('data.json', auto_detect=true);

-- Line-delimited JSON (JSONL)
select * from read_json_auto('logs.jsonl');

-- From string column
select
    json_extract(data, '$.user_id') as user_id,
    json_extract(data, '$.event_type') as event_type
from raw_logs;
```

### Nested Structures

```sql
-- Create nested structure
select
    user_id,
    list(order_id) as order_ids,
    list({date: order_date, amount: amount}) as orders
from orders
group by user_id;

-- Unnest arrays
select
    user_id,
    unnest(order_ids) as order_id
from user_orders;

-- Nested struct access
select
    user_id,
    unnest(orders).date as order_date,
    unnest(orders).amount as order_amount
from user_orders;
```

### JSON Extraction

```sql
-- Extract values
select
    data->>'$.user_id' as user_id,
    data->>'$.metadata.ip' as ip_address,
    data->'$.tags' as tags_array
from events;

-- Transform JSON to columns
select
    id,
    data->>'name' as name,
    cast(data->>'age' as integer) as age,
    data->>'city' as city
from json_table;
```

## Transactions & Data Integrity

Transactions ensure atomicity - changes either all succeed or all fail.

```sql
begin transaction; -- or just 'begin;'

-- Perform modifications
update inventory set quantity = quantity - 1 where product_id = 123;
insert into orders (user_id, product_id, quantity) values (456, 123, 1);

-- Verify results
select quantity from inventory where product_id = 123;

commit; -- Make changes permanent
-- or: rollback; -- Undo all changes
```

**Savepoints** (partial rollback):

```sql
begin;
update accounts set balance = balance - 100 where id = 1;
savepoint sp1;
update accounts set balance = balance + 100 where id = 2;
-- Oops, wrong account
rollback to sp1;
update accounts set balance = balance + 100 where id = 3;
commit;
```

## Performance Optimization

### Query Planning

```sql
-- See query plan
explain select * from orders where user_id = 123;

-- Detailed analysis
explain analyze select * from orders where user_id = 123;
```

### Indexes

```sql
-- Create index (speeds up lookups, slows down writes)
create index idx_user_id on orders(user_id);

-- Composite index
create index idx_user_date on orders(user_id, order_date);

-- Drop index
drop index idx_user_id;
```

### Parallelism

```sql
-- Check thread count
select * from duckdb_settings() where name = 'threads';

-- Set threads (defaults to CPU cores)
set threads = 4;
```

### Memory Management

```sql
-- Check memory usage
select * from duckdb_settings() where name like '%memory%';

-- Limit memory
set memory_limit = '4GB';

-- Larger buffer pool for big queries
set max_memory = '8GB';
```

### Column Pruning & Filter Pushdown

```sql
-- Good: only reads needed columns from Parquet
select user_id, revenue
from 'large_file.parquet'
where date = '2024-01-01';

-- Bad: reads entire file then filters
select * from 'large_file.parquet'
where date = '2024-01-01';
```

## Python Integration

### Basic Usage

```python
import duckdb

# In-memory database
con = duckdb.connect()

# Persistent database
con = duckdb.connect('mydata.duckdb')

# Query
result = con.execute("SELECT * FROM users WHERE age > 18").fetchall()
df = con.execute("SELECT * FROM users").df()  # -> pandas DataFrame
```

### Pandas Interop

```python
import pandas as pd
import duckdb

# Query pandas directly
df = pd.read_csv('data.csv')
result = duckdb.query("SELECT * FROM df WHERE revenue > 1000").df()

# Register pandas as view
con = duckdb.connect()
con.register('my_view', df)
result = con.execute("SELECT * FROM my_view").df()

# Convert DuckDB relation to pandas
rel = con.table('users')
df = rel.filter('age > 18').df()
```

### Polars Interop

```python
import polars as pl
import duckdb

# Read with DuckDB, convert to Polars
df = duckdb.query("SELECT * FROM 'data.parquet'").pl()

# Query Polars DataFrame
pl_df = pl.read_csv('data.csv')
result = duckdb.query("SELECT * FROM pl_df WHERE revenue > 1000").pl()
```

### Chaining Operations

```python
# Method chaining style
result = (duckdb
    .read_csv('users.csv')
    .filter('age > 18')
    .aggregate('country, count(*) as cnt')
    .order('cnt DESC')
    .limit(10)
    .df())

# SQL style with Python objects
users_df = pd.read_csv('users.csv')
orders_df = pd.read_csv('orders.csv')

result = duckdb.query("""
    SELECT u.name, COUNT(*) as order_count
    FROM users_df u
    JOIN orders_df o ON u.user_id = o.user_id
    GROUP BY u.name
""").df()
```

## Parquet Best Practices

### Why Parquet?

- **Columnar storage**: Only read columns you need
- **Compression**: 5-10x smaller than CSV
- **Schema included**: No guessing types
- **Fast aggregations**: Optimized for analytical queries
- **Predicate pushdown**: Filter before reading

### Writing Parquet

```sql
-- Single file
copy (select * from large_table) to 'output.parquet'
(format parquet, compression 'zstd', row_group_size 100000);

-- Partitioned (by year/month for time-series)
copy (select * from events) to 'events_partitioned'
(format parquet, partition_by (year, month));

-- Creates: events_partitioned/year=2024/month=01/data.parquet
--          events_partitioned/year=2024/month=02/data.parquet
```

### Reading Partitioned Parquet

```sql
-- Read all partitions
select * from 'events_partitioned/**/*.parquet';

-- Filter uses partition pruning (fast!)
select * from 'events_partitioned/**/*.parquet'
where year = 2024 and month = 1;
```

### Parquet Metadata

```python
import duckdb

# Inspect Parquet schema
con = duckdb.connect()
con.execute("SELECT * FROM parquet_schema('data.parquet')").df()

# Parquet metadata
con.execute("SELECT * FROM parquet_metadata('data.parquet')").df()
```

## Common Patterns

### Deduplication

```sql
-- Keep first occurrence
select distinct on (user_id) *
from events
order by user_id, event_date;

-- Keep most recent
select distinct on (user_id) *
from events
order by user_id, event_date desc;

-- Using window function
with ranked as (
    select *,
        row_number() over (partition by user_id order by event_date desc) as rn
    from events
)
select * from ranked where rn = 1;
```

### Pivoting

```sql
-- Pivot: rows to columns
pivot (
    select month, product, revenue
    from sales
) on product using sum(revenue);
-- Result: month | product_a | product_b | product_c

-- Unpivot: columns to rows
unpivot (
    select * from monthly_sales
) on (jan, feb, mar) into name month value revenue;
```

### Sampling

```sql
-- Random sample (10%)
select * from large_table using sample 10%;

-- Fixed number of rows
select * from large_table using sample 1000 rows;

-- Reservoir sampling (deterministic with seed)
select * from large_table using sample reservoir(1000) repeatable(42);
```

## Gotchas & Tips

### Performance Tips

1. **Use Parquet for large datasets** - 10x+ faster than CSV
2. **Filter early** - WHERE clauses before JOINs
3. **Project only needed columns** - SELECT specific columns, not *
4. **Partition time-series data** - By year/month for fast filtering
5. **Use QUALIFY instead of subqueries** - For window function filtering

### Common Mistakes

```sql
-- Bad: String concatenation in loop
select string_agg(value, ',') from table; -- Good

-- Bad: Reading entire file to count
select count(*) from 'huge.parquet'; -- Still slow
-- Better: Use metadata if available

-- Bad: Joining on strings
select * from a join b on a.email = b.email; -- Slow
-- Better: Hash first, then join on hash

-- Bad: DISTINCT with many columns
select distinct * from table; -- Slow
-- Better: Identify key columns, use window functions
```

### Data Type Notes

- **VARCHAR**: Variable-length strings (efficient storage)
- **INTEGER** vs **BIGINT**: Use INTEGER when possible (faster)
- **DECIMAL**: Exact precision for money (use DECIMAL(10,2))
- **TIMESTAMP**: Includes time zone info; **DATE** for dates only
- **INTERVAL**: For date arithmetic (interval 7 day)

## CLI Tips

```bash
# Start interactive SQL shell
duckdb mydata.duckdb

# Execute SQL from command line
duckdb mydata.duckdb "SELECT * FROM users LIMIT 10"

# Execute SQL file
duckdb mydata.duckdb < script.sql

# Output to CSV
duckdb -c "SELECT * FROM users" -csv > output.csv

# Read CSV, query, output JSON
duckdb -c "SELECT * FROM 'data.csv' WHERE age > 18" -json
```

## Learning Resources

- **Official docs**: https://duckdb.org/docs/
- **SQL dialect quirks**: [[sql-patterns]] for analytical patterns
- **Python integration**: [[python-notes]] for pandas/polars workflows
- **Regex in DuckDB**: [[duckdb-regex-cheatsheet]]

## See Also

- [[sql-patterns]] - Advanced analytical query patterns
- [[python-notes]] - Python data analysis workflows
- [[polars-regex]] - Data manipulation with Polars
- [[fp]] - Functional programming concepts (composable queries)
