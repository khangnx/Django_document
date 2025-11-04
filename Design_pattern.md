# Design Pattern:
 - Là mẫu thiết kế phần mềm – những giải pháp đã được kiểm chứng để giải quyết các vấn đề phổ biến trong lập trình hướng đối tượng. Chúng không phải là code cụ thể, mà là cách tổ chức, cấu trúc và tương tác giữa các đối tượng để đạt được mục tiêu như dễ bảo trì, mở rộng, và tái sử dụng.

# Dùng để làm gì?

- Tái sử dụng giải pháp: Không phải phát minh lại bánh xe cho các vấn đề lặp đi lặp lại.
- Tăng tính linh hoạt: Giúp code dễ mở rộng, thay đổi mà không phá vỡ hệ thống.
- Giảm độ phức tạp: Tách biệt trách nhiệm, giảm sự phụ thuộc giữa các thành phần.
- Chuẩn hóa cách làm việc: Giúp team dễ hiểu và làm việc chung.

# Các nhóm Design Pattern phổ biến
## 1. Creational Patterns (Nhóm khởi tạo)
Mục tiêu: Quản lý cách tạo đối tượng để tránh việc code phụ thuộc trực tiếp vào new Class().


### Singleton
Đảm bảo chỉ có một instance duy nhất của một class trong toàn bộ ứng dụng.
Ví dụ: Laravel Container hoặc Config thường là Singleton.


### Factory Method
Cung cấp một phương thức để tạo đối tượng mà không cần chỉ rõ class cụ thể.
Ví dụ: Laravel Model Factory để tạo dữ liệu giả.


### Abstract Factory
Tạo ra nhóm đối tượng liên quan mà không cần chỉ rõ class cụ thể.
Ví dụ: Khi bạn có nhiều loại kết nối DB (MySQL, PostgreSQL) và muốn tạo chúng thông qua một interface chung.


### Builder
Tách quá trình tạo đối tượng phức tạp thành nhiều bước, giúp dễ tùy chỉnh.
Ví dụ: Tạo một đối tượng PDF với nhiều phần header, footer, nội dung.



## 2. Structural Patterns (Nhóm cấu trúc)
Mục tiêu: Tổ chức các class và đối tượng để dễ mở rộng và bảo trì.


### Adapter
Chuyển đổi interface của một class thành interface khác mà client mong muốn.
Ví dụ: Tích hợp một API bên thứ ba vào hệ thống Laravel bằng cách viết lớp Adapter.


### Decorator
Thêm chức năng cho đối tượng mà không thay đổi cấu trúc ban đầu.
Ví dụ: Trong Rails dùng gem Draper để thêm logic hiển thị cho model.


### Facade
Cung cấp một interface đơn giản cho một hệ thống phức tạp.
Ví dụ: Laravel Facades (như Cache::get()) thực chất là lớp trung gian cho nhiều service bên dưới.


### Composite
Cho phép xử lý nhóm đối tượng và đối tượng đơn lẻ theo cách giống nhau.
Ví dụ: Xây dựng cây menu hoặc cấu trúc phân cấp danh mục.



## 3. Behavioral Patterns (Nhóm hành vi)
Mục tiêu: Quản lý cách các đối tượng giao tiếp và phân chia trách nhiệm.


### Observer
Một đối tượng thay đổi sẽ thông báo cho các đối tượng liên quan.
Ví dụ: Laravel Events & Listeners, Rails callbacks.


### Strategy
Định nghĩa nhiều thuật toán và cho phép chọn thuật toán phù hợp lúc runtime.
Ví dụ: Middleware trong Laravel hoặc Policy trong Rails.


### Command
Đóng gói một yêu cầu thành một đối tượng để dễ quản lý, undo/redo.
Ví dụ: Tác vụ queue trong Laravel hoặc ActiveJob trong Rails.


### State
Thay đổi hành vi của đối tượng dựa trên trạng thái nội tại.
Ví dụ: Quản lý trạng thái đơn hàng (pending, shipped, delivered).

## Trong PHP (Laravel)
### Laravel sử dụng nhiều pattern:

```
Repository Pattern: Tách logic truy vấn dữ liệu khỏi Controller.
Service Pattern: Đóng gói logic nghiệp vụ vào Service.
Singleton: Laravel Container và Facades hoạt động theo kiểu Singleton.
Observer: Laravel Events & Listeners.
Factory: Laravel Model Factories để tạo dữ liệu giả.
Strategy: Middleware hoặc Policy có thể coi là Strategy.
```

## Trong Ruby on Rails
### Rails cũng áp dụng nhiều pattern:
```
Active Record: Pattern cho ORM (Rails Model).
Observer: Callback trong Model (before_save, after_create).
Decorator: Dùng gem như Draper để trang trí đối tượng.
Service Object Pattern: Tách logic nghiệp vụ ra khỏi Controller.
Command Pattern: Dùng cho các tác vụ phức tạp (thường kết hợp với Service Object).
Singleton: Rails config hoặc cache store.
```
