# 🎨 Design Pattern trong Ruby on Rails

## 1. Model-View-Controller (MVC)
- **Model**: Quản lý dữ liệu và business logic.
- **View**: Hiển thị giao diện cho người dùng.
- **Controller**: Xử lý request, điều phối giữa Model và View.
- **Ưu điểm**: Tách biệt trách nhiệm, dễ mở rộng.

---

## 2. Service Objects
- Gom business logic phức tạp vào class riêng.
- Ví dụ: `UserRegistrationService` để xử lý đăng ký người dùng.
- **Ưu điểm**: Giữ controller/model gọn gàng, dễ test.

---

## 3. Decorator / Presenter
- Thêm logic hiển thị mà không làm phình to model.
- Ví dụ: `UserDecorator` để định dạng tên hiển thị.
- **Ưu điểm**: View gọn gàng, dễ tái sử dụng.

---

## 4. Form Objects
- Gom logic xử lý form phức tạp (nhiều model liên quan).
- Ví dụ: `SignupForm` quản lý dữ liệu từ nhiều bảng.
- **Ưu điểm**: Validation rõ ràng, tách biệt khỏi model chính.

---

## 5. Query Objects
- Đóng gói các truy vấn ActiveRecord phức tạp.
- Ví dụ: `RecentOrdersQuery.new(user).call`.
- **Ưu điểm**: Dễ tái sử dụng, tránh lặp lại query.

  #Arel trong Ruby on Rails không phải là một Query Object theo nghĩa pattern mà ta hay dùng trong ứng dụng Rails, nhưng nó có liên quan khá chặt chẽ.

  ## 🔎 Arel là gì?
  - Arel là một thư viện đi kèm Rails, dùng để xây dựng SQL AST (Abstract Syntax Tree).
  - Nó cho phép bạn tạo các truy vấn SQL phức tạp bằng Ruby thay vì viết raw SQL.
  - Ví dụ:
  ```
  users = Arel::Table.new(:users)
  query = users.project(users[:id], users[:name])
               .where(users[:age].gt(18))
  query.to_sql
  # => SELECT "users"."id", "users"."name" FROM "users" WHERE "users"."age" > 18
  
  ```
  
  ## 🧩 So sánh với Query Object Pattern
  
  | Tiêu chí | Arel | Query Object Pattern |
  |--------|------|---------------------|
  | **Bản chất** | Thư viện nội bộ của Rails | Pattern do dev tự định nghĩa |
  | **Mục đích** | Sinh SQL an toàn, linh hoạt | Đóng gói logic query phức tạp |
  | **Cách dùng** | Dùng trực tiếp trong ActiveRecord | Tạo class riêng, ví dụ `RecentOrdersQuery` |
  | **Phạm vi** | Toàn bộ Rails (ActiveRecord dựa vào) | Từng ứng dụng cụ thể |
  
  
  
  ## ✅ Kết luận
  - Arel không phải là Query Object pattern, mà là công cụ nền tảng để Rails xây dựng query.
  - Query Object pattern là một cách tổ chức code của bạn, thường dùng Arel/ActiveRecord bên trong để đóng gói logic query.
  - Nói cách khác: Query Object pattern có thể sử dụng Arel như một “nguyên liệu” để tạo ra các truy vấn phức tạp.


---

## 6. Observer / Callback
- Theo dõi thay đổi trong model và tự động thực hiện hành động.
- Ví dụ: Gửi email khi `User` được tạo mới.
- **Ưu điểm**: Tự động hóa workflow, nhưng cần dùng cẩn thận để tránh khó debug.

---

## 📊 So sánh nhanh

| Pattern            | Mục đích chính                  | Ưu điểm                          | Nhược điểm |
|--------------------|---------------------------------|----------------------------------|------------|
| MVC                | Tách biệt logic & giao diện     | Chuẩn, dễ mở rộng                | Controller/model dễ phình to |
| Service Objects    | Gom business logic              | Skinny controller/model, dễ test | Tạo nhiều class nhỏ |
| Decorator/Presenter| Logic hiển thị                  | View gọn gàng, dễ tái sử dụng    | Thêm lớp phức tạp |
| Form Objects       | Xử lý form phức tạp             | Validation rõ ràng               | Tăng số lượng object |
| Query Objects      | Gom query phức tạp              | Dễ tái sử dụng, DRY              | Dư thừa nếu query đơn giản |
| Observer/Callback  | Tự động hóa hành động           | Tiện lợi, giảm code lặp          | Khó debug, dễ ẩn logic |

---

## ⚠️ Lưu ý
- Không lạm dụng quá nhiều pattern → code khó đọc.
- Nguyên tắc: Chỉ dùng khi thực sự cần để giữ code **DRY**.
- Thường kết hợp **Service Objects + Form Objects** trong ứng dụng Rails lớn.
