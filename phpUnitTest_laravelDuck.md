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


# Khi viết test cho ứng dụng PHP hoặc Laravel, việc tạo dữ liệu giả (fake data) là rất quan trọng để kiểm thử logic mà không phụ thuộc vào dữ liệu thật. PHPUnit và Laravel (bao gồm Laravel Dusk cho test giao diện) đều hỗ trợ nhiều cách để tạo dữ liệu giả.

## Phần 1: PHPUnit

### 1. Mock Objects
- PHPUnit cung cấp phương thức:
- ```
  - createMock(ClassName::class)
  - getMockBuilder(ClassName::class)->getMock()
  ``
- Dùng để giả lập hành vi của class hoặc interface mà không cần kết nối thực tế.

Ví dụ:
```
public function testUserRepository()
{
    $mockDb = $this->createMock(Database::class);
    $mockDb->method('find')->willReturn(['id' => 1, 'name' => 'John Doe']);

    $repo = new UserRepository($mockDb);
    $result = $repo->getUser(1);

    $this->assertEquals('John Doe', $result['name']);
}

```
### 2. Dùng Faker trong PHPUnit
```
- Faker là thư viện phổ biến để tạo dữ liệu giả như tên, email, địa chỉ.
- Cài đặt: composer require fakerphp/faker --dev

Ví dụ:
use Faker\Factory;

public function testWithFakeData()
{
    $faker = Factory::create();
    $name = $faker->name;
    $email = $faker->email;

    $this->assertNotEmpty($name);
    $this->assertNotEmpty($email);
}
```
## Phần 2: Laravel

### 1. Seeder với Faker
Có thể dùng Faker trực tiếp trong Seeder:
```
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Faker\Factory as Faker;

class UserSeeder extends Seeder
{
    public function run(): void
    {
        $faker = Faker::create();

        foreach (range(1, 10) as $index) {
            DB::table('users')->insert([
                'name' => $faker->name,
                'email' => $faker->unique()->safeEmail,
                'password' => bcrypt('password'),
                'created_at' => now(),
                'updated_at' => now(),
            ]);
        }
    }
}
```
### 2. Seeder với Factory
Seeder trợ Factory tích hợp Faker sẵn.
```
class UserSeeder extends Seeder
{
    public function run(): void
    {
        User::factory()->count(50)->create();
    }
}

Ví dụ Factory (UserFactory.php):
public function definition(): array
{
    return [
        'name' => $this->faker->name(),
        'email' => $this->faker->unique()->safeEmail(),
        'password' => bcrypt('password'),
    ];
}
```
### 3. Cách chạy Seeder
- Đăng ký Seeder trong DatabaseSeeder.php:
```
public function run(): void
{
    $this->call([
        UserSeeder::class,
    ]);
}
```
- Chạy lệnh:
php artisan db:seed


# So sánh giữa E2E Testing và Unit Testing:

## ✅ 1. Mục tiêu

E2E Testing: Kiểm tra toàn bộ luồng hoạt động của ứng dụng từ đầu đến cuối, mô phỏng hành vi thực tế của người dùng.
Unit Testing: Kiểm tra một đơn vị nhỏ nhất của mã (thường là một hàm hoặc phương thức) để đảm bảo nó hoạt động đúng.


## ✅ 2. Phạm vi

E2E: Bao gồm nhiều thành phần (UI, API, DB, dịch vụ bên thứ ba).
Unit: Chỉ tập trung vào logic nội bộ của một module hoặc hàm.


## ✅ 3. Tốc độ

E2E: Chạy chậm hơn vì phải khởi tạo môi trường, trình duyệt, dữ liệu.
Unit: Chạy rất nhanh, thường chỉ vài mili-giây cho mỗi test.


## ✅ 4. Độ phức tạp

E2E: Khó thiết lập, cần công cụ như Cypress, Playwright, Selenium.
Unit: Dễ viết, thường dùng JUnit, pytest, Mocha.


## ✅ 5. Chi phí bảo trì

E2E: Cao, vì khi UI hoặc luồng nghiệp vụ thay đổi, test dễ bị hỏng.
Unit: Thấp, vì chỉ phụ thuộc vào logic code.


## ✅ 6. Mục đích sử dụng

E2E: Đảm bảo hệ thống hoạt động đúng khi tích hợp.
Unit: Đảm bảo từng phần nhỏ hoạt động chính xác.


📌 Ví dụ minh họa:

Unit Test: Kiểm tra hàm calculateTotal(price, quantity) trả về đúng kết quả.
E2E Test: Mô phỏng người dùng đăng nhập, thêm sản phẩm vào giỏ, thanh toán và nhận email xác nhận.
