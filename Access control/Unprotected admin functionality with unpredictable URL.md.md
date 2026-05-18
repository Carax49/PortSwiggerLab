# Unprotected admin functionality with unpredictable URL

## Information

- Lab: Access control
- Level: APPRENTICE
- Description:

    ![](<Images/Unprotected admin functionality with unpredictable URL 0.png>)     

## Solution

Access the website and go to the `My Account` page.

![](<Images/Unprotected admin functionality with unpredictable URL 1.png>)

View the page source of the login page and we can see something interesting.

![](<Images/Unprotected admin functionality with unpredictable URL 2.png>)

As we can see, there is a hidden endpoint.

```text
/admin-3311o5
```

I tried to access that endpoint and found the admin panel!

![](<Images/Unprotected admin functionality with unpredictable URL 3.png>)

Delete `carlos` and the lab is solved.