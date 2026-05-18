# User role controlled by request parameter

## Information

- Lab: Access control
- Level: APPRENTICE
- Description:

    ![](<Images/User role controlled by request parameter 0.png>)    

## Solution

Access the website, go to the `My account` page, and log in using the account provided by the lab.

![](<Images/User role controlled by request parameter 1.png>)

Capture the login traffic in Burp Suite.

![](<Images/User role controlled by request parameter 2.png>)

We can immediately see that in the `Cookie` header, the `Admin` parameter is set to false.

```text
Admin=false
```

I tried changing it to `true`.

![](<Images/User role controlled by request parameter 3.png>)

And it worked ! I could see an endpoint in the response body:

```text
/admin
```

I then tried changing the endpoint in Burp.

![](<Images/User role controlled by request parameter 4.png>)

And the response returned the Admin panel!

![](<Images/User role controlled by request parameter 5.png>)

As shown above, I could delete the `carlos` account using:

```text
/admin/delete?username=carlos
```

![](<Images/User role controlled by request parameter 6.png>)

I successfully deleted the user `carlos`. The lab is solved!

![](<Images/User role controlled by request parameter 7.png>)