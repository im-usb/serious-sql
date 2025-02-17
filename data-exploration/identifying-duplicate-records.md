# Dealing with Duplicate Data

## 1. Introduction to Health Data

### 1.1 Dataset Inspection

> Inspect a snapshot to view the first 10 rows from the health.user_logs table:

```sql
SELECT
    *
FROM
    health.user_logs
LIMIT
    10;

```

Output:
| id | log_date | measure | measure_value | systolic | diastolic |
|-------------------------------------|------------|-----------------|-----------------|------------|-------------|
| fa28f948a740320ad56b81a24744c8b81df119fa | 2020-11-15 | weight | 46.03959 | null | null |
| 1a7366eef15512d8f38133e7ce9778bce5b4a21e | 2020-10-10 | blood_glucose | 97 | 0 | 0 |
| bd7eece38fb4ec71b3282d60080d296c4cf6ad5e | 2020-10-18 | blood_glucose | 120 | 0 | 0 |
| 0f7b13f3f0512e6546b8d2c0d56e564a2408536a | 2020-10-17 | blood_glucose | 232 | 0 | 0 |
| d14df0c8c1a5f172476b2a1b1f53cf23c6992027 | 2020-10-15 | blood_pressure | 140 | 140 | 113 |
| 0f7b13f3f0512e6546b8d2c0d56e564a2408536a | 2020-10-21 | blood_glucose | 166 | 0 | 0 |
| 0f7b13f3f0512e6546b8d2c0d56e564a2408536a | 2020-10-22 | blood_glucose | 142 | 0 | 0 |
| 87be2f14a5550389cb2cba03b3329c54c993f7d2 | 2020-10-12 | weight | 129.060012817 | 0 | 0 |
| 0efe1f378aec122877e5f24f204ea70709b1f5f8 | 2020-10-07 | blood_glucose | 138 | 0 | 0 |
| 054250c692e07a9fa9e62e345231df4b54ff435d | 2020-10-04 | blood_glucose | 210 | null | null |

### 1.2 Record Counts

> Take a quick look at a few basic counts to get a good feel for our dataset.

```sql
SELECT
    COUNT(*)
FROM
    health.user_logs;
```

Output:
| count |
|-------|
| 43891 |

### 1.3 Unique Column Counts

> Using the COUNT DISTINCT to take a look at how many unique id values there are in this dataset. This will give us a feel for how many unique users there are whilst we continue getting a better picture of what’s going on

```sql
select
  count(distinct id)
from
  health.user_logs
```

Output:
| count |
|-------|
| 554 |

### 1.4 Single Column Frequency Counts

> Inspect that measure column and take a look at the most frequent values within this column using a GROUP BY and ORDER BY DESC.

```sql
SELECT
    measure,
    COUNT(*) AS frequency,
    ROUND(
        100 * COUNT(measure)::numeric / SUM(COUNT(measure)) OVER(),
        2
    ) AS percentage
FROM
    health.user_logs
GROUP BY
    measure
ORDER BY
    frequency DESC;

```

Output:
| measure | frequency | percentage |
|------------------|-----------|------------|
| blood_glucose | 38692 | 88.15 |
| weight | 2782 | 6.34 |
| blood_pressure | 2417 | 5.51 |

> Doing the same for the id column too and limit the output to just the first 10 rows.

```sql
SELECT
    id,
    COUNT(*) AS frequency,
    ROUND(100 * COUNT(*) / SUM(COUNT(*)) OVER(), 2) AS percent_share
FROM
    health.user_logs
GROUP BY
    id
ORDER BY
    frequency DESC
LIMIT
    10;
```

Output:
| id | frequency | percentage |
|------------------------------------------|-----------|------------|
| 054250c692e07a9fa9e62e345231df4b54ff435d | 22325 | 50.86 |
| 0f7b13f3f0512e6546b8d2c0d56e564a2408536a | 1589 | 3.62 |
| ee653a96022cc3878e76d196b1667d95beca2db6 | 1235 | 2.81 |
| abc634a555bbba7d6d6584171fdfa206ebf6c9a0 | 1212 | 2.76 |
| 576fdb528e5004f733912fae3020e7d322dbc31a | 1018 | 2.32 |
| 87be2f14a5550389cb2cba03b3329c54c993f7d2 | 747 | 1.70 |
| 46d921f1111a1d1ad5dd6eb6e4d0533ab61907c9 | 651 | 1.48 |
| fba135f6a50a2e3f371e47f943b025705f9d9617 | 633 | 1.44 |
| d696925de5e9297694ef32a1c9871f3629bec7e5 | 597 | 1.36 |
| 6c2f9a8372dac248192c50219c97f9087ab778ba | 582 | 1.33 |

### 1.5 Individual Column Distributions

#### 1.5.1 Measure Column

> Top 10 most frequent measure_value along with their frequencies

```sql
SELECT
    measure_value,
    COUNT(*) AS frequency
FROM
    health.user_logs
GROUP BY
    1
ORDER BY
    frequency DESC
LIMIT
    10;
```

Output:
| measure_value | frequency |
|---------------|-----------|
| 0 | 572 |
| 401 | 433 |
| 117 | 390 |
| 118 | 346 |
| 123 | 342 |
| 122 | 331 |
| 126 | 326 |
| 120 | 323 |
| 128 | 319 |
| 115 | 319 |

#### 1.5.2 Systolic

> Top 10 most frequent systolic along with their frequencies

```sql
SELECT
    systolic,
    COUNT(*) AS frequency
FROM
    health.user_logs
GROUP BY
    1
ORDER BY
    frequency DESC
LIMIT
    10;
```

Output:
| systolic | frequency |
|----------|-----------|
| null | 26023 |
| 0 | 15451 |
| 120 | 71 |
| 123 | 70 |
| 128 | 66 |
| 127 | 64 |
| 130 | 60 |
| 119 | 60 |
| 135 | 57 |
| 124 | 55 |

#### 1.5.3 Diastolic

> Top 10 most frequent diastolic along with their frequencies

```sql
SELECT
    diastolic,
    COUNT(*) AS frequency
FROM
    health.user_logs
GROUP BY
    1
ORDER BY
    frequency DESC
LIMIT
    10;
```

Output:
| systolic | frequency |
|----------|-----------|
| null | 26023 |
| 0 | 15451 |
| 120 | 71 |
| 123 | 70 |
| 128 | 66 |
| 127 | 64 |
| 130 | 60 |
| 119 | 60 |
| 135 | 57 |
| 124 | 55 |

#### 1.5.4. Deeper Look Into Specific Values

> There are many 0 values under **measure_value** field and null values for both systolic and diastolic columns. To inspect these rows a bit further - will use a simple WHERE filter and check if this only happens for certain **measure** values when the condition **measure_value** = 0 is met and the systolic and diastolic columns are null.

```sql
SELECT
    measure,
    COUNT(*) AS frequency
FROM
    health.user_logs
WHERE
    measure_value = 0
GROUP BY
    1;
```

Output(frequency only):

| measure        | count |
| -------------- | ----- |
| blood_glucose  | 8     |
| blood_pressure | 562   |
| weight         | 2     |

Output(with percentage):

| measure        | frequency | percentage |
| -------------- | --------- | ---------- |
| blood_glucose  | 8         | 1.40       |
| blood_pressure | 562       | 98.25      |
| weight         | 2         | 0.35       |

> We can dig in a little bit further and inspect a few rows for **measure_value**=0 and **measure**='blood_pressure':

```sql
SELECT
    *
FROM
    health.user_logs
WHERE
    measure_value = 0
    AND measure = 'blood_pressure'
LIMIT
    10;
```

| id       | log_date   | measure        | measure_value | systolic | diastolic |
| -------- | ---------- | -------------- | ------------- | -------- | --------- |
| ee653a96 | 2020-03-18 | blood_pressure | 0             | 115      | 76        |
| ee653a96 | 2020-03-15 | blood_pressure | 0             | 115      | 76        |
| ee653a96 | 2020-02-03 | blood_pressure | 0             | 105      | 70        |
| 0f7b13f3 | 2020-02-24 | blood_pressure | 0             | 136      | 87        |
| c7af488f | 2020-02-06 | blood_pressure | 0             | 164      | 84        |
| c7af488f | 2020-02-10 | blood_pressure | 0             | 190      | 94        |
| 0f7b13f3 | 2020-02-07 | blood_pressure | 0             | 125      | 79        |
| 0f7b13f3 | 2020-02-19 | blood_pressure | 0             | 136      | 84        |
| 0f7b13f3 | 2020-02-15 | blood_pressure | 0             | 135      | 89        |
| 0f7b13f3 | 2020-02-27 | blood_pressure | 0             | 138      | 85        |

Indeed there are 0 values under **measure_value** column.

> How about we check for the non-zero values under **measure_value** column

```sql
SELECT
    *
FROM
    health.user_logs
WHERE
    measure_value != 0
    AND measure = 'blood_pressure'
LIMIT
    10;
```

Output:
| id | log_date | measure | measure_value | systolic | diastolic |
|-----------|------------|-----------------|---------------|----------|-----------|
| d14df0c8 | 2020-10-15 | blood_pressure | 140 | 140 | 113 |
| 9fef7a7b | 2020-10-02 | blood_pressure | 114 | 114 | 80 |
| 0b494d45 | 2020-10-19 | blood_pressure | 132 | 132 | 94 |
| ee653a96 | 2020-10-09 | blood_pressure | 105 | 105 | 68 |
| 46d921f1 | 2020-04-12 | blood_pressure | 149 | 149 | 85 |
| 46d921f1 | 2020-04-10 | blood_pressure | 156 | 156 | 88 |
| 46d921f1 | 2020-04-29 | blood_pressure | 142 | 142 | 84 |
| 0f7b13f3 | 2020-04-07 | blood_pressure | 131 | 131 | 71 |
| 0f7b13f3 | 2020-04-08 | blood_pressure | 128 | 128 | 77 |
| abc634a5 | 2020-03-09 | blood_pressure | 114 | 114 | 76 |

Looks like there are duplicates among measure_value and systolic values

> the null systolic and diastolic values

```sql
SELECT
  measure,
  COUNT(*)
FROM health.user_logs
WHERE systolic IS NULL
GROUP BY 1;
```

Systolic:
| measure | frequency |
|----------------|-----------|
| weight | 443 |
| blood_glucose | 25580 |

```sql
SELECT
  measure,
  COUNT(*)
FROM health.user_logs
WHERE diastolic IS NULL
GROUP BY 1;
```

Diastolic:
| measure | frequency |
|----------------|-----------|
| weight | 443 |
| blood_glucose | 25580 |

## 2. How To Deal With Duplicates

In SQL there are a few different ways to deal with these duplicate records:

- Remove them in a SELECT statement
- Recreating a “clean” version of our dataset
- Identify exactly which rows are duplicated for further investigation or
- Simply ignore the duplicates and leave the dataset alone

### 2.1. Detecting Duplicate Records

> First run the basic record count query for our table

```sql
SELECT COUNT(*)
FROM health.user_logs;
```

Output:
| count |
|--------|
| 43891 |

### 2.2. Remove All Duplicates

> Apply DISTINCT keyword to the count query for the same table

```sql
SELECT COUNT(DISTINCT *)
FROM health.user_logs;
```

But the above syntax cannot be run in PostgreSQL. To do something similar this we can use following techniques:

#### 2.2.1 Subqueries

A subquery is essentially a query within a query.

```sql
SELECT COUNT(*)
FROM (
  SELECT DISTINCT *
  FROM health.user_logs
) AS subquery
;
```

Output:
| count |
|--------|
| 31004 |

#### 2.2.2. Common Table Expression

CTE stands for Common Table Expression. It is like a temporary table that helps simplify complex SQL queries by breaking them into smaller, more manageable parts.

```sql
WITH deduped_logs AS (
  SELECT DISTINCT *
  FROM health.user_logs
)
SELECT COUNT(*)
FROM deduped_logs;
```

Output:
| count |
|--------|
| 31004 |

#### 2.2.3. Temporary Tables

We can also create a temporary table with only the unique values of our dataset after we run the DISTINCT query.

The main benefit of using temporary tables is removing the need to always run the same DISTINCT command everytime you want to run a query on the deduplicated records.

- First we run a DROP TABLE IF EXISTS statement to clear out any previously created tables

```sql
DROP TABLE IF EXISTS deduplicated_user_logs;
```

- Next let’s create a new temporary table using the results of the query below

```sql
CREATE TEMP TABLE deduplicated_user_logs AS
SELECT DISTINCT *
FROM health.user_logs;
```

- Finally, run the same COUNT on this deduplicated temp table to confirm it did the right thing!

```sql
SELECT
    COUNT(*)
FROM
    deduplicated_user_logs;
```

> We now have the row counts of the original table 43,891 and of our deduplicated table 31,004 It’s pretty safe to say that we have some duplicate records!

## 3. Identifying Duplicate Records

The aim here is to write a final SQL statement to efficiently return us all rows which are duplicated in our target table.

### 3.1. Group By Counts On All Columns

> Using the following SQL query we can get a frequency table of the duplicates that are there in the table

```sql
SELECT
  id,
  log_date,
  measure,
  measure_value,
  systolic,
  diastolic,
  COUNT(*) AS frequency
FROM health.user_logs
GROUP BY
  id,
  log_date,
  measure,
  measure_value,
  systolic,
  diastolic
ORDER BY frequency DESC
```

Output:
| id | log_date | measure | value | sys | dia | frequency |
|-----------|------------|----------------|-------|------|------|-----------|
| 054250c | 2019-12-06 | blood_glucose | 401 | null | null | 104 |
| 054250c | 2019-12-05 | blood_glucose | 401 | null | null | 77 |
| 054250c | 2019-12-04 | blood_glucose | 401 | null | null | 72 |
| 054250c | 2019-12-07 | blood_glucose | 401 | null | null | 70 |
| 054250c | 2020-09-30 | blood_glucose | 401 | null | null | 39 |
| 054250c | 2020-09-29 | blood_glucose | 401 | null | null | 24 |
| 054250c | 2020-10-02 | blood_glucose | 401 | null | null | 18 |
| 054250c | 2019-12-10 | blood_glucose | 140 | null | null | 12 |
| 054250c | 2019-12-11 | blood_glucose | 220 | null | null | 12 |
| 054250c | 2020-04-15 | blood_glucose | 236 | null | null | 12 |
| ... | ... | ... | ... | ... | ... | ... |
| 054250c6 | 2020-01-10 | blood_glucose | 93 | null | null | 1 |
| d696925d | 2020-06-02 | blood_glucose | 307 | 0 | 0 | 1 |
| 29874263 | 2020-03-24 | blood_glucose | 135 | 0 | 0 | 1 |
| bca46f69 | 2020-05-30 | blood_glucose | 120 | 0 | 0 | 1 |
| d696925d | 2020-05-07 | blood_glucose | 224 | 0 | 0 | 1 |
| fba135f6 | 2020-04-18 | blood_glucose | 110 | 0 | 0 | 1 |
| 832e45ce | 2019-11-24 | blood_glucose | 100 | 0 | 0 | 1 |
| 054250c6 | 2020-05-07 | blood_glucose | 291 | null | null | 1 |
| 576fdb52 | 2020-06-20 | blood_glucose | 98 | null | null | 1 |
| 6d9c9595 | 2020-02-06 | blood_pressure | 122 | 122 | 81 | 1 |

> Here it can be seen that value of frequency is greater than 1 for many of the records

### 3.2. Having Clause For Unique Duplicates

> using HAVING clause we can get the records of frequency greater than 1.

```sql
DROP TABLE IF EXISTS unique_duplicate_records;

CREATE TEMPORARY TABLE unique_duplicate_records AS
SELECT *
FROM health.user_logs
GROUP BY
  id,
  log_date,
  measure,
  measure_value,
  systolic,
  diastolic
HAVING COUNT(*) > 1;
```

Finally, inspect the top 10 rows of the temp table

```sql
SELECT *
FROM unique_duplicate_records
LIMIT 10;
```

### 3.3. Retaining Duplicate Counts

> In order to get the output what we did before with frequency values except for the fact that we remove all the rows with frequency = 1, we can use the following ways to do so.

1. Using CTE

```sql
WITH groupby_counts AS (
  SELECT
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic,
    COUNT(*) AS frequency
  FROM health.user_logs
  GROUP BY
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
SELECT *
FROM groupby_counts
WHERE frequency > 1
ORDER BY frequency DESC
LIMIT 10;
```

2. Using Normal SELECT query

```sql
SELECT
  id,
  log_date,
  measure,
  measure_value,
  systolic,
  diastolic,
  COUNT(*) AS frequency
FROM
  health.user_logs
GROUP BY
  id,
  log_date,
  measure,
  measure_value,
  systolic,
  diastolic
HAVING
  COUNT(*) > 1
ORDER BY
  frequency DESC
LIMIT 10;
```

## 4. Exercises

### 4.1. Highest Duplicate

> Which id value has the most number of duplicate records in the health.user_logs table?

```sql
WITH groupby_counts AS (
  SELECT
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic,
    COUNT(*) AS frequency
  FROM health.user_logs
  GROUP BY
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
SELECT
  id,
  SUM(frequency) AS total_duplicate_rows
FROM groupby_counts
WHERE frequency > 1
GROUP BY id
ORDER BY total_duplicate_rows DESC
LIMIT 1;
```

Output:
| id | total_duplicate_rows |
|------------------------------------------|----------------------|
| 054250c692e07a9fa9e62e345231df4b54ff435d | 17279 |

### 4.2. Highest Duplicate

> Which log_date value had the most duplicate records after removing the max duplicate id value from question 1?

```sql
WITH groupby_table as (
  select
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic,
    count(*) as frequency
  from
    health.user_logs
  where
    id != '054250c692e07a9fa9e62e345231df4b54ff435d'
  group by
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
Select
  log_date,
  sum(frequency) as total_duplicates
from
  groupby_table
where
  frequency > 1
group by
  log_date
order by
  total_duplicates desc
limit
  1;
```

Output:
| log_date | total_duplicate_rows |
|------------|----------------------|
| 2019-12-11 | 55 |

### 4.3. Highest Duplicate

> Which measure_value had the most occurences in the health.user_logs value when measure = 'weight'?

```sql
SELECT
    measure_value,
    COUNT(*) AS frequency
FROM
    health.user_logs
WHERE
    measure = 'weight'
GROUP BY
    measure_value
ORDER BY
    frequency DESC
LIMIT
    1;
```

Output:
| measure_value | frequency |
|----------------|-----------|
| 68.49244787 | 109 |

### 4.4. Highest Duplicate

> How many single duplicated rows exist when measure = 'blood_pressure' in the health.user_logs? How about the total number of duplicate records in the same table?

```sql
WITH measure_table AS (
  SELECT
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic,
    COUNT(*) AS frequency
  FROM
    health.user_logs
  WHERE
    measure = 'blood_pressure'
  GROUP BY
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
SELECT
  COUNT(*) AS single_duplicates,
  SUM(frequency) AS total_duplicates
FROM
  measure_table
WHERE
  frequency > 1;
```

Output:
| single_duplicate_rows | total_duplicate_records |
|-----------------------|-------------------------|
| 147 | 301 |

### 4.5. Highest Duplicate

> What percentage of records measure_value = 0 when measure = 'blood_pressure' in the health.user_logs table? How many records are there also for this same condition?

```sql
WITH measure_table AS (
  SELECT
    measure_value,
    COUNT(*) AS records,
    SUM(COUNT(*)) OVER() AS total_records
  FROM
    health.user_logs
  WHERE
    measure = 'blood_pressure'
  GROUP BY
    measure_value
)
SELECT
  measure_value,
  records,
  total_records,
  ROUND(100 * records::numeric / total_records, 2) AS percentage
FROM
  measure_table
WHERE
  measure_value = 0;
```

Output:
| measure_value | total_records | overall_total | percentage |
|---------------|---------------|---------------|------------|
| 0 | 562 | 2417 | 23.25 |

### 4.6. Highest Duplicate

> What percentage of records are duplicates in the health.user_logs table?

```sql
WITH groupby_counts AS (
  SELECT
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic,
    COUNT(*) AS frequency
  FROM health.user_logs
  GROUP BY
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
SELECT
  ROUND(
    100 * SUM(CASE
        WHEN frequency > 1 THEN frequency - 1
        ELSE 0 END
    )::NUMERIC / SUM(frequency),
    2
  ) AS duplicate_percentage
FROM groupby_counts;
```

Output:
| duplicate_percentage |
|----------------------|
| 29.36 |
