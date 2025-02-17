# Health Analytics Mini Case Study

**Situation:** The General Manager of Analytics at Health Co. urgently requested our help in analyzing the health.user_logs dataset. The Health Co. analytics team provided their SQL script, but encountered bugs they couldn't resolve. Our task is to swiftly debug their SQL script and utilize the corrected query outputs to promptly address inquiries posed by the GM for an upcoming board meeting regarding their active users.

> Following are the business questions that the General Manager needs help with:

1. How many unique users exist in the logs dataset?
2. How many total measurements do we have per user on average?
3. What about the median number of measurements per user?
4. How many users have 3 or more measurements?
5. How many users have 1,000 or more measurements?

**Looking at the logs data - what is the number and percentage of the active user base who:**

6. Have logged blood glucose measurements?
7. Have at least 2 types of measurements?
8. Have all 3 measures - blood glucose, weight and blood pressure?

**For users that have blood pressure measurements:**

9. What is the median systolic/diastolic blood pressure values?

## 1. How many unique users exist in the logs dataset?

```sql
SELECT
  COUNT DISTINCT user_id
FROM health.user_logs;
```

```sql
-- Debugged Query
SELECT
  COUNT(DISTINCT id)
FROM health.user_logs;
```

#### NOTE:

For questions 2-8 we created a temporary table

```sql
DROP TABLE IF EXISTS user_measure_count;
CREATE TEMP TABLE user_measure_cout
SELECT
    id,
    COUNT(*) AS measure_count,
    COUNT(DISTINCT measure) as unique_measures
  FROM health.user_logs
  GROUP BY 1;
```

```sql
-- Debugged Query
DROP TABLE IF EXISTS user_measure_count;
CREATE TEMP TABLE user_measure_count As
SELECT
  id,
  COUNT(*) AS measure_count,
  COUNT(DISTINCT measure) as unique_measures
FROM
  health.user_logs
GROUP BY
  1;

```

## 2. How many total measurements do we have per user on average?

```sql
SELECT
  ROUND(MEAN(measure_count))
FROM user_measure_count;
```

```sql
-- Debugged Query
SELECT
  ROUND(Avg(measure_count))
FROM user_measure_count;
```

## 3. What about the median number of measurements per user?

```sql
SELECT
  PERCENTILE_CONTINUOUS(0.5) WITHIN GROUP (ORDER BY id) AS median_value
FROM user_measure_count;
```

```sql
-- Debugged Query
SELECT
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY measure_count) AS median_value
FROM user_measure_count;
```

## 4. How many users have 3 or more measurements?

```sql
SELECT
  COUNT(*)
FROM user_measure_count
HAVING measure >= 3;
```

```sql
-- Debugged Query
SELECT
  COUNT(*)
FROM user_measure_count
Where measure_count >= 3;
```

## 5. How many users have 1,000 or more measurements?

```sql
SELECT
  SUM(id)
FROM user_measure_count
WHERE measure_count >= 1000;
```

```sql
-- Debugged Query
SELECT
  COUNT(id)
FROM user_measure_count
WHERE measure_count >= 1000;
```

## 6. Have logged blood glucose measurements?

```sql
SELECT
  COUNT DISTINCT id
FROM health.user_logs
WHERE measure is 'blood_sugar';
```

```sql
-- Debugged Query
SELECT
 COUNT(DISTINCT id)
FROM health.user_logs
WHERE measure = 'blood_glucose';
```

## 7. Have at least 2 types of measurements?

```sql
SELECT
  COUNT(*)
FROM user_measure_count
WHERE COUNT(DISTINCT measures) >= 2;
```

```sql
-- Debugged Query
SELECT
  COUNT(*)
FROM user_measure_count
WHERE unique_measures >= 2;
```

## 8. Have all 3 measures - blood glucose, weight and blood pressure?

```sql
SELECT
  COUNT(*)
FROM usr_measure_count
WHERE unique_measures = 3;
```

```sql
-- Debugged Query
SELECT
  COUNT(*)
FROM user_measure_count
WHERE unique_measures = 3;
```

## 9. What is the median systolic/diastolic blood pressure values?

```sql
SELECT
  PERCENTILE_CONT(0.5) WITHIN (ORDER BY systolic) AS median_systolic
  PERCENTILE_CONT(0.5) WITHIN (ORDER BY diastolic) AS median_diastolic
FROM health.user_logs
WHERE measure is blood_pressure;
```

```sql
-- Debugged Query
SELECT
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY systolic) AS median_systolic,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY diastolic) AS median_diastolic
FROM health.user_logs
WHERE measure = 'blood_pressure';
```
