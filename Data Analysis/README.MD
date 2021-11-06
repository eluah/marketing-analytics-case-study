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



## Question 2



## Question 3


