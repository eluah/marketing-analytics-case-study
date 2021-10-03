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

### Requirement 1
#### Top 2 Categories
For each customer, we need to identify the top 2 categories for each customer based off their past rental history. These top categories will drive marketing creative images as seen in the travel and sci-fi examples in the draft email.

### Requirement 2
#### Category Film Recommendations
The marketing team has also requested for the 3 most popular films for each customer’s top 2 categories.

There is a catch though - we cannot recommend a film which the customer has already viewed.

If there are less than 3 films available - marketing is happy to show at least 1 film.

Any customer which do not have any film recommendations for either category must be flagged out so the marketing team can exclude from the email campaign - this is of high importance!

### Requirement 3 & 4
#### Individual Customer Insights
The number of films watched by each customer in their top 2 categories is required as well as some specific insights.

> For the 1st category, the marketing requires the following insights (requirement 3):

1. How many total films have they watched in their top category?
2. How many more films has the customer watched compared to the average DVD Rental Co customer?
3. How does the customer rank in terms of the top X% compared to all other customers in this film category?

> For the second ranking category (requirement 4):

1. How many total films has the customer watched in this category?
2. What proportion of each customer’s total films watched does this count make?

### Requirement 5
#### Favorite Actor Recommendations
Along with the top 2 categories, marketing has also requested top actor film recommendations where up to 3 more films are included in the recommendations list as well as the count of films by the top actor.

We have been given guidance by marketing to choose the actors in alphabetical order should there be any ties - i.e. if the customer has seen 5 Brad Pitt films vs 5 George Clooney films - Brad Pitt will be chosen instead of George Clooney.

The same logical business rules apply - in addition any films that have already been recommended in the top 2 categories must not be included as actor recommendations.

If the customer doesn’t have at least 1 film recommendation - they also need to be flagged with a separate actor exclusion flag.

## 📊 Data Exploration
Firstly, we start with data exploration. There are 7 tables in total viz ```rental```, ```inventory```, ```film```, ```film_category```, ```category```, ```film_actor``` and ```actor```. The Entity Relationship diagram is below.

<p align="center">
    <img src="https://github.com/eluah/marketing-analytics-case-study/blob/main/img/dvd_rental_erd.png" alt="erd">
</p>

## 📌 Data Analysis


## 🧲 Join Implementation


## 💥 Problem Solving


## 🚀 Solutions

