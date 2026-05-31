# URL-based access control can be circumvented

## Information

- Lab: Access control
- Level: PRACTITIONER
- Description:

    ![](<Images/URL-based access control can be circumvented 0.png>)

## Solution

![](<Images/URL-based access control can be circumvented 1.png>)

When I accessed the lab, I noticed there was an `Admin Panel` section. However, when I tried to access it, I received the following message:

```text
Access Denied !
```

I captured the request using Burp.

![](<Images/URL-based access control can be circumvented 2.png>)

I tried to bypass the restriction using the `X-Original-URL` header:

```http
X-Original-URL: /admin
```

And it worked.

![](<Images/URL-based access control can be circumvented 3.png>)

According to the lab requirements, I needed to delete the user `carlos`, so I searched through the response and found what I needed.

![](<Images/URL-based access control can be circumvented 4.png>)

Next, I continued using the `X-Original-URL` header:

```http
GET /?username=carlos
...

X-Original-URL: /admin/delete
```

![](<Images/URL-based access control can be circumvented 5.png>)

I sent the request, and the lab was solved.

![](<Images/URL-based access control can be circumvented 6.png>)