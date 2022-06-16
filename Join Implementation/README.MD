# Join Implementation

Next I look at the table joining journey. 

| Join Journey Part | Start               |  End                |  Foreign Key       |
|-------------------|---------------------|---------------------|--------------------|
| Part 1            | ```rental```        | ```inventory```     | ```inventory_id``` |
| Part 2            | ```inventory```     | ```film```          | ```film_id```      |
| Part 3            | ```film```          | ```film_category``` | ```film_id```      |
| Part 4            | ```film_category``` | ```category```      | ```category_id```  |

## Table Joining Checklist for Part 2
1. What is the purpose of joining these two tables?
2. What contextual hypotheses do we have about the data?
3. How can we validate these assumptions?
4. What is the distribution of foreign keys within each table?
5. How many unique foreign key values exist in each table?

Let's go through the checklist one by one.

> 1. What is the purpose of joining these two tables?

We want to match the films on ```film_id``` to obtain the title of each film.

> 2. What contextual hypotheses do we have about the data?

a. There be 1-to-many relationship for ```film_id``` and the rows of the ```dvd_rentals.inventory``` table as one specific film might have multiple copies to be purchased at the rental store.

b. There should be 1-to-1 relationship for ```film_id``` and the rows of the ```dvd_rentals.film``` table as it doesn’t make sense for there to be duplicates in this ```dvd_rentals.film```.

> 3. How can we validate these assumptions?

Now we test the assumptions, by generating the row counts for ```film_id``` for both the ```dvd_rentals.inventory``` and ```dvd_rentals.film``` tables

Let’s use a summarized group by with a CTE for the dvd_rentals.inventory table:

```sql
WITH base_counts AS (
  SELECT 
    film_id,
    COUNT(*) AS record_count
  FROM dvd_rentals.inventory
  GROUP BY 1
)

SELECT 
  record_count,
  COUNT(DISTINCT film_id) AS unique_film_id_values
FROM base_counts
GROUP BY record_count
ORDER BY record_count;
```

*Output:*

| record_count | unique_film_id_values |
|--------------|-----------------------|
| 2            | 133                   |
| 3            | 131                   |
| 4            | 183                   |
| 5            | 136                   |
| 6            | 187                   |
| 7            | 116                   |
| 8            | 72                    |

The first assumption is true as we have a 1-to-many relationship for the ```film_id``` foreign key in our ```dvd_rentals.inventory_table```. 

Next we check our second assumption, we can just do a group by on the ```film_id``` and sort it in descending order to check if there's just one row per unique ```film_id``` or not.

```sql
SELECT
  film_id,
  COUNT(*) AS film_count
FROM dvd_rentals.film
GROUP BY film_id
ORDER BY film_id DESC
LIMIT 5;
```

*Output:*

| film_id | film_count |
|---------|------------|
| 1000    | 1          |
| 999     | 1          |
| 998     | 1          |
| 997     | 1          |
| 996     | 1          |

Thus, we can confirm that there is one-to-one relationship for ```film_id``` in the ```film``` table.

> 4. What is the distribution of foreign keys within each table?

We already did this with our previouis summarized group by count when confirming our hypotheses for both tables.

> 5. How many unique foreign key values exist in each table?

We will use our same anti join approach to figure out the overlaps and exlusions as per the venn diagram previously.

```sql
SELECT
  COUNT(DISTINCT inventory.film_id)
FROM dvd_rentals.inventory
WHERE NOT EXISTS (
  SELECT film_id
  FROM dvd_rentals.film
  WHERE film.film_id = inventory.film_id
);
```

*Output:*

| total_count |
|-------------|
| 0           |

We can conclude that all of the ```film_id``` records from the ```dvd_rentals.inventory``` table exists in the ```dvd_rentals.film``` table.

Let’s do the same for the ```film``` table.

```sql
SELECT
  COUNT(DISTINCT film.film_id)
FROM dvd_rentals.film
WHERE NOT EXISTS (
  SELECT film_id
  FROM dvd_rentals.inventory
  WHERE film.film_id = inventory.film_id
);
```

*Output:*

| total_count |
|-------------|
| 42          |

Alright, so there is some discrepancy in the number of unique ```film_id``` values between the inventory & film table.

Finally - let’s check that total count of distinct foreign key values that will be generated when we use a left semi join on our ```dvd_rentals.inventory``` as our base left table.

```sql
SELECT
  COUNT(DISTINCT film_id)
FROM dvd_rentals.inventory
-- note how the NOT is no longer here for a left semi join
-- compared to the anti join!
WHERE EXISTS (
  SELECT film_id
  FROM dvd_rentals.film
  WHERE film.film_id = inventory.film_id
);
```

*Output:*

| total_count |
|-------------|
| 958         |

We will be expecting a total distinct count of ```film_id``` values of 958 once we perform the final join between our 2 tables.

## Join Implementation

Now that we know that we have all of the key values from the base table, our ```dvd_rentals.inventory``` table - we now need to do our join.

We will use an ```INNER JOIN``` but let's confirm that there is no difference between left or right joins with some summaries.

```sql
DROP TABLE IF EXISTS left_join_part_2;
CREATE TEMP TABLE left_join_part_2 AS
SELECT
  inventory.inventory_id,
  inventory.film_id,
  film.title
FROM dvd_rentals.inventory
LEFT JOIN dvd_rentals.film
  ON film.film_id = inventory.film_id;

DROP TABLE IF EXISTS inner_join_part_2;
CREATE TEMP TABLE inner_join_part_2 AS
SELECT
  inventory.inventory_id,
  inventory.film_id,
  film.title
FROM dvd_rentals.inventory
INNER JOIN dvd_rentals.film
  ON film.film_id = inventory.film_id;

-- check the counts for each output (bonus UNION usage)
-- note that these parantheses are not really required but it makes
-- the code look and read a bit nicer!
(
  SELECT
    'left join' AS join_type,
    COUNT(*) AS record_count,
    COUNT(DISTINCT film_id) AS unique_key_values
  FROM left_join_part_2
)
-- we can use UNION ALL here because we do not need UNION for distinct values!
UNION ALL
(
  SELECT
    'inner join' AS join_type,
    COUNT(*) AS record_count,
    COUNT(DISTINCT film_id) AS unique_key_values
  FROM inner_join_part_2
);
```

*Output:*

| join_type  | row_counts | unique_film_values |
|------------|------------|--------------------|
| inner join | 4581       | 958                |
| left join  | 4581       | 958                |

## Joining Part 1 and 2

Now we proceed to to do a 3 way join. 

```sql
DROP TABLE IF EXISTS join_part1_and_part2;
CREATE TEMP TABLE join_part1_and_part2 AS (
SELECT
  rental.customer_id,
  inventory.film_id,
  film.title
FROM dvd_rentals.rental
INNER JOIN dvd_rentals.inventory
  ON rental.inventory_id = inventory.inventory_id
INNER JOIN dvd_rentals.film
  ON inventory.film_id = film.film_id
);

SELECT * 
FROM join_part1_and_part2
ORDER BY customer_id
LIMIT 5;
```

*Output:*

| customer_id | film_id | title                |
|-------------|---------|----------------------|
| 1           | 308     | FERRIS MOTHER        |
| 1           | 243     | DOORS PRESIDENT      |
| 1           | 924     | UNFORGIVEN ZOOLANDER |
| 1           | 480     | JEEPERS WEDDING      |
| 1           | 611     | MUSKETEERS WAIT      |

## Joining Part 3 & 4

