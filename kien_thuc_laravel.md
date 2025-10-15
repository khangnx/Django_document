
# Tổng hợp Kiến Thức Quan Trọng về Laravel

Laravel là một PHP framework mạnh mẽ, dễ sử dụng và rất phổ biến. Dưới đây là tổng hợp các kiến thức quan trọng giúp bạn nắm vững Laravel.

---

## 1. Kiến trúc tổng quan của Laravel

Laravel tuân theo mô hình MVC (Model - View - Controller):
- Model: Quản lý dữ liệu và tương tác với database.
- View: Giao diện người dùng (thường dùng Blade template).
- Controller: Xử lý logic và điều phối giữa Model và View.

---

## 2. Routing (Định tuyến)

Laravel sử dụng hệ thống định tuyến đơn giản và mạnh mẽ:
- Hỗ trợ các phương thức: GET, POST, PUT, DELETE, PATCH.
- Có thể gán middleware, đặt tên route, nhóm route theo prefix.

---

## 3. Middleware

Middleware là lớp trung gian xử lý request trước khi đến controller:
- Dùng để kiểm tra xác thực, phân quyền, ghi log.
- Có thể tạo bằng lệnh: php artisan make:middleware CheckRole

---

## 4. Authentication & Authorization

### Authentication (Xác thực)
- Laravel hỗ trợ xác thực người dùng qua Breeze, Jetstream, Fortify, Sanctum.
- Dùng Auth::check(), Auth::user() để kiểm tra trạng thái đăng nhập.

### Authorization (Phân quyền)
- Dùng Gate hoặc Policy để kiểm tra quyền truy cập.
- Tạo policy: php artisan make:policy PostPolicy --model=Post

---

## 5. Eloquent ORM

Eloquent là hệ thống ORM giúp thao tác với database dễ dàng:
- Ví dụ: User::where('active', 1)->get()
- Hỗ trợ quan hệ: hasOne, hasMany, belongsTo, belongsToMany

---

## 6. Validation (Kiểm tra dữ liệu)

Laravel hỗ trợ kiểm tra dữ liệu đầu vào:
- Ví dụ: $request->validate(['name' => 'required|max:255'])

---

## 7. Blade Template

Blade là engine template của Laravel:
- Cú pháp đơn giản: {{ $user->name }}, @if($user->isAdmin)

---

## 8. Migration & Seeder

### Migration
- Quản lý cấu trúc bảng trong database.
- Lệnh: php artisan make:migration create_users_table

### Seeder
- Tạo dữ liệu mẫu cho database.
- Lệnh: php artisan make:seeder UserSeeder

---

## 9. Artisan Command

Artisan là công cụ dòng lệnh của Laravel:
- Ví dụ: php artisan route:list, php artisan migrate

---

## 10. Laravel Ecosystem

Laravel có hệ sinh thái phong phú:
- Horizon, Telescope, Nova, Scout, Sanctum, Passport

---

## 11. Các khái niệm nâng cao

- Service Container: Quản lý dependency injection
- Service Provider: Đăng ký các dịch vụ
- Event & Listener: Xử lý các sự kiện
- Queue & Job: Tác vụ nền
- Broadcasting: Gửi dữ liệu real-time

---

## 12. Testing

Laravel hỗ trợ viết test bằng PHPUnit:
- Lệnh: php artisan make:test UserTest

---

## 13. API Development

Laravel hỗ trợ phát triển RESTful API:
- Dùng Route::apiResource()
- Xác thực bằng Sanctum hoặc Passport
- Trả về JSON response
