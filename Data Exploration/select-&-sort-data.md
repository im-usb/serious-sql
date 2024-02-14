# Select & Sort Data

## 1. How To Query Data

### 1.1 Select All Columns

> Show all records from the language table from the dvd_rentals schema

```sql
SELECT * FROM dvd_rentals.languages;
```

### 1.2 Select Specific Columns

> Show only the language_id and name columns from the language table

```sql
SELECT
    language_id,
    name
FROM dvd_rentals.languages;
```

### 1.3 Limit Output Rows

> Show the first 10 rows from the actor tables

```sql
SELECT *
FROM dvd_rentals.actor
LIMIT 10;
```

## 2. Sorting Query Results

### 2.1 Sort by Text Column

> What are the first 5 values in the country column from the country table by alphabetical order?

```sql
SELECT country
FROM dvd_rentals.country
ORDER BY country
LIMIT 5;
```

### 2.2 Sort by Numeric/Date Column

> What are the 5 lowest total_sales values in the sales_by_film_category table?

```sql
SELECT total_sales
FROM dvd_rentals.sales_by_film_category
ORDER BY total_sales
LIMIT 5;
```

OR

```sql
SELECT total_sales
FROM dvd_rentals.sales_by_film_category
ORDER BY 1
LIMIT 5;
```

### 2.3 Sort by Descending

> What are the first 5 values in reverse alphabetical order in the country column from the country table?

```sql
SELECT country
FROM dvd_rentals.country
ORDER BY 1 DESC
LIMIT 5;
```

> Which category had the lowest total_sales value according to the sales_by_film_category table? What was the total_sales value?

```sql
SELECT category, total_sales
FROM dvd_rentals.sales_by_film_category
ORDER BY total_sales
LIMIT 1;
```

> What was the latest payment_date of all dvd rentals in the payment table?

```sql
SELECT payment_date
FROM dvd_rentals.payment
ORDER BY payment_date DESC
LIMIT 1;
```

### 2.4 Sort by Multiple Columns

> Which customer_id had the latest rental_date for inventory_id = 1 and 2?

```sql
SELECT customer_id,inventory_id, rental_date
FROM dvd_rentals.rental
ORDER BY inventory_id, rental_date DESC
LIMIT 8;
```

> In the dvd_rentals.sales_by_film_category table, which category has the highest total_sales?

```sql
SELECT category, total_sales
FROM dvd_rentals.sales_by_film_category
ORDER BY total_sales DESC, category
LIMIT 5;
```

## 3. Exercises

> 1. What is the name of the category with the highest category_id in the dvd_rentals.category table?

```sql
SELECT category_id, name
FROM dvd_rentals.category
ORDER BY category_id DESC;
```

> 2. For the films with the longest length, what is the title of the “R” rated film with the lowest replacement_cost in dvd_rentals.film table?

```sql
SELECT title, rating, length, replacement_cost
FROM dvd_rentals.film
WHERE rating = 'R'
ORDER BY length DESC, replacement_cost
LIMIT 5;
```

> 3. Who was the manager of the store with the highest total_sales in the dvd_rentals.sales_by_store table?

```sql
SELECT manager, total_sales
FROM dvd_rentals.sales_by_store
ORDER BY total_sales DESC;
```

> 4. What is the postal_code of the city with the 5th highest city_id in the dvd_rentals.address table?

```sql
SELECT city_id, postal_code
FROM dvd_rentals.address
ORDER BY city_id DESC
LIMIT 5;
```

OR

```sql
SELECT city_id, postal_code
FROM dvd_rentals.address
ORDER BY city_id DESC
LIMIT 1 OFFSET 4;
```
