# SQL Patterns for Analytics

Practical query patterns for analytical workloads - time series, cohort analysis, funnels, aggregations, and optimization techniques.

**Related:** [[duckdb]] for DuckDB-specific workflows | [[python-notes]] for Python data analysis | [[fp]] for functional composition patterns

## Philosophy

**SQL as a functional language:**
- Each query transforms input relations to output relations
- CTEs enable composition and modularity
- Window functions separate concerns (ordering, aggregation)
- Declarative style: describe *what* you want, not *how* to get it

**Query design principles:**
1. **Filter early, aggregate late** - Reduce data volume ASAP
2. **Compose with CTEs** - Break complex queries into steps
3. **Window functions over self-joins** - More efficient and readable
4. **Indexes on JOIN/WHERE columns** - Speed up lookups
5. **Avoid SELECT *** - Only fetch needed columns

## Time Series Analysis

### Date Spines (Generating Complete Date Ranges)

Often you need a continuous date range even when data is sparse.

```sql
-- Generate date series
with recursive dates as (
    select date '2024-01-01' as date
    union all
    select date + interval 1 day
    from dates
    where date < '2024-12-31'
)
select * from dates;

-- DuckDB shorthand
select * from range(date '2024-01-01', date '2024-12-31', interval 1 day) as t(date);

-- Fill gaps with zeros
with date_spine as (
    select * from range(date '2024-01-01', date '2024-12-31', interval 1 day) as t(date)
)
select
    d.date,
    coalesce(sum(s.revenue), 0) as revenue
from date_spine d
left join sales s on d.date = s.sale_date
group by d.date
order by d.date;
```

### Moving Averages & Rolling Windows

```sql
-- 7-day moving average
select
    date,
    revenue,
    avg(revenue) over (
        order by date
        rows between 6 preceding and current row
    ) as ma_7day,
    -- Exponential moving average (approximation)
    avg(revenue) over (
        order by date
        rows between 13 preceding and current row
    ) as ma_14day
from daily_sales
order by date;

-- Month-over-month growth
select
    date,
    revenue,
    lag(revenue, 1) over (order by date) as prev_month,
    revenue - lag(revenue, 1) over (order by date) as mom_change,
    100.0 * (revenue - lag(revenue, 1) over (order by date)) 
        / lag(revenue, 1) over (order by date) as mom_pct_change
from monthly_sales;

-- Year-over-year comparison
select
    date,
    revenue,
    lag(revenue, 12) over (order by date) as prev_year,
    revenue - lag(revenue, 12) over (order by date) as yoy_change
from monthly_sales;
```

### Cumulative Metrics

```sql
-- Running total
select
    date,
    revenue,
    sum(revenue) over (order by date) as cumulative_revenue,
    -- Running average
    avg(revenue) over (order by date rows between unbounded preceding and current row) as avg_to_date
from daily_sales;

-- Cumulative distinct users
with daily_signups as (
    select
        date(created_at) as signup_date,
        user_id
    from users
)
select
    signup_date,
    count(*) as new_users,
    count(distinct user_id) over (
        order by signup_date
        rows between unbounded preceding and current row
    ) as total_users
from daily_signups
group by signup_date
order by signup_date;
```

### Seasonality & Trends

```sql
-- Day of week patterns
select
    dayname(order_date) as day_name,
    extract(dow from order_date) as day_num, -- 0=Sunday
    count(*) as order_count,
    avg(amount) as avg_amount
from orders
group by day_name, day_num
order by day_num;

-- Hour of day patterns
select
    extract(hour from timestamp) as hour,
    count(*) as event_count,
    avg(value) as avg_value
from events
group by hour
order by hour;

-- Seasonality decomposition (simple)
with daily_avg as (
    select avg(revenue) as overall_avg
    from daily_sales
),
dow_avg as (
    select
        extract(dow from date) as dow,
        avg(revenue) as dow_avg
    from daily_sales
    group by dow
)
select
    s.date,
    s.revenue,
    d.overall_avg,
    dow.dow_avg,
    s.revenue - dow.dow_avg as detrended
from daily_sales s
cross join daily_avg d
join dow_avg dow on extract(dow from s.date) = dow.dow;
```

## Cohort Analysis

Cohort analysis tracks groups of users over time (e.g., retention by signup month).

### User Retention

```sql
-- Basic retention cohort
with cohorts as (
    select
        user_id,
        date_trunc('month', created_at) as cohort_month
    from users
),
user_activity as (
    select
        user_id,
        date_trunc('month', activity_date) as activity_month
    from activity_log
)
select
    c.cohort_month,
    u.activity_month,
    -- Months since cohort start
    datediff('month', c.cohort_month, u.activity_month) as month_number,
    count(distinct u.user_id) as active_users,
    count(distinct u.user_id) * 100.0 / count(distinct c.user_id) as retention_pct
from cohorts c
left join user_activity u on c.user_id = u.user_id
group by c.cohort_month, u.activity_month
order by c.cohort_month, month_number;

-- Simplified version with cohort size
with cohort_sizes as (
    select
        date_trunc('month', created_at) as cohort_month,
        count(*) as cohort_size
    from users
    group by cohort_month
),
cohort_activity as (
    select
        date_trunc('month', u.created_at) as cohort_month,
        date_trunc('month', a.activity_date) as activity_month,
        count(distinct a.user_id) as active_users
    from users u
    join activity_log a on u.user_id = a.user_id
    group by cohort_month, activity_month
)
select
    a.cohort_month,
    a.activity_month,
    datediff('month', a.cohort_month, a.activity_month) as month_number,
    a.active_users,
    s.cohort_size,
    100.0 * a.active_users / s.cohort_size as retention_pct
from cohort_activity a
join cohort_sizes s on a.cohort_month = s.cohort_month
order by a.cohort_month, month_number;
```

### Revenue Cohorts

```sql
-- LTV by cohort
with cohorts as (
    select
        user_id,
        date_trunc('month', first_purchase_date) as cohort_month
    from users
),
cohort_revenue as (
    select
        c.cohort_month,
        date_trunc('month', o.order_date) as revenue_month,
        datediff('month', c.cohort_month, date_trunc('month', o.order_date)) as month_number,
        sum(o.amount) as revenue
    from cohorts c
    join orders o on c.user_id = o.user_id
    group by c.cohort_month, revenue_month
)
select
    cohort_month,
    month_number,
    revenue,
    sum(revenue) over (
        partition by cohort_month
        order by month_number
    ) as cumulative_ltv
from cohort_revenue
order by cohort_month, month_number;
```

### Cohort Pivot Table

```sql
-- Retention pivot: cohorts as rows, months as columns
with retention_data as (
    select
        date_trunc('month', u.created_at) as cohort_month,
        datediff('month', 
            date_trunc('month', u.created_at),
            date_trunc('month', a.activity_date)
        ) as month_number,
        count(distinct a.user_id) * 100.0 / count(distinct u.user_id) as retention_pct
    from users u
    left join activity_log a on u.user_id = a.user_id
    group by cohort_month, month_number
)
pivot retention_data
on month_number
using first(retention_pct)
order by cohort_month;
```

## Funnel Analysis

Track user progression through stages (signup → activation → purchase).

### Basic Funnel

```sql
with funnel_events as (
    select
        user_id,
        max(case when event_type = 'signup' then 1 else 0 end) as signup,
        max(case when event_type = 'activation' then 1 else 0 end) as activation,
        max(case when event_type = 'purchase' then 1 else 0 end) as purchase
    from events
    group by user_id
)
select
    count(*) as total_users,
    sum(signup) as signups,
    sum(activation) as activations,
    sum(purchase) as purchases,
    100.0 * sum(activation) / sum(signup) as signup_to_activation,
    100.0 * sum(purchase) / sum(activation) as activation_to_purchase,
    100.0 * sum(purchase) / sum(signup) as overall_conversion
from funnel_events
where signup = 1;
```

### Time-Bound Funnels

Only count conversions within a time window.

```sql
with user_events as (
    select
        user_id,
        event_type,
        timestamp,
        min(case when event_type = 'signup' then timestamp end) over (partition by user_id) as signup_time
    from events
),
conversions as (
    select
        user_id,
        max(case when event_type = 'signup' then 1 else 0 end) as signup,
        max(case 
            when event_type = 'activation' 
                and timestamp <= signup_time + interval 7 day
            then 1 else 0 end
        ) as activation_7d,
        max(case 
            when event_type = 'purchase'
                and timestamp <= signup_time + interval 30 day
            then 1 else 0 end
        ) as purchase_30d
    from user_events
    group by user_id
)
select
    count(*) as total_users,
    sum(signup) as signups,
    sum(activation_7d) as activations_within_7d,
    sum(purchase_30d) as purchases_within_30d,
    100.0 * sum(activation_7d) / sum(signup) as activation_rate,
    100.0 * sum(purchase_30d) / sum(signup) as purchase_rate
from conversions
where signup = 1;
```

### Funnel Drop-off Points

```sql
with step_sequence as (
    select
        user_id,
        event_type,
        timestamp,
        row_number() over (partition by user_id order by timestamp) as step_num
    from events
    where event_type in ('signup', 'activation', 'first_purchase')
),
user_journeys as (
    select
        user_id,
        array_agg(event_type order by timestamp) as journey
    from step_sequence
    group by user_id
)
select
    journey,
    count(*) as user_count,
    100.0 * count(*) / sum(count(*)) over () as pct_of_total
from user_journeys
group by journey
order by user_count desc;
```

## Aggregation Strategies

### Group by Multiple Dimensions

```sql
-- Traditional GROUP BY
select
    country,
    category,
    count(*) as orders,
    sum(amount) as revenue
from orders
group by country, category
order by country, category;

-- ROLLUP (add subtotals and grand total)
select
    country,
    category,
    count(*) as orders,
    sum(amount) as revenue
from orders
group by rollup(country, category)
order by country, category;

-- CUBE (all combinations of dimensions)
select
    country,
    category,
    count(*) as orders,
    sum(amount) as revenue
from orders
group by cube(country, category);

-- GROUPING SETS (specific combinations)
select
    country,
    category,
    count(*) as orders,
    sum(amount) as revenue
from orders
group by grouping sets (
    (country, category),  -- By country and category
    (country),            -- By country only
    (category),           -- By category only
    ()                    -- Grand total
);
```

### Conditional Aggregation

```sql
-- Pivot metrics with CASE
select
    user_id,
    count(*) as total_orders,
    count(case when status = 'completed' then 1 end) as completed_orders,
    count(case when status = 'cancelled' then 1 end) as cancelled_orders,
    sum(case when status = 'completed' then amount else 0 end) as completed_revenue,
    avg(case when status = 'completed' then amount end) as avg_completed_amount
from orders
group by user_id;

-- Filter before aggregation (more efficient)
select
    user_id,
    count(*) filter (where status = 'completed') as completed_orders,
    count(*) filter (where status = 'cancelled') as cancelled_orders,
    sum(amount) filter (where status = 'completed') as completed_revenue
from orders
group by user_id;
```

### Top N per Group

```sql
-- Top 3 products by revenue per category
with ranked as (
    select
        category,
        product_id,
        revenue,
        row_number() over (partition by category order by revenue desc) as rank
    from products
)
select category, product_id, revenue
from ranked
where rank <= 3
order by category, rank;

-- Using QUALIFY (DuckDB-specific, more concise)
select
    category,
    product_id,
    revenue
from products
qualify row_number() over (partition by category order by revenue desc) <= 3
order by category, revenue desc;

-- Top 10% per group
select
    category,
    product_id,
    revenue
from products
qualify percent_rank() over (partition by category order by revenue desc) < 0.10;
```

### Percentiles & Distribution

```sql
-- Quartiles and percentiles
select
    category,
    min(revenue) as min,
    approx_quantile(revenue, 0.25) as q1,
    approx_quantile(revenue, 0.50) as median,
    approx_quantile(revenue, 0.75) as q3,
    max(revenue) as max,
    approx_quantile(revenue, 0.90) as p90,
    approx_quantile(revenue, 0.95) as p95,
    approx_quantile(revenue, 0.99) as p99
from products
group by category;

-- Histogram bins
select
    width_bucket(revenue, 0, 1000, 10) as bucket,
    count(*) as frequency
from products
group by bucket
order by bucket;
```

## JOIN Patterns

### Inner Join (Matching Records Only)

```sql
-- Basic inner join
select
    u.user_id,
    u.name,
    o.order_id,
    o.amount
from users u
join orders o on u.user_id = o.user_id;
```

### Left Join (Keep All Left Records)

```sql
-- All users, even those with no orders
select
    u.user_id,
    u.name,
    count(o.order_id) as order_count,
    coalesce(sum(o.amount), 0) as total_revenue
from users u
left join orders o on u.user_id = o.user_id
group by u.user_id, u.name;

-- Find users with no orders
select u.*
from users u
left join orders o on u.user_id = o.user_id
where o.order_id is null;
```

### Self-Join (Compare Rows in Same Table)

```sql
-- Find users in the same city
select
    u1.user_id as user1,
    u2.user_id as user2,
    u1.city
from users u1
join users u2 on u1.city = u2.city and u1.user_id < u2.user_id;

-- Sequential events (current and next)
select
    e1.event_id as current_event,
    e2.event_id as next_event,
    e2.timestamp - e1.timestamp as time_diff
from events e1
join events e2 
    on e1.user_id = e2.user_id
    and e2.timestamp > e1.timestamp
    and e2.timestamp = (
        select min(timestamp)
        from events e3
        where e3.user_id = e1.user_id
            and e3.timestamp > e1.timestamp
    );
```

### Cross Join (Cartesian Product)

```sql
-- All combinations of categories and dates
select
    c.category,
    d.date,
    coalesce(sum(s.revenue), 0) as revenue
from categories c
cross join date_spine d
left join sales s 
    on c.category = s.category
    and d.date = s.sale_date
group by c.category, d.date;
```

### Multiple Join Conditions

```sql
-- Range join (events within time window)
select
    o.order_id,
    o.user_id,
    o.order_date,
    count(e.event_id) as event_count
from orders o
left join events e
    on o.user_id = e.user_id
    and e.timestamp between o.order_date - interval 7 day and o.order_date
group by o.order_id, o.user_id, o.order_date;
```

### Anti-Join (Records NOT in Another Table)

```sql
-- Users who never made a purchase
select u.*
from users u
where not exists (
    select 1 from orders o
    where o.user_id = u.user_id
);

-- Alternative with LEFT JOIN
select u.*
from users u
left join orders o on u.user_id = o.user_id
where o.user_id is null;
```

### Semi-Join (Records WITH Match, No Duplication)

```sql
-- Users who made at least one purchase
select u.*
from users u
where exists (
    select 1 from orders o
    where o.user_id = u.user_id
);

-- Alternative with DISTINCT
select distinct u.*
from users u
join orders o on u.user_id = o.user_id;
```

## Deduplication Strategies

### Keep First or Last Record

```sql
-- Keep first occurrence
select distinct on (user_id) *
from events
order by user_id, timestamp;

-- Keep most recent
select distinct on (user_id) *
from events
order by user_id, timestamp desc;

-- Window function approach (more flexible)
with ranked as (
    select *,
        row_number() over (partition by user_id order by timestamp desc) as rn
    from events
)
select * from ranked where rn = 1;
```

### Exact Duplicates

```sql
-- Find exact duplicates
select
    user_id,
    email,
    count(*) as duplicate_count
from users
group by user_id, email
having count(*) > 1;

-- Remove exact duplicates (keep one)
create table users_clean as
select distinct * from users;

-- Or with row_number
create table users_clean as
with ranked as (
    select *,
        row_number() over (partition by user_id, email order by created_at) as rn
    from users
)
select * from ranked where rn = 1;
```

### Fuzzy Duplicates (Similar Records)

```sql
-- Find similar names (Levenshtein distance)
select
    u1.user_id as user1,
    u2.user_id as user2,
    u1.name as name1,
    u2.name as name2,
    levenshtein(u1.name, u2.name) as edit_distance
from users u1
join users u2 on u1.user_id < u2.user_id
where levenshtein(u1.name, u2.name) <= 2;

-- Similar emails (by domain)
select
    regexp_extract(email, '@(.+)$', 1) as domain,
    count(*) as user_count
from users
group by domain
having count(*) > 1;
```

## Advanced Window Functions

### Running Calculations

```sql
-- Running min/max
select
    date,
    revenue,
    max(revenue) over (order by date rows between unbounded preceding and current row) as max_to_date,
    min(revenue) over (order by date rows between unbounded preceding and current row) as min_to_date
from daily_sales;

-- Running count of events
select
    user_id,
    event_date,
    row_number() over (partition by user_id order by event_date) as event_number,
    count(*) over (partition by user_id order by event_date) as events_to_date
from user_events;
```

### Gaps and Islands

Find continuous sequences and gaps.

```sql
-- Identify consecutive day streaks
with daily_activity as (
    select distinct
        user_id,
        date(activity_date) as activity_date
    from activity_log
),
with_prev as (
    select
        user_id,
        activity_date,
        lag(activity_date) over (partition by user_id order by activity_date) as prev_date,
        activity_date - lag(activity_date) over (partition by user_id order by activity_date) as gap
    from daily_activity
),
islands as (
    select
        user_id,
        activity_date,
        sum(case when gap = 1 or gap is null then 0 else 1 end) 
            over (partition by user_id order by activity_date) as island_id
    from with_prev
)
select
    user_id,
    island_id,
    min(activity_date) as streak_start,
    max(activity_date) as streak_end,
    count(*) as streak_length
from islands
group by user_id, island_id
order by user_id, streak_start;
```

### Session Identification

Group events into sessions based on time gaps.

```sql
with event_gaps as (
    select
        user_id,
        event_id,
        timestamp,
        timestamp - lag(timestamp) over (partition by user_id order by timestamp) as gap
    from events
),
session_starts as (
    select
        user_id,
        event_id,
        timestamp,
        case when gap is null or gap > interval 30 minute then 1 else 0 end as is_new_session
    from event_gaps
),
sessions as (
    select
        user_id,
        event_id,
        timestamp,
        sum(is_new_session) over (partition by user_id order by timestamp) as session_id
    from session_starts
)
select
    user_id,
    session_id,
    min(timestamp) as session_start,
    max(timestamp) as session_end,
    count(*) as event_count
from sessions
group by user_id, session_id;
```

## Performance Patterns

### Avoid Correlated Subqueries

```sql
-- Bad: Correlated subquery (runs for each row)
select
    u.user_id,
    u.name,
    (select count(*) from orders o where o.user_id = u.user_id) as order_count
from users u;

-- Good: Join with aggregation
select
    u.user_id,
    u.name,
    coalesce(o.order_count, 0) as order_count
from users u
left join (
    select user_id, count(*) as order_count
    from orders
    group by user_id
) o on u.user_id = o.user_id;
```

### Filter Before Join

```sql
-- Bad: Filter after join (processes all rows)
select *
from large_table a
join another_large_table b on a.id = b.id
where a.date = '2024-01-01';

-- Good: Filter before join (reduces rows early)
select *
from (select * from large_table where date = '2024-01-01') a
join another_large_table b on a.id = b.id;

-- Or use CTE
with filtered as (
    select * from large_table where date = '2024-01-01'
)
select *
from filtered a
join another_large_table b on a.id = b.id;
```

### Incremental Processing

```sql
-- Process only new data
create table processed_events as
select * from events where 1=0; -- Empty table with same schema

-- Initial load
insert into processed_events
select * from events
where date = current_date;

-- Daily incremental load
insert into processed_events
select * from events
where date = current_date
    and event_id not in (select event_id from processed_events);

-- Better: Use NOT EXISTS
insert into processed_events
select e.* from events e
where e.date = current_date
    and not exists (
        select 1 from processed_events p
        where p.event_id = e.event_id
    );
```

## Testing & Validation

### Data Quality Checks

```sql
-- Check for nulls in critical columns
select
    'user_id' as column_name,
    count(*) filter (where user_id is null) as null_count,
    count(*) as total_count,
    100.0 * count(*) filter (where user_id is null) / count(*) as null_pct
from users
union all
select
    'email',
    count(*) filter (where email is null),
    count(*),
    100.0 * count(*) filter (where email is null) / count(*)
from users;

-- Duplicate check
select
    'users' as table_name,
    count(*) as total_rows,
    count(distinct user_id) as unique_ids,
    count(*) - count(distinct user_id) as duplicates
from users;

-- Value range validation
select
    count(*) filter (where age < 0 or age > 120) as invalid_age,
    count(*) filter (where created_at > current_timestamp) as future_dates,
    count(*) filter (where email not like '%@%') as invalid_emails
from users;
```

### Reconciliation

```sql
-- Compare aggregates across tables
with source_totals as (
    select
        'source' as source,
        count(*) as row_count,
        sum(amount) as total_amount
    from source_table
),
target_totals as (
    select
        'target' as source,
        count(*) as row_count,
        sum(amount) as total_amount
    from target_table
)
select * from source_totals
union all
select * from target_totals;
```

## Common Pitfalls

### NULL Handling

```sql
-- Bad: NULL != NULL (always FALSE)
select * from users where manager_id = null; -- Returns nothing!

-- Good: Use IS NULL
select * from users where manager_id is null;

-- NULL in aggregations (ignored)
select avg(revenue) from sales; -- Ignores NULL values
select avg(coalesce(revenue, 0)) from sales; -- Treats NULL as 0

-- NULL in comparisons
select * from products where price > 100; -- Excludes NULL prices
select * from products where price > 100 or price is null; -- Includes NULL
```

### String Comparison

```sql
-- Case sensitivity
select * from users where name = 'John'; -- Won't match 'john'
select * from users where lower(name) = 'john'; -- Case-insensitive

-- Whitespace
select * from users where trim(name) = 'John'; -- Removes leading/trailing spaces
```

### Date/Time Gotchas

```sql
-- Timestamp vs Date
select * from events where timestamp = '2024-01-01'; -- Exact match only
select * from events where date(timestamp) = '2024-01-01'; -- Full day

-- Better: Use range
select * from events
where timestamp >= '2024-01-01'
    and timestamp < '2024-01-02';
```

## See Also

- [[duckdb]] - DuckDB-specific syntax and workflows
- [[python-notes]] - Python data analysis integration
- [[fp]] - Functional programming patterns (query composition)
- [[duckdb-regex-cheatsheet]] - Regex patterns for data extraction
