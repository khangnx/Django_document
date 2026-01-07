# Laravel Dusk

## 📌 Giới thiệu Laravel Dusk
- **Mục đích**: Tự động hóa kiểm thử giao diện người dùng (UI) và hành vi của ứng dụng web Laravel.  
- **Cách hoạt động**: Dusk khởi chạy trình duyệt (thường là Chrome thông qua ChromeDriver) và thực hiện các thao tác như click, nhập dữ liệu, điều hướng, kiểm tra nội dung.  
- **Ưu điểm**:
  - Không cần Selenium/JDK, cài đặt đơn giản.
  - API rõ ràng, dễ viết test.
  - Hỗ trợ chụp ảnh màn hình, lưu HTML, console log khi test thất bại.  

---

## ⚙️ Các bước cài đặt cơ bản
1. **Cài đặt package**:  
   ```bash
   composer require --dev laravel/dusk
   php artisan dusk:install
   ```
 2.  **Tạo test mới**:
 ```
php artisan dusk:make LoginTest
```
3. **Chạy test**:
```
php artisan dusk
```
## 🛠 Tính năng chính
- Quản lý ChromeDriver: Tự động tải và cập nhật phiên bản phù hợp.
- Hỗ trợ nhiều trình duyệt: Ngoài Chrome, có thể dùng các driver Selenium khác.
- Tương tác với phần tử:
- Click, nhập text, chọn dropdown, upload file.
- Kiểm tra text, giá trị, thuộc tính.
- Assertions: Kiểm tra URL, nội dung, trạng thái phần tử.
- Chụp màn hình & lưu HTML: Giúp debug khi test thất bại.
- Macros & Components: Tái sử dụng logic test cho nhiều phần giao diện.
```
📄 Ví dụ test đơn giảnpublic function testLogin()
{
    $this->browse(function ($browser) {
        $browser->visit('/login')
                ->type('email', 'user@example.com')
                ->type('password', 'secret')
                ->press('Login')
                ->assertPathIs('/home');
    });
}
```
## 🔑 Khi nào nên dùng Laravel Dusk?
- Khi bạn muốn kiểm thử toàn bộ luồng người dùng (end-to-end).
- Khi cần đảm bảo giao diện hoạt động đúng sau khi deploy.
- Khi muốn tích hợp CI/CD để tự động chạy test trên GitHub Actions, Travis CI, Heroku CI.
## ⚠️ Lưu ý & hạn chế
- Chạy test có thể chậm hơn so với unit test vì phải khởi động trình duyệt.
- Chủ yếu phù hợp cho ứng dụng Laravel, không dùng cho framework khác.
- Nếu muốn kiểm thử hiệu năng hoặc API, nên dùng công cụ khác (ví dụ: PHPUnit, Postman, JMeter).
## So sánh Unit Test và Dusk Test
| Loại test | Mục tiêu | Công cụ thường dùng | Ví dụ |
|----------|---------|--------------------|-------|
| **Unit Test** | Kiểm thử từng hàm, class, logic nhỏ trong code | PHPUnit (tích hợp sẵn trong Laravel) | Kiểm tra hàm tính tổng, kiểm tra validation rule |
| **Feature Test** | Kiểm thử chức năng ở mức controller/service, không cần trình duyệt | PHPUnit + Laravel Testing Helpers | Kiểm tra API `/login` trả về JSON đúng |
| **Dusk Test (Browser Test)** | Mô phỏng hành vi người dùng trên giao diện web | Laravel Dusk | Người dùng nhập email + password, bấm Login, được chuyển hướng sang `/home` |

## ✅ Dusk có dùng để test chức năng không?
- Có thể, nhưng ở mức chức năng qua giao diện (UI), tức là test xem người dùng thao tác có đúng kết quả mong muốn.
- Không phù hợp để test logic nội bộ (unit test), vì Dusk chạy trình duyệt, tốn thời gian và phức tạp hơn.
### 👉 Nếu muốn test chức năng nghiệp vụ (ví dụ: tính toán, xử lý dữ liệu), hãy dùng unit test hoặc feature test.
### 👉 Nếu muốn test luồng người dùng (ví dụ: đăng nhập, đăng ký, mua hàng), hãy dùng Dusk.
