# File path traversal, validation of file extension with null byte bypass

## Information

- Lab: File path traversal
- Level: PRACTITIONER
- Description:

![](<Images/validation of file extension with null byte bypass 0.png>)

## Solution

Truy cập vào trang web và dùng Burp để bắt traffic có dạng về các bức ảnh trên web

![](<Images/validation of file extension with null byte bypass 1.png>)

Thử sử dụng payload đường dẫn tuyệt đối

```text
/etc/passwd
```

![](<Images/validation of file extension with null byte bypass 2.png>)

Tôi cũng đã thử với đường dẫn tương đối

```text
../../../../../../etc/passwd
```

Nhưng kết quả trả cũng tương tự

```text
No such file
```

Theo mô tả của lab, ta sẽ cần sử dụng `null byte` và file extension

Ý tưởng

---
Một số trang web sẽ chỉ cho phép truy cập vào 1 số file có phần mở rổng nhất định như `.txt`, `.png`, ...

Ta có thể sử dụng null byte (`\0`) có URL encode là `%00` để bypass

Một số ngôn ngữ hoặc thư viện cũ (đặc biệt C/C++) coi null byte là `kết thúc chuỗi`, tức hệ thống chỉ đọc đến kí tự đó là dừng

Giả sử server chỉ cho phép đọc file có đuôi `.png`, và ta có payload `ahihi.txt%00.png`

-> Server check `.png` --> OK --> Bypass validation

-> Hệ thống đọc `ahihi.txt`, gặp `\0` --> Kết thúc đọc

---

Ta có thể lợi dụng điều trên để bypass việc check đuôi file của hệ thống mà vẫn đọc được `/etc/passwd`

Từ những traffic hợp lệ mà ta bắt ban đầu, ta có thể biết được server chấp nhận đuôi file nào 

![](<Images/validation of file extension with null byte bypass 3.png>)

Dễ thấy, server chấp nhận đuôi `.jpg`

Ta có payload

```text
../../../../../etc/passwd%00.jpg
```

![](<Images/validation of file extension with null byte bypass 4.png>)

Và tôi đã đọc được nội dung trong `/etc/passwd`

Lab đã được giải