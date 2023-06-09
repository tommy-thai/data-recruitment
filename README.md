# Technical exercise
### Instructions:
The current version of the code is incomplete. A finalised version is to be runnable as a standalone script.
### Challenges preventing completion in good time:
* poor time management around the below blockers.
* 2 explored paths for loading the data failed.
   1. Using the native Snowflake connector `snowflake-connector-python`. I encountered a `MissingDependencyError` despite installing the dependency to the environment `pip install "snowflake-connector-python[pandas]"`
   2. Using sqlalchemy. I encountered an anonymous exception.
* I did finally overcome this with the final path I explored where I `PUT` the file on a created `STAGE`.
### Intended process to finalise:
1. Identified a formatting issue in column 'Expenditure' where a '£' sign is present. I plan to address this by cleaning this in my python code. An alternative is to switch to a ELT pipeline and load the data as is, handling the transformations within Snowflake instead.
2. Once I have confirmed all the data has loaded without errors, I would write and test SQL queries within the web Snowsight IDE (taking advantage of the intellisense) before migrating to python. My SQL queries would something like, I would alternatively use CTEs or subqueries instead of `LIMIT`:
   * How many transactions are in the dataset?
```
SELECT count(DISTINCT transaction_number)
FROM TOMMYTHAI.TEST3.TEST_TABLE
;
```
   * What is the average yearly spend for Salisbury NHS Foundation Trust?
```
SELECT 
    year(date) as year,
    sum(expenditure) as year_spend,
    avg(sum(expenditure)) over () as yearly_avg
FROM TOMMYTHAI.TEST3.TEST_TABLE
WHERE entity = 'Salisbury NHS Foundation Trust'
GROUP BY year
;
```
   * Who are the top 3 Suppliers by spend in 2018?
```
SELECT 
    supplier,
    sum(expenditure) as spend
FROM TOMMYTHAI.TEST3.TEST_TABLE
WHERE year(date) = 2018
GROUP BY supplier
ORDER BY spend DESC
LIMIT 3
;
```
   * Which Expense Area has the highest spend overall?
```
SELECT 
    expense_area,
    sum(expenditure) as spend
FROM TOMMYTHAI.TEST3.TEST_TABLE
GROUP BY expense_area
ORDER BY spend DESC
LIMIT 1
;
```
   * How many suppliers are in the dataset?
```
SELECT count(DISTINCT supplier)
FROM TOMMYTHAI.TEST3.TEST_TABLE
;
```
3. For Requirement #6 I would assume this column is added after the inital pipeline is built. If this is the case, I would use SQL to `ALTER` the table to add the new column. If the data becomes available historically I would `UPDATE` the records, otherwise I would update the pipeline to `INSERT INTO` with the new column once the column becomes available.