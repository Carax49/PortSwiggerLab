# Find the number of columns using ORDER BY and binary search principles.

Trước khi thực hiện `UNION` Attack, chúng ta cần phải biết chính xác số cột trong truy vấn hiện tại

Để làm được điều đó, một trong những các đơn giản nhất là sử dụng `NULL`

```sql
a' UNION SELECT NULL, NULL ...
```