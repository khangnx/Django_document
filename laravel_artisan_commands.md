# 🛠 Tổng hợp các lệnh Artisan trong Laravel

## 🛠 Lệnh Artisan cơ bản
| Lệnh | Mô tả |
|------|------|
| `php artisan list` | Hiển thị danh sách tất cả các lệnh Artisan |
| `php artisan help <command>` | Hiển thị hướng dẫn chi tiết cho một lệnh |
| `php artisan serve` | Khởi chạy server nội bộ tại `http://localhost:8000` |
| `php artisan tinker` | Mở giao diện dòng lệnh để tương tác với Laravel |

## 📦 Lệnh liên quan đến Composer
| Lệnh | Mô tả |
|------|------|
| `composer install` | Cài đặt các package từ `composer.lock` |
| `composer update` | Cập nhật các package theo `composer.json` |
| `composer dump-autoload` | Tái tạo lại autoload files |

## 🧱 Lệnh tạo file (make)
| Lệnh | Mô tả |
|------|------|
| `php artisan make:controller NameController` | Tạo controller mới |
| `php artisan make:model NameModel` | Tạo model mới |
| `php artisan make:migration create_table_name` | Tạo migration mới |
| `php artisan make:seeder NameSeeder` | Tạo seeder |
| `php artisan make:factory NameFactory` | Tạo factory |
| `php artisan make:request NameRequest` | Tạo form request |
| `php artisan make:middleware NameMiddleware` | Tạo middleware |
| `php artisan make:command NameCommand` | Tạo custom Artisan command |

## 🗃 Lệnh liên quan đến cơ sở dữ liệu
| Lệnh | Mô tả |
|------|------|
| `php artisan migrate` | Thực thi các migration |
| `php artisan migrate:rollback` | Quay lại migration trước đó |
| `php artisan migrate:refresh` | Rollback và migrate lại toàn bộ |
| `php artisan db:seed` | Chạy các seeder |
| `php artisan migrate:fresh --seed` | Xóa toàn bộ bảng, migrate lại và seed dữ liệu |

## 🔐 Lệnh liên quan đến bảo mật và xác thực
| Lệnh | Mô tả |
|------|------|
| `php artisan make:auth` *(Laravel <=6)* | Tạo hệ thống xác thực |
| `php artisan ui vue --auth` *(Laravel UI)* | Tạo auth với Vue |
| `php artisan passport:install` | Cài đặt Laravel Passport |
| `php artisan jwt:secret` | Tạo secret key cho JWT (nếu dùng tymon/jwt-auth) |

## 🧪 Lệnh kiểm thử
| Lệnh | Mô tả |
|------|------|
| `php artisan test` | Chạy toàn bộ test |
| `php artisan test --filter=TestName` | Chạy test cụ thể |

## 🧹 Lệnh dọn dẹp và cache
| Lệnh | Mô tả |
|------|------|
| `php artisan config:cache` | Cache file cấu hình |
| `php artisan route:cache` | Cache route |
| `php artisan view:clear` | Xóa cache view |
| `php artisan cache:clear` | Xóa cache ứng dụng |
| `php artisan config:clear` | Xóa cache cấu hình |
| `php artisan route:clear` | Xóa cache route |
