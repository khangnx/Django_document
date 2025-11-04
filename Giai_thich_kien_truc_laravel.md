
```
TỔNG HỢP GIẢI THÍCH CẤU TRÚC THƯ MỤC TRONG DỰ ÁN LARAVEL

------------------------------------------------------------
1. Console
- Khái niệm: Định nghĩa các lệnh CLI (Artisan Command).
- Công dụng: Mở rộng Artisan để thực hiện tác vụ tự động hóa.
- Ví dụ: php artisan custom:task.

2. Exceptions
- Khái niệm: Chứa các lớp xử lý ngoại lệ.
- Công dụng: Quản lý phản hồi khi xảy ra lỗi.
- Ví dụ: Handler.php.

3. Exports
- Khái niệm: Xuất dữ liệu ra file (Excel, CSV, PDF).
- Công dụng: Tách logic xuất dữ liệu khỏi Controller.
- Ví dụ: UserExport.php.

4. Http
- Khái niệm: Chứa các thành phần xử lý HTTP (Controllers, Middleware, Requests).
- Công dụng: Trung tâm của MVC.
- Ví dụ: UserController.php.

5. Imports
- Khái niệm: Nhập dữ liệu từ file vào DB.
- Công dụng: Xử lý bulk data.
- Ví dụ: ProductImport.php.

6. Jobs
- Khái niệm: Tác vụ nền (background jobs).
- Công dụng: Xử lý tác vụ nặng mà không làm chậm request.
- Ví dụ: SendEmailJob.php.

7. Listeners
- Khái niệm: Lắng nghe và xử lý sự kiện.
- Công dụng: Tách logic phản ứng với sự kiện.
- Ví dụ: SendWelcomeEmailListener.php.

8. Mail
- Khái niệm: Định nghĩa email (Mailable).
- Công dụng: Quản lý template email và logic gửi mail.
- Ví dụ: WelcomeMail.php.

9. Models
- Khái niệm: Đại diện cho bảng trong DB.
- Công dụng: Tầng kết nối dữ liệu trong MVC.
- Ví dụ: User.php.

10. Notifications
- Khái niệm: Gửi thông báo qua nhiều kênh.
- Công dụng: Tách logic gửi thông báo khỏi Controller.
- Ví dụ: InvoicePaidNotification.php.

11. Observers
- Khái niệm: Theo dõi sự kiện của Model.
- Công dụng: Tự động hóa hành động khi dữ liệu thay đổi.
- Ví dụ: UserObserver.php.

12. Providers
- Khái niệm: Service Provider đăng ký các service.
- Công dụng: Cấu hình ứng dụng, khởi tạo service.
- Ví dụ: AppServiceProvider.php.

13. Repository
- Khái niệm: Repository Pattern để quản lý truy vấn DB.
- Công dụng: Tách logic truy vấn khỏi Controller.
- Ví dụ: UserRepository.php.

14. Requests
- Khái niệm: Form Request để validate dữ liệu.
- Công dụng: Giúp Controller sạch hơn.
- Ví dụ: StoreUserRequest.php.

15. Rules
- Khái niệm: Custom Validation Rules.
- Công dụng: Định nghĩa quy tắc kiểm tra riêng.
- Ví dụ: PhoneNumberRule.php.

16. Scopes
- Khái niệm: Query Scope cho Model.
- Công dụng: Tái sử dụng điều kiện truy vấn.
- Ví dụ: active().

17. Services
- Khái niệm: Lớp Service xử lý nghiệp vụ phức tạp.
- Công dụng: Giúp Controller không bị quá tải logic.
- Ví dụ: PaymentService.php.

18. Traits
- Khái niệm: Trait để tái sử dụng code.
- Công dụng: Giảm lặp code giữa các lớp.
- Ví dụ: UploadFileTrait.php.

19. constants.php
- Khái niệm: File chứa hằng số dùng chung.
- Công dụng: Quản lý giá trị cố định tập trung.
- Ví dụ: STATUS_ACTIVE = 1.

20. helpers.php
- Khái niệm: File chứa hàm tiện ích dùng chung.
- Công dụng: Tái sử dụng logic nhỏ gọn.
- Ví dụ: formatDate(), generateSlug().

------------------------------------------------------------
MỐI QUAN HỆ GIỮA CÁC THƯ MỤC VÀ SƠ ĐỒ KIẾN TRÚC (DẠNG MÔ TẢ TEXT)

- Controller: Điểm vào của HTTP request, gọi Service, có thể phát Event.
- Service: Xử lý nghiệp vụ, gọi Repository, có thể gửi Notification/Mail.
- Repository: Quản lý truy vấn DB, làm việc với Models, dùng Scopes.
- Models: Đại diện bảng DB, kích hoạt Observers khi dữ liệu thay đổi.
- Events & Listeners: Controller/Service phát sự kiện, Listener xử lý.
- Notifications & Mail: Gửi thông báo qua nhiều kênh.
- Requests & Rules: Validate dữ liệu trước khi vào Controller.
- Traits & Helpers: Cung cấp logic tái sử dụng.
- Jobs: Xử lý tác vụ nền (queue).
- Exports & Imports: Xử lý xuất/nhập dữ liệu.
```

SƠ ĐỒ KIẾN TRÚC (TEXT):
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/2becf1fc-0d4a-45e2-a547-7674ad763201" />
