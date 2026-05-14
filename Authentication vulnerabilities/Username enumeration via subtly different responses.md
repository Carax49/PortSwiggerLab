# Username enumeration via subtly different responses

## Information

- Lab: Authentication vulnerabilities
- Level: PRACTITIONER
- Description:

    ![](<Images/Username enumeration via subtly different responses 0.png>)    

## Solution

Access the `My Account` page and try logging in with any account.

![](<Images/Username enumeration via subtly different responses 1.png>) 

And I received a message:

```text
Invalid username or password.
```

Use BurpSuite to capture that traffic and send it to the `Intruder` tab to perform the attack.

I will attack the username first with the username list provided by the lab in the description.

Attack type: `Sniper attack`

![](<Images/Username enumeration via subtly different responses 2.png>) 

It is easy to see that when either the username or password is incorrect, the server will return the message `Invalid username or password.`

I will use the `Grep - Match` feature in Burp to check whether that string appears in the response or not.

![](<Images/Username enumeration via subtly different responses 3.png>) 

After finishing the setup, I click `Start attack` to begin the attack.

After it finishes, I can easily see that one payload has a different response compared to the others.

The payload `acounting` does not contain the string `Invalid username or password.` in the response.

![](<Images/Username enumeration via subtly different responses 4.png>) 

From that, I identified the username as `accounting`. The next step is to brute-force the password.

![](<Images/Username enumeration via subtly different responses 5.png>) 

Similar to the username attack, I also use the password list provided in the description and set the Attack type to `Sniper attack`.

![](<Images/Username enumeration via subtly different responses 6.png>) 

After it finishes, I sort by `Length` and immediately notice the difference in the payload `klaster`.

```text
Status code: 302 --> redirect page
Length: 192
```

So now I have all the required information.

```text
Username: accounting
Password: klaster
```

Log in with that account and the lab is solved.

![](<Images/Username enumeration via subtly different responses 7.png>) 