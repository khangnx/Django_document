# PHP Unit Test và Laravel Dusk - Tổng hợp chi tiết

## 1. PHP Unit Test là gì?
- Định nghĩa: PHP Unit Test là một framework kiểm thử đơn vị (unit testing) cho PHP, giúp kiểm tra từng phần nhỏ của mã nguồn (class, method) để đảm bảo hoạt động đúng.
- Ưu điểm:
  + Tự động hóa kiểm thử, giảm lỗi.
  + Dễ tích hợp CI/CD.
  + Hỗ trợ mock, assert đa dạng.
- Nhược điểm:
  + Tốn thời gian viết test.
  + Cần hiểu rõ logic code để viết test tốt.

## 2. Laravel Dusk là gì?
- Định nghĩa: Laravel Dusk là công cụ kiểm thử giao diện (browser testing) cho ứng dụng Laravel, dùng để kiểm thử end-to-end (E2E).
- Ưu điểm:
  + Kiểm thử thực tế trên trình duyệt.
  + Hỗ trợ thao tác UI như click, nhập liệu.
  + Không cần cài đặt Selenium.
- Nhược điểm:
  + Chạy chậm hơn unit test.
  + Yêu cầu môi trường trình duyệt (ChromeDriver).

## 3. Khi nào dùng?
- PHP Unit Test:
  + Khi cần kiểm thử logic nghiệp vụ, hàm, class.
  + Phù hợp cho kiểm thử backend.
- Laravel Dusk:
  + Khi cần kiểm thử UI, luồng người dùng.
  + Phù hợp cho kiểm thử end-to-end.

## 4. Triển khai như thế nào?
- PHP Unit Test:
  1. Cài đặt qua Composer: composer require --dev phpunit/phpunit
  2. Tạo file test trong thư mục tests/Unit.
  3. Chạy test: php artisan test hoặc vendor/bin/phpunit.
- Laravel Dusk:
  1. Cài đặt: composer require --dev laravel/dusk
  2. Đăng ký service provider trong AppServiceProvider (chỉ cho môi trường local).
  3. Tạo test: php artisan dusk:make LoginTest.
  4. Chạy test: php artisan dusk.

## 5. Ví dụ code

Ví dụ PHPUnit (Unit Test):
```
<?php

namespace Tests\Unit;

use PHPUnit\Framework\TestCase;
use App\Services\Calculator;

class CalculatorTest extends TestCase
{
    public function test_addition()
    {
        $calculator = new Calculator();
        $result = $calculator->add(2, 3);
        $this->assertEquals(5, $result);
    }

    public function test_subtraction()
    {
        $calculator = new Calculator();
        $result = $calculator->subtract(5, 2);
        $this->assertEquals(3, $result);
    }
}
```
Ví dụ Laravel Dusk (Browser Test):
```
<?php

namespace Tests\Browser;

use Laravel\Dusk\Browser;
use Tests\DuskTestCase;

class LoginTest extends DuskTestCase
{
    public function test_login_success()
    {
        $this->browse(function (Browser $browser) {
            $browser->visit('/login')
                    ->type('email', 'user@example.com')
                    ->type('password', 'secret')
                    ->press('Login')
                    ->assertPathIs('/home');
        });
    }  
}
```
    }
}
