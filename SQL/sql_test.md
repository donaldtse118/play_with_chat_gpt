# Input
1. write sql to query name, address from table employee_detail and salary from table employ_compenstation, join by employee_id
2. rely on department in employee_detail and found out top 5 salary employ in each department
3. show me which employee without employee's salary records created

# Output 
## From step 1 - Joining table

What can do?
- understand columns in tables
  - e.g. columns `name`, `address` under table `employee_detail`
- able to create join with another table
```sql
SELECT employee_detail.name, employee_detail.address, employee_compensation.salary
FROM employee_detail
JOIN employee_compensation
ON employee_detail.employee_id = employee_compensation.employee_id;
```

## From step 2 - Summary for joining table

What can do?
- Recall table in step 1 , `employee_compensation`
- Recall how to join tables `employee_compensation` and `employee_detail`
- Select `name` as output without mention it
- generate subquery 

```sql
SELECT department, name, salary
FROM (
    SELECT department, name, salary,
           row_number() OVER (PARTITION BY department ORDER BY salary DESC) as rn
    FROM employee_detail
    JOIN employee_compensation
    ON employee_detail.employee_id = employee_compensation.employee_id
) subquery
WHERE rn <= 5;
```


### From Step 3 - for debug

What can do?
- leverage same join key
- intrepret missing record as `not in` another table

``` sql
SELECT name
FROM employee_detail
WHERE employee_id NOT IN (SELECT employee_id FROM employee_compensation);
```

