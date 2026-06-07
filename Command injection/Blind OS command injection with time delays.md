# Blind OS command injection with time delays

## Information

- Lab: Command injection
- Level: PRACTITIONER
- Description:

    ![](<Images/Blind OS command injection with time delays 0.png>)

## Solution

Go to the `Submit feedback` page and fill in the form.

![](<Images/Blind OS command injection with time delays 1.png>)

After submitting, I received an error message because the email format was invalid.

```text
Please enter an email address
```

=> This strongly suggests that the `Email` field may be vulnerable to Command Injection.

I entered a valid email address and intercepted the request using Burp Suite.

![](<Images/Blind OS command injection with time delays 2.png>)

I then modified the email parameter with the following payload:

```text
test||sleep 10||
```

And URL-encoded it as:

```text
test%7c%7csleep%2010%7c%7c
```

Click `Send`, wait for 10 seconds, and the lab is solved.

![](<Images/Blind OS command injection with time delays 3.png>)