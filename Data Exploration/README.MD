# 1. Data Exploration

## 1.1 rental table

The first table in our database schema is the rental table. We'll look at the first 5 rows of the table.

```sql
SELECT * 
FROM dvd_rentals.rental
WHERE customer_id = 130;
LIMIT 5;
```

*Output:*

| rental_id | rental_date              | inventory_id | customer_id | return_date              | staff_id | last_update              |
|-----------|--------------------------|--------------|-------------|--------------------------|----------|--------------------------|
|1	2005-05-24 22:53:30	367	130	2005-05-26 22:04:30	1	2006-02-15 21:30:53
746	2005-05-29 09:25:10	4272	130	2005-06-02 04:20:10	2	2006-02-15 21:30:53
1630	2005-06-16 07:55:01	2413	130	2005-06-19 06:38:01	1	2006-02-15 21:30:53
1864	2005-06-17 01:39:47	1815	130	2005-06-24 19:39:47	2	2006-02-15 21:30:53
2163	2005-06-17 23:46:16	2600	130	2005-06-22 22:48:16	2	2006-02-15 21:30:53|

The rental table contains all the details of the films that were rented along with other details such as ```inventory_id```, ```customer_id``` along with ```rental & return date```.

```sql
SELECT 
  COUNT(*) 
FROM dvd_rentals.rental;
```

*Output:*

| count |
|-------|
| 16044 |

Let's see all the records for one customer.

```sql
SELECT *
FROM dvd_rentals.rental
WHERE customer_id = 5
LIMIT 5;
```

*Output:*

| rental_id | rental_date              | inventory_id | customer_id | return_date              | staff_id | last_update              |
|-----------|--------------------------|--------------|-------------|--------------------------|----------|--------------------------|
| 731       | 2005-05-29T07:25:16.000Z | 4124         | 5           | 2005-05-30T05:21:16.000Z | 1        | 2006-02-15T21:30:53.000Z |
| 1085      | 2005-05-31T11:15:43.000Z | 301          | 5           | 2005-06-07T12:02:43.000Z | 1        | 2006-02-15T21:30:53.000Z |
| 1142      | 2005-05-31T19:46:38.000Z | 3998         | 5           | 2005-06-05T14:03:38.000Z | 1        | 2006-02-15T21:30:53.000Z |
| 1502      | 2005-06-15T22:03:14.000Z | 3277         | 5           | 2005-06-23T18:42:14.000Z | 2        | 2006-02-15T21:30:53.000Z |
| 1631      | 2005-06-16T08:01:02.000Z | 2466         | 5           | 2005-06-19T09:04:02.000Z | 1        | 2006-02-15T21:30:53.000Z |

## 1.2 inventory table

The inventory table consists of all the records of avalaible film copies and the store which they belong to.

```sql
SELECT *
FROM dvd_rentals.inventory
LIMIT 5;
```

*Output:*

| inventory_id | film_id | store_id | last_update              |
|--------------|---------|----------|--------------------------|
| 1            | 1       | 1        | 2006-02-15T05:09:17.000Z |
| 2            | 1       | 1        | 2006-02-15T05:09:17.000Z |
| 3            | 1       | 1        | 2006-02-15T05:09:17.000Z |
| 4            | 1       | 1        | 2006-02-15T05:09:17.000Z |
| 5            | 1       | 2        | 2006-02-15T05:09:17.000Z |

One film can have multiple copies across different stores. Next we'll check the total inventory.

```sql
SELECT 
  COUNT(*)
FROM dvd_rentals.inventory;
```

*Output:*

| count |
|-------|
| 4581  |

Also, we'll take a look at inventory records for one particular ```film_id```.

```sql
SELECT *
FROM dvd_rentals.inventory
WHERE film_id = 10
LIMIT 5;
```

*Output:*

| inventory_id | film_id | store_id | last_update              |
|--------------|---------|----------|--------------------------|
| 46           | 10      | 1        | 2006-02-15T05:09:17.000Z |
| 47           | 10      | 1        | 2006-02-15T05:09:17.000Z |
| 48           | 10      | 1        | 2006-02-15T05:09:17.000Z |
| 49           | 10      | 1        | 2006-02-15T05:09:17.000Z |
| 50           | 10      | 2        | 2006-02-15T05:09:17.000Z |

## 1.3 film table

This table contains the film informations and will be the main table we will be using to join other tables together. We will ignore the last 2 columns for the purpose of this study. 

```sql
SELECT 
  film_id,
  title,
  description,
  release_year,
  language_id,
  rental_duration,
  rental_rate,
  length,
  replacement_cost,
  rating,
  last_update
FROM dvd_rentals.film
LIMIT 5;
```

*Output:*

| film_id | title            | description                                                                                                           | release_year | language_id | rental_duration | rental_rate | length | replacement_cost | rating | last_update              |
|---------|------------------|-----------------------------------------------------------------------------------------------------------------------|--------------|-------------|-----------------|-------------|--------|------------------|--------|--------------------------|
| 1       | ACADEMY DINOSAUR | A Epic Drama of a Feminist And a Mad Scientist who must Battle a Teacher in The Canadian Rockies                      | 2006         | 1           | 6               | 0.99        | 86     | 20.99            | PG     | 2006-02-15T05:03:42.000Z |
| 2       | ACE GOLDFINGER   | A Astounding Epistle of a Database Administrator And a Explorer who must Find a Car in Ancient China                  | 2006         | 1           | 3               | 4.99        | 48     | 12.99            | G      | 2006-02-15T05:03:42.000Z |
| 3       | ADAPTATION HOLES | A Astounding Reflection of a Lumberjack And a Car who must Sink a Lumberjack in A Baloon Factory                      | 2006         | 1           | 7               | 2.99        | 50     | 18.99            | NC-17  | 2006-02-15T05:03:42.000Z |
| 4       | AFFAIR PREJUDICE | A Fanciful Documentary of a Frisbee And a Lumberjack who must Chase a Monkey in A Shark Tank                          | 2006         | 1           | 5               | 2.99        | 117    | 26.99            | G      | 2006-02-15T05:03:42.000Z |
| 5       | AFRICAN EGG      | A Fast-Paced Documentary of a Pastry Chef And a Dentist who must Pursue a Forensic Psychologist in The Gulf of Mexico | 2006         | 1           | 6               | 2.99        | 130    | 22.99            | G      | 2006-02-15T05:03:42.000Z |


## 1.4 film_category table

The 4th table in the ERD is film_category which shows the relationship between film_id and category_id.

```sql
SELECT * 
FROM dvd_rentals.film_category
LIMIT 5;
```

| film_id | category_id | last_update              |
|---------|-------------|--------------------------|
| 1       | 6           | 2006-02-15T05:07:09.000Z |
| 2       | 11          | 2006-02-15T05:07:09.000Z |
| 3       | 6           | 2006-02-15T05:07:09.000Z |
| 4       | 11          | 2006-02-15T05:07:09.000Z |
| 5       | 8           | 2006-02-15T05:07:09.000Z |

## 1.5 category table

The 5th table in the ERD is the category table which simply contains a one to one mapping between category_id and the name of each category.

```sql
SELECT * 
FROM dvd_rentals.category
LIMIT 5;
```

*Output:*

| category_id | name        | last_update              |
|-------------|-------------|--------------------------|
| 1           | Action      | 2006-02-15T04:46:27.000Z |
| 2           | Animation   | 2006-02-15T04:46:27.000Z |
| 3           | Children    | 2006-02-15T04:46:27.000Z |
| 4           | Classics    | 2006-02-15T04:46:27.000Z |
| 5           | Comedy      | 2006-02-15T04:46:27.000Z |

We will check the total categories as well.

```sql
SELECT 
  COUNT(DISTINCT category_id)
FROM dvd_rentals.category;
```

*Output:*

| count |
|-------|
|  16   |

## 1.6 film_actor table

The 6th table in our ERD is film_actor which shows actors who appeared in each film based off related actor_id and film_id values.

```sql
SELECT *
FROM dvd_rentals.film_actor
WHERE actor_id = 1
LIMIT 5;
```

*Output:*

| actor_id	| film_id | last_update|
|----------|---------|--------------------------|
|1	|1|	2006-02-15 05:05:03|
|1	|23|	2006-02-15 05:05:03|
|1	|25|	2006-02-15 05:05:03|
|1	|106|	2006-02-15 05:05:03|
|1	|140|	2006-02-15 05:05:03|


## 1.7 actor table

```sql
SELECT *
FROM dvd_rentals.actor
LIMIT 5;
```

*Output:*

| actor_id | first_name | last_name    | last_update              |
|----------|------------|--------------|--------------------------|
| 1        | PENELOPE   | GUINESS      | 2006-02-15T04:34:33.000Z |
| 2        | NICK       | WAHLBERG     | 2006-02-15T04:34:33.000Z |
| 3        | ED         | CHASE        | 2006-02-15T04:34:33.000Z |
| 4        | JENNIFER   | DAVIS        | 2006-02-15T04:34:33.000Z |
| 5        | JOHNNY     | LOLLOBRIGIDA | 2006-02-15T04:34:33.000Z |

```sql
SELECT
  COUNT(DISTINCT actor_id)
FROM dvd_rentals.actor;
```

*Output:*

| count |
|-------|
|  200  |
