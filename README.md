# Marketing Analytics Case Study 

> This case study is contained within the [Serious SQL](https://www.datawithdanny.com) by Danny Ma. 
## 📕 **Table of contents**
<!--ts-->
   * 🛠️ [Overview](#️-overview)
   * 📊 [Data Exploration](-#data-exploration)
   * 📌 [Data Analysis](#-data-analysis)
   * 🧲 [Join Implementation](#-join-implementation)
   * 💥 [Problem Solving](#-problem-solving)
   * 🚀 [Solutions](#-solutions)


## 🛠️ Overview
Personalized customer emails based off marketing analytics is a winning formula for many digital companies, and this is exactly the initiative that the leadership team at DVD Rental Co has decided to tackle!

We have been asked to support the customer analytics team at DVD Rental Co who have been tasked with generating the necessary data points required to populate specific parts of this first-ever customer email campaign.

The following email template has been shared to us by the marketing team at DVD Rental Co. They need our help in order to start a personalized marketing campaign.

<p align="center">
  
<img src="https://github.com/eluah/marketing-analytics-case-study/blob/main/img/dvd_rental_co.png" alt="email-template" width="700px">
  
</p>

We'll summarize the business requirements and information needed in the table below.

Data Points | Business Requirement | What information do we need to find? | Flag to Marketing Team | 
----------- | ----------- | ------------------------------------ | -------------- |
1 & 4       | Top 2 Categories | Identify top 2 categories for each customer based off their past rental history. | - |
2 & 5       | Individual Customer Insights | For data point 2, identify total films watched, average comparison and percentile. For data point 5, identify total films watched and catergory percentage. | - |
3 & 6       | Film Recommendations | Identify 3 most popular films for each customer's top 2 categories that they have not watched. | If customer has no film recommendations for either category. |
7, 8 & 9    | Top Actor Recommendation | Identify favourite actor with 3 film recommendations that have not been recommended. | If customer does not have at least 1 recommendation. |


## 📊 Data Exploration
Firstly, we start with data exploration. There are 7 tables in total viz ```rental```, ```inventory```, ```film```, ```film_category```, ```category```, ```film_actor``` and ```actor```. The Entity Relationship diagram is below.

<p align="center">
    <img src="https://github.com/eluah/marketing-analytics-case-study/blob/main/img/dvd_rental_erd.png" alt="erd">
</p>

### Click to view 👇:
https://github.com/eluah/marketing-analytics-case-study/blob/main/Data%20Exploration/README.MD

## 📌 Data Analysis

Based on the ERD above, the key columns we will need to generate each customer insight for answering questions are:
  * **```category_name```**: The name of the top 2 ranking categories
  * **```rental_count```**: How many total films have they watched in this category?
  * **```average_comparison```**: How many more films has the customer watched compared to the average DVD Rental Co customer?
  * **```percentile```**: How does the customer rank in terms of the top X% compared to all other customers in this film category?
  * **```category_percentage```**: What proportion of each customer’s total films watched does this count make?

In summary, we have now successfully answered all 3 questions for table join:

**1. What is the purpose of joining these two tables?**

> I need to keep all of the customer rental records from **```dvd_rentals.rental```** and match up each record with its equivalent **```film_id```** value from the **```dvd_rentals.inventory```** table.

**2. What is the distribution of foreign keys within each table?**
> There is a **1-to-many relationship** between the **```inventory_id```** and the rows of the **```dvd_rentals.rental```** table

|row_counts|count_of_fk_values|
|----------|------------------|
|1         |4                 |
|2         |1126              |
|3         |1151              |
|4         |1160              |
|5         |1139              |

 <br /> 

> There is a **1-to-1 relationship** between the **```inventory_id```** and the rows of the **```dvd_rentals.inventory```** table

|row_counts|count_of_fk_values|
|----------|------------------|
|1         |4581              |

 <br /> 

**3. How many unique foreign key values exist in each table?**

All of the foreign key values in **```dvd_rentals.rental```** exist in **```dvd_rentals.inventory```** and only 1 record **```inventory_id```** = 5 exists only in the **```dvd_rentals.inventory```** table.

There is an overlap of **4,580 unique** **```inventory_id```** **foreign key values** which will exist after the join is complete.

### Click to view 👇:
https://github.com/eluah/marketing-analytics-case-study/edit/main/Data%20Analysis/README.MD

## 🧲 Join Implementation

Next, we start implementing the table joins which will then help us to start the problem solving. From the analysis section, we have come to conclusion to the following join table sequence.

### Click to view 👇:


## 💥 Problem Solving

### Click to view 👇:


## 🚀 Solutions

### Click to view 👇:

