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

Now we test the assunmptions, by generating the row counts for ```film_id``` for both the ```dvd_rentals.inventory``` and ```dvd_rentals.film``` tables

Let’s use a summarized group by with a CTE for the dvd_rentals.inventory table:

