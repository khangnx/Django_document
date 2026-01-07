# Mockery trong Laravel được dùng để tạo “mock objects” trong quá trình viết unit test, giúp bạn giả lập hành vi của các class, service, hoặc facade mà không cần thực sự chạy chúng. Điều này cho phép bạn kiểm thử logic riêng biệt mà không phụ thuộc vào các thành phần khác.

## 🔎 Mockery là gì?
- Mockery là một thư viện PHP chuyên dùng cho unit testing. Nó cho phép bạn tạo ra các đối tượng giả (mock objects) để kiểm soát và kiểm tra cách code của bạn tương tác với các dependency.
- Laravel tích hợp sẵn các helper để làm việc với Mockery, giúp việc viết test dễ dàng hơn mà không cần gọi trực tiếp các hàm phức tạp của Mockery.

## ⚙️ Tác dụng của Mockery trong Laravel
- Giả lập đối tượng (Mock Objects):
Bạn có thể tạo một bản sao giả của một class/service để kiểm tra cách controller hoặc service gọi đến nó.
- Giả lập Facades:
Laravel cung cấp các helper để mock các facade như Mail, Queue, Event… Ví dụ, bạn có thể kiểm tra xem một email có được gửi đi hay không mà không cần thực sự gửi.
- Kiểm tra tương tác:
Mockery cho phép bạn xác định kỳ vọng (expectations) như:
- Một phương thức phải được gọi bao nhiêu lần.
- Phương thức phải được gọi với tham số nào.
- Tách biệt logic:
Giúp bạn kiểm thử từng phần của ứng dụng mà không cần chạy toàn bộ hệ thống (ví dụ: không cần kết nối database, không cần gửi mail thật).

## 📌 Ví dụ sử dụng Mockery trong Laravel
```
use Mockery;
use Tests\TestCase;

class UserServiceTest extends TestCase
{
    public function test_create_user_sends_email()
    {
        // Tạo mock cho Mailer
        $mailer = Mockery::mock('App\Services\Mailer');
        $mailer->shouldReceive('send')
               ->once()
               ->with('welcome', Mockery::type('App\User'));

        $service = new App\Services\UserService($mailer);
        $service->create(['name' => 'Nguyen', 'email' => 'test@example.com']);
    }
}
```

## 👉 Ở đây, ta không thực sự gửi email, nhưng kiểm tra được rằng hàm send() đã được gọi đúng cách.



## ⚠️ Lưu ý khi dùng Mockery
- Đóng Mockery sau mỗi test:
Thêm Mockery::close() trong tearDown() để tránh memory leak.
- Không lạm dụng:
Chỉ mock khi cần tách biệt logic. Nếu có thể, hãy dùng fakes của Laravel (ví dụ: Mail::fake(), Event::fake()), vì chúng đơn giản và dễ bảo trì hơn.

## 👉 Tóm lại: Mockery trong Laravel giúp bạn viết test dễ dàng hơn bằng cách giả lập các dependency, kiểm tra cách code tương tác với chúng mà không cần thực sự chạy.
