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
