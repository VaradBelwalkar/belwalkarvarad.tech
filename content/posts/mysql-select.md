---
title: "MySQL Select Query"
date: 2023-07-15T09:15:19+05:30
draft: false
ShowToc: false
ShowRelatedContent: true
summary: "Select Query"
tags: ["MySQL"]
categories: ["MySQL"]
---

### Select all fields from table customers

```
SELECT * FROM customers;
```

### Select Specific Columns from a Table

```
SELECT name, email FROM customers;
```

### Alias Columns

```
SELECT name AS customer_name, email AS customer_email FROM customers;
```

### Select Distinct Values
```
SELECT DISTINCT country FROM customers; 
```
> here there will not be any duplicate entry for what you chosen, remember for what you chosen 
i.e for only the fields you considered, of course this would not do anything when you choose
all the fields as that would be an entire tuple which will always be unique



### Limit the Number of Rows Returned

```
SELECT name, email FROM customers LIMIT 10;    
```
> This is mostly used with the ORDER BY in order to get First X tuples according to sorting




### Sort the Result Set

```
SELECT name, email FROM customers ORDER BY name ASC;    (DESC for descending)
```


### Filter Rows with WHERE Clause

```
SELECT name, email FROM customers WHERE country = 'USA';    
```
> Here understand that the priority of WHERE clause is MAX and thus,  
the mysql selects only those tuples which satisfy the criteria
rather than applying filter afterwards
Here the mysql goes tuple by tuple and sees first if the tuple is matching the criteria if given then actually considers the fields to be selected


### Use Logical Operators in WHERE Clause

```
SELECT name, email FROM customers WHERE age >= 18 AND city = 'New York';     (Here you can use OR as well and also IN operator)
```


### Aggregate Functions (e.g., COUNT, SUM, AVG)

```
SELECT COUNT(*) FROM customers;
```
> here COUNT is an aggregate function in SQL, where * defines which columns to choose, abviously, you can give any field name here
 as it won't affect the result 
 It is similar to having something like this:
  SELECT * FROM customers  >>>>>>>  COUNT 
Remember COUNT only counts the tuples i.e number of results


```
SELECT name, COUNT(*) FROM customers;
```
> In MySQL, when iterating through the table and matching WHERE criteria, the required field (e.g., name) is appended to a result table. Since there's no GROUP BY, the COUNTER in the result table is simply incremented for each matching tuple. After all tuples are processed, and at least one field is selected, MySQL creates a final result table by combining the first tuple with the single COUNTER value, providing the result with fields like name and COUNT(*) or names.



```
SELECT AVG(age) FROM customers WHERE country = 'USA';
```
> Here the AVG is again an aggregate function which finds average of given field, here becomes important aspect
It is similar to 
SELECT age FROM customers WHERE country = 'USA'    >>>>>>>>>  AVG
Here the AVG function is getting tuples containing only one field i.e age, so it is easy for it to calculate average and return it



```
SELECT SUM(quantity) FROM sales;
```
>Here the SUM is an aggregate just like the AVG but instead of calculating the average it simply sums up the result
It is like,
SELECT quantity FROM sales   >>>>>>>  SUM
Here it gets the tuples just containing quantity as the field and then the SUM just sums it up





### Grouping and Aggregation (e.g., GROUP BY, HAVING)

```
SELECT country, COUNT(*) as customer_count
FROM customers
GROUP BY country
HAVING customer_count > 5;
```

> In MySQL with the GROUP BY clause, the engine iterates through the table, appending required fields to a result table based on grouping criteria. Each tuple in the result table has a separate counter. If a tuple with a country already in the result table is encountered, the counter is incremented. After processing all tuples, MySQL returns distinct names with counters.

> Note: GROUP BY doesn't require the selected field (e.g., country) in the result table; it can have other criteria like company. MySQL creates a virtual entry for grouping.

> The HAVING clause, unlike WHERE, filters the result table based on aggregate functions. Use HAVING for filtering with aggregate functions.









### Subqueries

```
SELECT name, email
FROM customers
WHERE country IN (SELECT country FROM suppliers WHERE category = 'Electronics');
```



### SUM up the fields

```
SELECT column1 + column2 AS total_sum FROM table_name;
```


### SELECT distinct values 

```
SELECT DISTINCT column_name FROM table_name;
```



### LIKE operator

```
SELECT column1 FROM table_name WHERE column2 LIKE '%pattern%';
```


### MIN MAX aggregators
```
SELECT MIN(column1), MAX(column2) FROM table_name;
```
> here the workflow is same, what mysql does is makes two field result table here, and then starts iterating through tuples
when tuple matches the criteria, if the result table is empty, the respective fields are written as it is in the fields 
i.e MIN(column1) and MAX(column2)
but when a tuple satisfies criteria, and mysql sees already entered value/s in the result table, it then takes the value/s from the 
tuple and then compares it with one present in the result table, according to MIN and MAX, it then decides whether to update it or not




### JOIN

```
SELECT table1.column, table2.column FROM table1 JOIN table2 ON table1.id = table2.id;
```