# Record Counts and Distinct Values

### Schema Name: dvd_rentals

### Tables Covered:

1. **film_list**: Contains detailed information about films available for rental, including titles, ratings, categories, and prices.
2. **film_actor**: Likely a junction table linking films to actors, indicating which actors appear in which films.
3. **nicer_but_slower_film_list**: A variant of the film_list table, possibly optimized for certain operations or queries.
4. **city**: Stores data about cities, possibly related to store locations or customer addresses.
5. **sales_by_film_category**: Provides sales data categorized by film category, essential for analyzing sales trends.
6. **payment**: Contains data related to payments made for DVD rentals, including payment dates and amounts.
7. **rental**: Likely stores rental transactions, including customer IDs, inventory IDs, and rental dates.

### Summary:

These tables cover various aspects of a DVD rental business, including film details, sales data, payments, rental transactions, and geographical information. Analyzing data from these tables can provide valuable insights into customer preferences, sales performance, inventory management, and geographical distribution of rentals.

## 1. How Many Records

> How many rows are there in the film_list table?

```sql
SELECT
  COUNT(*) AS row_count
FROM dvd_rentals.film_list;
```

Output:
| row_count |
|-----------|
| 997 |

### 1.1 Column Aliases

> How does the output differ when you remove the alias component from the above query?

```sql
SELECT COUNT(*)
FROM dvd_rentals.film_list;
```

Output without Alias:
|count |
|------|
| 997 |

```sql
SELECT
  COUNT(*) AS row_count
FROM dvd_rentals.film_list;
```

Output with Alias:
| row_count |
|-----------|
| 997 |

## 2. DISTINCT For Unique Values

### 2.1 Show Unique Column Values

> What are the unique values for the rating column in the film table?

```sql
SELECT DISTINCT
  rating
FROM dvd_rentals.film_list;
```

Output:
| rating |
|-----------|
| PG |
| NC-17 |
| PG-13 |
| G |
| R |

### 2.2 Count of Unique Values

> How many unique category values are there in the film_list table?

```sql
Select count
  (distinct category) as unique_film_category
from dvd_rentals.film_list
```

Output:
| unique_film_category |
|-----------|
| 16 |

## 3. Group By Counts

> What is the frequency of values in the rating column in the film_list table?

```sql
SELECT
    rating,
    COUNT(rating) AS frequency
FROM
    dvd_rentals.film_list
GROUP BY
    rating;
```

Output:
| rating | frequency |
|--------|----------|
| PG | 233 |
| NC-17 | 194 |
| PG-13 | 177 |
| G | 193 |
| R | 210 |

Group by query using common table expression

```sql
WITH example_table AS (
  SELECT
    fid,
    title,
    category,
    rating,
    price
  FROM dvd_rentals.film_list
  LIMIT 10
)
SELECT
  rating,
  COUNT(*) as record_count
FROM example_table
GROUP BY rating
ORDER BY record_count DESC;
```

Output:
| rating | record_count |
|--------|--------------|
| PG-13 | 4 |
| G | 2 |
| NC-17 | 2 |
| PG | 1 |
| R | 1 |

### 3.1 Adding a Percentage Column

```sql
select
  rating,
  count(*) as frequency,
  Round(
    100 * count(*) :: NUMERIC / SUM(Count(*)) over (),
    2
  ) as percent
from
  dvd_rentals.film_list
group by
  rating
```

Output:
| rating | frequency | percentage |
|--------|-----------|------------|
| PG-13 | 223 | 22.37 |
| NC-17 | 210 | 21.06 |
| PG | 194 | 19.46 |
| R | 193 | 19.36 |
| G | 177 | 17.75 |

## 4. Counts For Multiple Column Combinations

> What are the 5 most frequent rating and category combinations in the film_list table?

```sql
SELECT
    rating,
    category,
    COUNT(*) AS frequency
FROM
    dvd_rentals.film_list
GROUP BY
    category,
    rating
ORDER BY
    frequency DESC
LIMIT
    5;

```

Output:
| rating | category | frequency |
|--------|-----------|-----------|
| PG-13 | Drama | 22 |
| NC-17 | Music | 20 |
| PG-13 | Animation | 19 |
| PG-13 | Foreign | 19 |
| NC-17 | New | 18 |

### 4.1 Using Positional Numbers Instead of Column Names

Similar output can be achieved using positional numbers instead of column names

```sql
SELECT
  rating,
  category,
  COUNT(*) AS frequency
FROM
  dvd_rentals.film_list
GROUP BY
  1,
  2
order by
  frequency desc;
```

## 5. Exercises

> Which actor_id has the most number of unique film_id records in the dvd_rentals.film_actor table?

**Solution:**

```sql
SELECT
    actor_id,
    COUNT(DISTINCT film_id) AS unique_films
FROM
    dvd_rentals.film_actor
GROUP BY
    actor_id
ORDER BY
    unique_films DESC
LIMIT
    5;
```

> How many distinct fid values are there for the 3rd most common price value in the dvd_rentals.nicer_but_slower_film_list table?

**Solution:**

```sql
Select
  count(distinct fid) as count,
  price
from
  dvd_rentals.nicer_but_slower_film_list
group by
  price
order by
  price
limit
  1;
```

> How many unique country_id values exist in the dvd_rentals.city table?

**Solution:**

```sql
Select
  count(distinct country_id) as countries
from
  dvd_rentals.city;
```

> What percentage of overall total_sales does the Sports category make up in the dvd_rentals.sales_by_film_category table?

**Solution:**

```sql
SELECT
    category,
    ROUND(
        100 * total_sales::numeric / SUM(total_sales) OVER(),
        2
    ) AS percent_sales
FROM
    dvd_rentals.sales_by_film_category;

```

> What percentage of unique fid values are in the Children category in the dvd_rentals.film_list table?

**Solution:**

```sql
Select
  category,
  round(
    100 * count(distinct fid) :: numeric / sum(count(fid)) over (),
    2
  ) as unique_fids
from
  dvd_rentals.film_list
group by
  category
```
