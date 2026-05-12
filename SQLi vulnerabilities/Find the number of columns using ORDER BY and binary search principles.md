# Find the number of columns using ORDER BY and binary search principles.

Before performing a `UNION` Attack, we need to know the exact number of columns in the current query because `UNION` requires both queries to have the same number of columns.

To do that, one of the simplest ways is using `NULL`. We try with 1, 2, 3, ... `NULL` values until the server no longer returns an error.

```sql
a' UNION SELECT NULL, NULL ...
```

At that point, we can find the exact number of columns based on the number of `NULL` values.

--> Time complexity: `O(N)`

---

The method above can work well with a small number of columns (around 5 - 10 columns).

But with a large number of columns (dozens or even hundreds), this method not only takes a lot of time, but also attracts attention and is easier to detect because too many requests are sent to the server.

We need another approach that is more efficient. That is using `ORDER BY` and *Binary Search principles*.

Because `ORDER BY` can work with column indexes (starting from 1), this allows us to quickly check whether "column number `n` exists or not".

Idea:

- We first guess that the current query has `N` columns.

```sql
a' ORDER BY N -- -
```

- If the query does not return an error --> number of columns >= `N`
- If the query returns an error --> number of columns < `N` --> Use `N` as max and `0` as min
- After we have min and max, we perform Binary Search on the range `[0; N]` like this:

```text
// Pseudocode

min = 0
max = N

while min <= max:
    middle = (min + max) / 2        // Take integer part

    Try: a' ORDER BY middle -- -

    If no error:
        min = middle + 1

    If error:
        max = middle - 1

// Loop ends, the exact number of columns will be max

return max
```

--> Time complexity: `O(log N)`

---

Example: The exact number of columns is 31

- min = 0
- max = 50

```sql
a' ORDER BY 25 -- -
```

--> No error

- min = 26
- max = 50

```sql
a' ORDER BY 38 -- -
```

--> Error

- min = 26
- max = 37

```sql
a' ORDER BY 31 -- -
```

--> No error

- min = 32
- max = 37

```sql
a' ORDER BY 34 -- -
```

--> Error

- min = 32
- max = 33

```sql
a' ORDER BY 32 -- -
```

--> Error

- min = 32
- max = 31

Because max < min (`31 < 32`)

--> The exact number of columns is max = 31

---

Conclusion:

With the same example above, using the first method, we need around `31` requests to find the number of columns, while with the second method, we only need `5` requests (a pretty significant difference).

Using `ORDER BY` and Binary Search is a very efficient way to find the number of columns in the current query.