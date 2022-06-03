# Join Implementation

Next I look at the tbale join journey. 

| Join Journey Part | Start               |  End                |  Foreign Key       |
|-------------------|---------------------|---------------------|--------------------|
| Part 1            | ```rental```        | ```inventory```     | ```inventory_id``` |
| Part 2            | ```inventory```     | ```film```          | ```film_id```      |
| Part 3            | ```film```          | ```film_category``` | ```film_id```      |
| Part 4            | ```film_category``` | ```category```      | ```category_id```  |

## Table Joining Checklist
1. What is the purpose of joining these two tables?
2. What contextual hypotheses do we have about the data?
3. How can we validate these assumptions?
4. What is the distribution of foreign keys within each table?
5. How many unique foreign key values exist in each table?


