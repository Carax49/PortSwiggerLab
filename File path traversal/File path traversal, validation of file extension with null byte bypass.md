# File path traversal, validation of file extension with null byte bypass

## Information

- Lab: File path traversal
- Level: PRACTITIONER
- Description:

![](<Images/validation of file extension with null byte bypass 0.png>)

## Solution

Access the website and use Burp Suite to intercept traffic related to the images displayed on the website.

![](<Images/validation of file extension with null byte bypass 1.png>)

I first tried using an absolute path payload:

```text
/etc/passwd
```

![](<Images/validation of file extension with null byte bypass 2.png>)

I also tried using a relative path:

```text
../../../../../../etc/passwd
```

But the result was still:

```text
No such file
```

According to the lab description, we need to use a `null byte` and file extension validation bypass.

Idea

---

Some web applications only allow access to files with specific extensions such as `.txt`, `.png`, and so on.

We can use a null byte (`\0`), whose URL-encoded form is `%00`, to bypass this validation.

Some old programming languages or libraries (especially C/C++) treat a null byte as the `end of a string`, meaning the system stops reading the string once it reaches that character.

For example, suppose the server only allows files with the `.png` extension, and we use the payload:

```text
ahihi.txt%00.png
```

- The server checks for `.png` --> OK --> Validation bypassed
- The system reads `ahihi.txt`, encounters `\0` --> Stops reading

---

We can abuse this behavior to bypass the file extension validation while still reading `/etc/passwd`.

From the valid traffic we intercepted earlier, we can determine which file extension the server accepts.

![](<Images/validation of file extension with null byte bypass 3.png>)

As we can see, the server accepts the `.jpg` extension.

So I used the following payload:

```text
../../../../../etc/passwd%00.jpg
```

![](<Images/validation of file extension with null byte bypass 4.png>)

And I was able to read the contents of `/etc/passwd`.

The lab was solved.