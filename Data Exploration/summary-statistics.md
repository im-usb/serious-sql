# Summary Statistics

## 1. Central Location Statitics

- By central location statistics here we are refering to Mean, Mode and Median values. They are all measure of central location summary often used in analytical reports.

### 1.1. Arithmetic Mean or Average

- Arithmetic mean is basically the sum of all values divided by the total count of values for a set of numbers.
- The mean is commonly used as a location summary statistic to show the central tendancy for a set of observations.

> SQL implementaton of mean is rather simpler as we use AVG funvtion to achieve it.

```sql
SELECT
  AVG(measure_value)
FROM health.user_logs;
```

Output:
| avg |
|--------------------|
| 1986.2288605267076 |

- By the looks of the mean value, something definitely seems off. Let's check the record counts for each measure

```sql
SELECT
  measure,
  COUNT(*) AS counts
FROM health.user_logs
GROUP BY measure
ORDER BY counts DESC;
```

Output:
| measure | counts |
|-----------------|--------|
| blood_glucose | 38692 |
| weight | 2782 |
| blood_pressure | 2417 |

- This definitely looks kinda fishy. Let's have a look at the AVG value across each measure too

```sql
SELECT
  measure,
  ROUND(AVG(measure_value), 2) AS average,
  COUNT(*) AS counts
FROM health.user_logs
GROUP BY measure
ORDER BY counts DESC;
```

Output:
| measure | average | counts |
|-----------------|----------|--------|
| blood_glucose | 177.35 | 38692 |
| **weight** | **28786.85** | 2782 |
| blood_pressure | 95.40 | 2417 |

- The mean weight seems very off. I'll keep this to the side and move onto the other topics for now.

## 2. Median & Mode

- These are two more central tendancy statistics that are different to the mean.

- The median shows the central location based off the values of the middle values, whilst the mode focuses on the most frequent values.

- The mode is simply calculated as the value that appears the most number of times

### 2.1. The Median Concept

#### Median Algorithm

- Firstly, all N observations of numbers are lined up from smallest to largest in a single line.

- To calculate the median we look at the number directly in the middle of this ordered line up.

- If N is odd, the middle number will be (N+1)/2.

- If N is even, N/2 and N/2 + 1 will be considered middle values. Taking the average of these two will give us the median value.

#### Mode Algorithm

- Calculate the tally of values similar to a GROUP BY and COUNT
- The mode is the values with the highest number of occurences

Example SQL Query:

```sql
WITH sample_data (example_values) AS (
 VALUES
 (82), (51), (144), (84), (120), (148), (148), (108), (160), (86)
)
SELECT
  AVG(example_values) AS mean_value,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY example_values) AS median_value,
  MODE() WITHIN GROUP (ORDER BY example_values) AS mode_value
from sample_data;
```

| mean_value           | median_value | mode_value |
| -------------------- | ------------ | ---------- |
| 113.1000000000000000 | 114          | 148        |

- If we use the same syntax to calculate our median and mode values for our health.user_logs

```sql
SELECT
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY measure_value) AS median_value,
  MODE() WITHIN GROUP (ORDER BY measure_value) AS mode_value,
  AVG(measure_value) as mean_value
FROM health.user_logs
WHERE measure = 'weight';
```

Output:

| median_value | mode_value  | mean_value         |
| ------------ | ----------- | ------------------ |
| 75.976721975 | 68.49244787 | 28786.846657296002 |

- Again, as highlighted earlier, there's something fishy about the results we can see over here.
