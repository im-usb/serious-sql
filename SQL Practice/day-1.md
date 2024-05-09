## 1. Histogram of Tweets [Twitter SQL Interview Question]

> Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket. In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

### tweets Table:

| Column Name | Type      |
| ----------- | --------- |
| tweet_id    | integer   |
| user_id     | integer   |
| msg         | string    |
| tweet_date  | timestamp |

### tweets Example Input:

| tweet_id | user_id | msg                                                               | tweet_date          |
| -------- | ------- | ----------------------------------------------------------------- | ------------------- |
| 214252   | 111     | Am considering taking Tesla private at $420. Funding secured.     | 12/30/2021 00:00:00 |
| 739252   | 111     | Despite the constant negative press covfefe                       | 01/01/2022 00:00:00 |
| 846402   | 111     | Following @NickSinghTech on Twitter changed my life!              | 02/14/2022 00:00:00 |
| 241425   | 254     | If the salary is so competitive why won’t you tell me what it is? | 03/01/2022 00:00:00 |
| 231574   | 148     | I no longer have a manager. I can't be managed                    | 03/23/2022 00:00:00 |

**Solution:**

```sql
WITH tweets_per_user As (
SELECT user_id, count(*) as frequency FROM tweets
WHERE EXTRACT(YEAR FROM tweet_date) = 2022
GROUP BY user_id )
SELECT frequency as tweet_bucket, COUNT(*) as users_num FROM tweets_per_user
GROUP BY frequency
ORDER BY frequency
```

## 2. Data Science Skills [LinkedIn SQL Interview Question]

> Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL. Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

Assumption:

- There are no duplicates in the candidates table.

### candidates Table:

| Column Name  | Type    |
| ------------ | ------- |
| candidate_id | integer |
| skill        | varchar |

### candidates Example Input:

| candidate_id | skill      |
| ------------ | ---------- |
| 123          | Python     |
| 123          | Tableau    |
| 123          | PostgreSQL |
| 234          | R          |
| 234          | PowerBI    |
| 234          | SQL Server |
| 345          | Python     |
| 345          | Tableau    |

### Example Output:

| candidate_id |
| ------------ |
| 123          |

**Solutions:**

```sql
WITH skill_table AS (
    SELECT candidate_id, COUNT(*) AS skill_count
    FROM candidates
    WHERE skill IN ('Python', 'PostgreSQL', 'Tableau')
    GROUP BY candidate_id
)
SELECT candidate_id
FROM skill_table
WHERE skill_count = 3;
```

## 3. Page With No Likes [Facebook SQL Interview Question]

> Assume you're given two tables containing data about Facebook Pages and their respective likes (as in "Like a Facebook Page"). Write a query to return the IDs of the Facebook pages that have zero likes. The output should be sorted in ascending order based on the page IDs.

### pages Table:

| Column Name | Type    |
| ----------- | ------- |
| page_id     | integer |
| page_name   | varchar |

### pages Example Input:

| page_id | page_name              |
| ------- | ---------------------- |
| 20001   | SQL Solutions          |
| 20045   | Brain Exercises        |
| 20701   | Tips for Data Analysts |

### page_likes Table:

| Column Name | Type     |
| ----------- | -------- |
| user_id     | integer  |
| page_id     | integer  |
| liked_date  | datetime |

### page_likes Example Input:

| user_id | page_id | liked_date          |
| ------- | ------- | ------------------- |
| 111     | 20001   | 04/08/2022 00:00:00 |
| 121     | 20045   | 03/12/2022 00:00:00 |
| 156     | 20001   | 07/25/2022 00:00:00 |

### Example Output:

| page_id |
| ------- |
| 20701   |

**Solutions:**

Method #1:

```sql
SELECT page_id
FROM pages
WHERE page_id NOT IN (
    SELECT page_id
    FROM page_likes
);
```

Method #2:

```sql
SELECT page_id
FROM pages
WHERE NOT EXISTS (
  SELECT page_id
  FROM page_likes AS likes
  WHERE likes.page_id = pages.page_id
;);
```

## 4. Unfinished Parts [Tesla SQL Interview Question]

> Tesla is investigating production bottlenecks and they need your help to extract the relevant data. Write a query to determine which parts have begun the assembly process but are not yet finished.

Assumptions:

- parts_assembly table contains all parts currently in production, each at varying stages of the assembly process.
- An unfinished part is one that lacks a finish_date.

### parts_assembly Table

| Column Name   | Type     |
| ------------- | -------- |
| part          | string   |
| finish_date   | datetime |
| assembly_step | integer  |

### parts_assembly Example Input:

| part    | finish_date         | assembly_step |
| ------- | ------------------- | ------------- |
| battery | 01/22/2022 00:00:00 | 1             |
| battery | 02/22/2022 00:00:00 | 2             |
| battery | 03/22/2022 00:00:00 | 3             |
| bumper  | 01/22/2022 00:00:00 | 1             |
| bumper  | 02/22/2022 00:00:00 | 2             |
| bumper  |                     | 3             |
| bumper  |                     | 4             |

### Example Output

| part   | assembly_step |
| ------ | ------------- |
| bumper | 3             |
| bumper | 4             |

**Solutions:**

```sql
SELECT part, assembly_step
FROM parts_assembly
WHERE assembly_step > 0
AND finish_date IS NULL;
```

## 5. Laptop vs. Mobile Viewership [New York Times SQL Interview Question]

> Assume you're given the table on user viewership categorised by device type where the three types are laptop, tablet, and phone. Write a query that calculates the total viewership for laptops and mobile devices where mobile is defined as the sum of tablet and phone viewership. Output the total viewership for laptops as laptop_reviews and the total viewership for mobile devices as mobile_views.

### parts_assembly Table

### parts_assembly Example Input:

### Example Output

**Solutions:**

```sql
SELECT (
SELECT count(*) from viewership
where device_type = 'laptop'
) as laptop_views,
(
SELECT count(*)  from viewership
where device_type in ('phone','tablet')
) as mobile_views
```
