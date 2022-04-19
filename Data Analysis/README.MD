# Data Analysis

Based on the ERD above, the key columns we will need to generate each customer insight for answering questions are:
  * **```category_name```**: The name of the top 2 ranking categories
  * **```rental_count```**: How many total films have they watched in this category?
  * **```average_comparison```**: How many more films has the customer watched compared to the average DVD Rental Co customer?
  * **```percentile```**: How does the customer rank in terms of the top X% compared to all other customers in this film category?
  * **```category_percentage```**: What proportion of each customer’s total films watched does this count make?

## Identifying Start & End Points

In order to generate results required to calculate **```rental_count```** at a **```customer_id```** level, the following information are needed:
  * **```customer_id```**
  * **```category_name```**
  * 
First we realise that we need to start with the ```dvd_rentals.rental``` table as it is the only place where our customer_id field exists - it’s the only place where we can identify how many films a customer has watched.

The end is to  link the records in the ```dvd_rentals.rental``` table to the ```dvd_rentals.category``` table to obtain ```category_name```.

### Mapping the join journey
Starting with our ```dvd_rentals.rental``` table we can see that we do indeed have the ```customer_id``` as well as addition columns - but there is no ```category_name``` in sight, in fact we are very far away!

After inspecting the ERD - we need to get from table 1 ```dvd_rentals.rental``` all the way through to table number 5 ```dvd_rentals.category``` using the blue lines. 

Below is the final 4 part table joining journey itinerary:

|Join Journey Part|Start              |End                |Foreign Key       |
|-----------------|-------------------|-------------------|------------------|
|**Part 1**       |```rental```       |```inventory```    |```inventory_id```|
|**Part 2**       |```inventory```    |```film```         |```film_id```     |
|**Part 3**       |```film```         |```film_category```|```film_id```     |
|**Part 4**       |```film_category```|```category```     |```category_id``` |

For part 1 of our table joining journey - we can see that we need to start with the rental table and end at the inventory table using the foreign key inventory_id. However, we realise that we need to answer the 3 key questions below. 

1. [What is **the purpose** of joining these two tables?](#question-1)
2. [What is **the distribution of foreign keys** within each table?](#question-2)
3. [How many **unique foreign key values** exist in each table?](#question-3)

## Question 1
We need to generate the rental_count calculation - the number of films that a customer has watched in a specific category.
From the table join journey, we have the ```rental table``` which consists of the important ```customer_id``` data point but we also know that this rental table also has the actual number of films that each customer has watched.

However there is a catch with our ```dvd_rentals.rental``` table - all the table records are not yet tracked at the ```film_id``` level - they only have the ```inventory_id``` which is recorded for each customer’s rental record.

When we look through the other tables in the ERD - we can notice the ```inventory``` table which can be used to help us get the ```film_id``` column for each rental.

Thus to answer Question 1, **we need to keep all of the customer rental records from ```dvd_rentals.rental``` and match up each record with its equivalent ```film_id``` value from the ```dvd_rentals.inventory``` table.** 

We can think of the deeper purpose of this join between the two tables in the following way:

> We need to keep all of the customer rental records from dvd_rentals.rental and match up each record with its equivalent film_id value from the dvd_rentals.inventory table.

Now we need to decide whether to use a left or inner join?

Now here is where things could get tricky, as there are a few unknowns that we need to address as we are matching the inventory_id foreign key between the rental and inventory tables.

1. How many records exist per inventory_id value in rental or inventory tables?
2. How many overlapping and missing unique foreign key values are there between the two tables?

The same questions can be used for all scenarios and is not just limited to this specific table join!

> 1. How many records exist per foreign key value in left and right tables?
> 2. How many overlapping and missing unique foreign key values are there between the two tables?

Since we know that the ```rental``` table contains every single rental for each customer - it makes sense logically that each valid rental record in the rental table should have a relevant ```inventory_id``` record as people need to physically hire some item in the store.

Additionally - it also makes sense that a specific item might be rented out by multiple customers at different times as customers return the DVD as shown by the ```return_date``` column in the dataset.

Now when we think about the ```inventory``` table - it should follow that every item should have a unique ```inventory_id``` but there may also be multiple copies of a specific film.

From these 2 key pieces of real life insight - we can generate some hypotheses about our 2 datasets.

1. The number of unique ```inventory_id``` records will be equal in both ```dvd_rentals.rental``` and ```dvd_rentals.inventory``` tables
2. There will be a multiple records per unique ```inventory_id``` in the ```dvd_rentals.rental``` table
3. There will be multiple ```inventory_id``` records per unique ```film_id``` value in the ```dvd_rentals.inventory``` table

Let’s start using SQL to validate those 3 hypotheses.

> H1: The number of unique ```inventory_id``` records will be equal in both ```dvd_rentals.rental``` and ```dvd_rentals.inventory``` tables

We can quickly use distinct counts on inventory_id for both tables to quickly validate this assumption.

```sql
(
SELECT
  'rental table' AS table_name,
  COUNT(DISTINCT inventory_id)
FROM dvd_rentals.rental
)
UNION
(
SELECT
  'inventory table' AS table_name,
  COUNT(DISTINCT inventory_id)
FROM dvd_rentals.inventory
);
```

|table_name|count|
|----------|-----|
|inventory table|4581 |
|rental table|4580 |

**FINDING**: 

There seems to be 1 additional **```inventory_id```** value in the **```dvd_rentals.inventory```** table compared to the **```dvd_rentals.rental```** table

This warrants further investigation but it seems to invalidate our first hypothesis - which is exactly what we are after!

> H2: There will be a multiple records per unique ```inventory_id``` in the ```dvd_rentals.rental``` table

A simple group by count with an additional summary group by will do to check this hypothesis.

We follow these 2 simple steps to summarise our dataset:

1. Perform a ```GROUP BY``` record count on the target column
2. Summarise the record count output to show the distribution of records by unique count of the target column

The target column in this case is our ```inventory_id``` field.

```sql
-- first generate group by counts on the target_column_values column
WITH counts_base AS (
SELECT
  inventory_id AS target_column_values,
  COUNT(*) AS row_counts
FROM dvd_rentals.rental
GROUP BY target_column_values
)
-- summarize the group by counts above by grouping again on the row_counts from counts_base CTE part
SELECT
  row_counts,
  COUNT(target_column_values) as count_of_target_values
FROM counts_base
GROUP BY row_counts
ORDER BY row_counts;
```

|row_counts|count_of_target_values|
|----------|----------------------|
|1         |4                     |
|2         |1126                  |
|3         |1151                  |
|4         |1160                  |
|5         |1139                  |

**Findings**

We can indeed confirm that there are multiple rows per inventory_id value in our dvd_rentals.rental table.

> H3: There will be multiple ```inventory_id``` records per unique ```film_id``` value in the ```dvd_rentals.inventory``` table

We can use this same approach as hypothesis 2 but instead of groupinng on the ```inventory_id``` - we will instead group on the ```film_id``` column and perform a count distinct on the ```inventory_id``` before summarizing the outputs. 

```sql
-- first generate group by counts on the target_column_values column
WITH counts_base AS (
SELECT
  film_id AS target_column_values,
  COUNT(DISTINCT inventory_id) AS unique_record_counts
FROM dvd_rentals.inventory
GROUP BY target_column_values
)
-- summarize the group by counts above by grouping again on the row_counts from counts_base CTE part
SELECT
  unique_record_counts,
  COUNT(target_column_values) AS count_of_target_values
FROM counts_base
GROUP BY unique_record_counts
ORDER BY unique_record_counts;
```

|row_counts|count_of_target_values|
|----------|----------------------|
|2         |133                   |
|3         |131                   |
|4         |183                   |
|5         |136                   |
|6         |187                   |
|7         |116                   |
|8         |72                    |

**Findings**

We can confirm that there are indeed multiple unique ```inventory_id``` per ```film_id``` value in the ```dvd_rentals.inventory``` table.

## Question 2

> What is **the distribution of foreign keys** within each table?

One of the first places to start inspecting our datasets is to look at the distribution of foreign key values in each rental and inventory table used for our join.

The distribution and relationship within the table by the foreign keys is super important because it helps us inspect what our table joining inputs consist of and also determines what sort of outputs we should expect after joining.

```rental``` distribution analysis on ```inventory_id``` foreign key

```sql
-- first generate group by counts on the foreign_key_values column
WITH counts_base AS (
SELECT
  inventory_id AS foreign_key_values,
  COUNT(*) AS row_counts
FROM dvd_rentals.rental
GROUP BY foreign_key_values
)
-- summarize the group by counts above by grouping again on the row_counts from counts_base CTE part
SELECT
  row_counts,
  COUNT(foreign_key_values) as count_of_foreign_keys
FROM counts_base
GROUP BY row_counts
ORDER BY row_counts;
```

|row_counts|count_of_foreign_keys|
|----------|---------------------|
|1         |4                    |
|2         |1126                 |
|3         |1151                 |
|4         |1160                 |
|5         |1139                 |

```inventory``` distribution analysis on ```inventory_id``` foreign key

```sql
WITH counts_base AS (
SELECT
  inventory_id AS foreign_key_values,
  COUNT(*) AS row_counts
FROM dvd_rentals.inventory
GROUP BY foreign_key_values
)
SELECT
  row_counts,
  COUNT(foreign_key_values) as count_of_foreign_keys
FROM counts_base
GROUP BY row_counts
ORDER BY row_counts;
```

|row_counts|count_of_foreign_keys|
|----------|---------------------|
|1         |4581                 |

**Findings**

We can see in the rental table - there exists different multiple row counts for some values of the foreign keys - this is not unexpected, in fact we did exactly the same analysis previously to validate our first data hypothesis!

To break it down - we can see that 4 of our foreign key values (the ```inventory_id``` in this case) will have only a single row in the rental_table.

Additionally in the rental table - there are 1,126 ```inventory_id``` values which exist in 2 different rows and 1,151 in 3 different rows and so on.

This can also be referred to as a a 1-to-many relationship for the ```inventory_id``` in this rental table, or in other words - there may exist 1 or more record for each unique ```inventory_id``` value in this table.

## Question 3

> How many **unique foreign key values** exist in each table?

Since the ```dvd_rentals.rental``` table is the only table in our ERD which has ```customer_id``` as a column - we would like to keep all of the records from this table. 

We will employ the anti join or a WHERE NOT EXISTS to obtain the following foreign key details:

1. Which foreign keys only exist in the left table?
2. Which foreign keys only exist in the right table?

Firstly - let’s count how many unique keys are in both (1) and (2):

```sql
-- how many foreign keys only exist in the left table and not in the right?
SELECT
  COUNT(DISTINCT rental.inventory_id)
FROM dvd_rentals.rental
WHERE NOT EXISTS (
  SELECT inventory_id
  FROM dvd_rentals.inventory
  WHERE rental.inventory_id = inventory.inventory_id
);
```

|count|
|-----|
|0    | 

Great we can confirm that there are no inventory_id records which appear in the dvd_rentals.rental table which does not appear in the dvd_rentals.inventory table.

Now onto the right side table:

```sql
-- how many foreign keys only exist in the right table and not in the left?
-- note the table reference changes
SELECT
  COUNT(DISTINCT inventory.inventory_id)
FROM dvd_rentals.inventory
WHERE NOT EXISTS (
  SELECT inventory_id
  FROM dvd_rentals.rental
  WHERE rental.inventory_id = inventory.inventory_id
);
```

|count|
|-----|
|1    | 

Ok - we’ve spotted a single inventory_id record. Let’s inspect further:

```sql
SELECT *
FROM dvd_rentals.inventory
WHERE NOT EXISTS (
  SELECT inventory_id
  FROM dvd_rentals.rental
  WHERE rental.inventory_id = inventory.inventory_id
);
```

|inventory_id|film_id|store_id|last_update             |
|------------|-------|--------|------------------------|
|5           |1      |2       |2006-02-15T05:09:17.000Z|

This is linked to a specific film record which could be rented out by a customer. One such reason for this odd record could be that this specific rental inventory unit was never rented out by a customer. 

**Joint Foreign Keys**

Since we already identified that 0 of the foreign key values in the ```dvd_rentals.rental``` do not exist in the ```dvd_rentals.inventory``` table - or in other words - all of the ```inventory_id``` values which exist in ```dvd_rentals.rental``` table also exists in the ```dvd_rentals.inventory``` dataset - we can now redraw our venn diagram from before with a representation of what our exact data looks like.

<p align="center">
    <img src="https://github.com/eluah/marketing-analytics-case-study/blob/main/img/Venn-Diagram.png" alt=venn>
</p> 

We can quickly perform a left semi join or a WHERE EXISTS to get the count of unique foreign key values that are in the intersection.

```sql
SELECT
  COUNT(DISTINCT rental.inventory_id)
FROM dvd_rentals.rental
WHERE EXISTS (
  SELECT inventory_id
  FROM dvd_rentals.inventory
  WHERE rental.inventory_id = inventory.inventory_id
);
```

|count|
|-----|
|4580 |

**Implementing the Join(s)**

After performing this analysis we can conclude there is in fact no difference between running a LEFT JOIN or an INNER JOIN in our example!

We can finally implement our joins and prove this is the case by inspecting the raw row counts from the resulting join outputs.

Let’s also confirm that the unique inventory_id records are the same too.

```sql
DROP TABLE IF EXISTS left_rental_join;
CREATE TEMP TABLE left_rental_join AS
SELECT
  rental.customer_id,
  rental.inventory_id,
  inventory.film_id
FROM dvd_rentals.rental
LEFT JOIN dvd_rentals.inventory
  ON rental.inventory_id = inventory.inventory_id;

DROP TABLE IF EXISTS inner_rental_join;
CREATE TEMP TABLE inner_rental_join AS
SELECT
  rental.customer_id,
  rental.inventory_id,
  inventory.film_id
FROM dvd_rentals.rental
INNER JOIN dvd_rentals.inventory
  ON rental.inventory_id = inventory.inventory_id;

-- check the counts for each output (bonus UNION usage)
-- note that these parantheses are not really required but it makes
-- the code look and read a bit nicer!
(
  SELECT
    'left join' AS join_type,
    COUNT(*) AS record_count,
    COUNT(DISTINCT inventory_id) AS unique_key_values
  FROM left_rental_join
)
UNION
(
  SELECT
    'inner join' AS join_type,
    COUNT(*) AS record_count,
    COUNT(DISTINCT inventory_id) AS unique_key_values
  FROM inner_rental_join
);
```

|join_type|record_count|unique_key_values|
|---------|------------|-----------------|
|inner join|16044       |4580             |
|left join|16044       |4580             |

Great! In this example - we can indeed confirm that there is no difference between an inner join or left join for our datasets!
