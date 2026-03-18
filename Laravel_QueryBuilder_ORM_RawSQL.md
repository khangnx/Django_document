Dưới đây là tài liệu chi tiết so sánh Query Builder, Eloquent ORM, và Raw SQL dưới dạng Markdown để bạn dễ copy và dùng khi ôn tập hoặc phỏng vấn:

🔍 So sánh Query Builder vs Eloquent ORM vs Raw SQL
1. Giới thiệu
- Query Builder: công cụ viết truy vấn SQL bằng cú pháp PHP, linh hoạt, bảo mật, không cần model.
- Eloquent ORM: ORM theo mô hình Active Record, ánh xạ bảng thành model, hỗ trợ quan hệ, cú pháp ngắn gọn cho CRUD.
- Raw SQL: viết trực tiếp câu lệnh SQL, cho phép kiểm soát tối đa, hiệu năng cao, nhưng dễ lỗi và kém bảo mật nếu không bind parameter.

2. So sánh tổng quan
<img width="692" height="653" alt="image" src="https://github.com/user-attachments/assets/8ca72686-b0b4-4c3f-8dc2-47698b92638a" />

3. Ví dụ minh họa
Query Builder

```
$orders = DB::table('orders')
    ->join('users', 'orders.user_id', '=', 'users.id')
    ->select('orders.*', 'users.name')
    ->where('orders.status', 'completed')
    ->get();
```

Eloquent ORM

```
$orders = Order::with('user')
    ->where('status', 'completed')
    ->get();
```

Raw SQL

```
$orders = DB::select('
    SELECT orders.*, users.name
    FROM orders
    INNER JOIN users ON orders.user_id = users.id
    WHERE orders.status = ?
', ['completed']);
```


4. Ưu nhược điểm
Query Builder
- ✅ Linh hoạt, hiệu năng cao, gần SQL.
- ✅ Hỗ trợ query phức tạp, nhiều join, aggregate.
- ❌ Không tận dụng được quan hệ ORM, code dài hơn.
Eloquent ORM
- ✅ Ngắn gọn, dễ đọc, dễ bảo trì.
- ✅ Hỗ trợ quan hệ, eager loading, lazy loading.
- ❌ Có overhead, query phức tạp có thể khó viết.
Raw SQL
- ✅ Kiểm soát tối đa, hiệu năng cao nhất.
- ✅ Dùng được mọi tính năng SQL đặc biệt.
- ❌ Dễ lỗi, khó bảo trì, dễ SQL Injection nếu không bind parameter.

5. Kết luận
- Dùng Eloquent: cho CRUD, quản lý quan hệ, code dễ đọc và bảo trì.
- Dùng Query Builder: cho query phức tạp, nhiều join, aggregate, khi không cần model.
- Dùng Raw SQL: khi cần tối ưu hiệu năng, viết query đặc biệt, hoặc tận dụng tính năng DB riêng (stored procedure, CTE phức tạp).
- Thực tế, nhiều dự án kết hợp cả ba: Eloquent cho CRUD, Query Builder cho báo cáo, Raw SQL cho query đặc biệt.

👉 Trong phỏng vấn, bạn có thể nói:
“Eloquent giúp tôi viết code ngắn gọn, dễ bảo trì, đặc biệt khi làm việc với quan hệ dữ liệu. Query Builder tôi dùng cho báo cáo phức tạp, nhiều join. Raw SQL tôi dùng khi cần tối ưu hiệu năng hoặc viết query đặc biệt mà Eloquent/Query Builder không hỗ trợ. Ví dụ: trong dự án e-commerce, CRUD sản phẩm tôi dùng Eloquent, báo cáo doanh thu tôi dùng Query Builder, còn query phân tích dữ liệu phức tạp tôi viết bằng Raw SQL.”
