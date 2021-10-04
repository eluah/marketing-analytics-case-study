# 1. Data Exploration

## 1.1 rental table

The first table in our database schema is the rental table. We'll look at the first 5 rows of the table.

```sql
SELECT * 
FROM dvd_rentals.rental
LIMIT 5;
```

*Output:*

| rental_id | rental_date              | inventory_id | customer_id | return_date              | staff_id | last_update              |
|-----------|--------------------------|--------------|-------------|--------------------------|----------|--------------------------|
| 1         | 2005-05-24T22:53:30.000Z | 367          | 130         | 2005-05-26T22:04:30.000Z | 1        | 2006-02-15T21:30:53.000Z |
| 2         | 2005-05-24T22:54:33.000Z | 1525         | 459         | 2005-05-28T19:40:33.000Z | 1        | 2006-02-15T21:30:53.000Z |
| 3         | 2005-05-24T23:03:39.000Z | 1711         | 408         | 2005-06-01T22:12:39.000Z | 1        | 2006-02-15T21:30:53.000Z |
| 4         | 2005-05-24T23:04:41.000Z | 2452         | 333         | 2005-06-03T01:43:41.000Z | 2        | 2006-02-15T21:30:53.000Z |
| 5         | 2005-05-24T23:05:21.000Z | 2079         | 222         | 2005-06-02T04:33:21.000Z | 1        | 2006-02-15T21:30:53.000Z |

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

## 1.4 film_category table


## 1.5 category table


## 1.6 film_actor table


## 1.7 actor table


