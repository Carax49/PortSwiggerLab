# File path traversal, traversal sequences stripped non-recursively

## Information

- Lab: File path traversal
- Level: PRACTITIONER
- Description:

    ![](<Images/File path traversal, traversal sequences stripped non-recursively 0.png>)    

## Solution

Access the website and use Burp Suite to intercept the requests.

I noticed that most of the requests were related to the images displayed on the website.

```text
Note: If you do not see any requests like I mentioned, try following the steps shown in the image below.
```

![](<Images/File path traversal, traversal sequences stripped non-recursively 1.png>)

I selected one of them and sent it to Repeater to modify the payload into:

```text
/etc/passwd
```

And I received the result:

```text
No such file
```

![](<Images/File path traversal, traversal sequences stripped non-recursively 3.png>)

It seems that the web application only retrieves data from the current Working Directory.

So I tried using `../` to move back to the root directory.

```text
../../../../../../../etc/passwd
```

The server responded with:

```text
No such file
```

It looks like the backend removed all of my `../` sequences, so I decided to use another payload to bypass it.

---

Idea:

If my payload is `../{file_name}`, then after being sent to the server, it becomes only `{file_name}` because the `../` gets removed.

So what happens if the payload is `....//` ?

After removing `../` (non-recursively), the payload becomes `../` again ! ---> Path Traversal

---

So I used the payload:

```text
....//....//....//....//....//....//....//etc/passwd
```

And it worked! I was able to read `/etc/passwd`.

![](<Images/File path traversal, traversal sequences stripped non-recursively 4.png>)

The lab was solved.