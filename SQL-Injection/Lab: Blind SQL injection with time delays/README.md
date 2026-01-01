## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/9955f4d7f900928c7766e832e9abf7406492ca5c/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20time%20delays/Images/Lab%20Description.png)

## Overview :

 it is often possible to exploit the blind SQL injection vulnerability by triggering time delays conditionally, depending on an injected condition. Because SQL queries are generally processed synchronously by the application, delaying the execution of a SQL query will also delay the HTTP response. This allows us to infer the truth of the injected condition based on the time taken before the HTTP response is received.

The techniques for triggering a time delay are highly specific to the type of database being used. On Microsoft SQL Server, input like the following can be used to test a condition and trigger a delay depending on whether the expression is true: 

```sql
'; IF (1=2) WAITFOR DELAY '0:0:10'--
'; IF (1=1) WAITFOR DELAY '0:0:10'--
```

 The first of these inputs will not trigger a delay, because the condition 1=2 is false. The second input will trigger a delay of 10 seconds, because the condition 1=1 is true.

Using this technique, we can retrieve data in the way already described, by systematically testing one character at a time: 

```sql
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```
## Solution :

#### Cause a 10 sec delay in response -

The request looks like this 

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/2ef9aceef941b88c11fbc459cf8f23333e73efc6/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20time%20delays/Images/Cookie%20Session%20Detail.png)

SLEEP command differs for each type of databse, here we find it by trial & error method that it is a *POSTgreSQL database*

![image]()


To do this we can use the following payload 

```sql
TrackingId=x'||pg_sleep(10)--
```

![image]()

We get a response after 10 sec which confirms the SQL injection vulnerability.


![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/1775d2d6309e162c1ea515b9bb91b6cb25cea9f1/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20time%20delays/Images/Lab%20Solution.png)






