# Base Gems trong Ruby on Rails (2025)

Tài liệu tổng hợp đầy đủ các gem nền tảng thường dùng trong dự án Ruby on Rails — từ bảo mật, API, background job, testing, cho đến performance.

---

## 1. Bảo mật & Xác thực

### **Devise**

* Cung cấp authentication đầy đủ: đăng nhập, đăng ký, reset password, confirm email.
* Dùng rộng rãi nhất trong Rails.

### **Pundit / CanCanCan**

* Authorization (phân quyền).
* **Pundit**: dùng dạng policy, sáng sủa và dễ maintain.
* **CanCanCan**: định nghĩa ability cho user.

---

## 2. API Development

### **rack-cors**

* Cho phép cấu hình CORS khi xây dựng API với frontend.

### **jbuilder / active_model_serializers / blueprinter**

* Xây JSON response.
* **Blueprinter**: nhanh, nhẹ, dễ viết hơn AMS.

### **doorkeeper**

* OAuth2 server nếu bạn cần: SSO, login qua app mobile/web.

---

## 3. Background Job

### **Sidekiq**

* Lựa chọn tốt nhất cho background processing.
* Sử dụng Redis, cực nhanh.

### **Sidekiq-cron**

* Chạy job theo lịch (schedule).

---

## 4. Tối ưu code & tiện ích

### **kaminari / will_paginate**

* Pagination cho ActiveRecord.

### **ransack**

* Search/filter mạnh mẽ cho bảng quản trị.

### **carrierwave / shrine / active_storage**

* Upload và xử lý ảnh/file.
* **CarrierWave**: phổ biến, dễ tùy biến.
* **Shrine**: hiện đại, linh hoạt.
* **ActiveStorage**: có sẵn trong Rails.

### **friendly_id**

* Tạo slug URL đẹp.

---

## 5. Hỗ trợ Database

### **pg**

* Gem PostgreSQL.

### **redis**

* Dùng cho cache, session store, Sidekiq backend.

### **paranoia / discarded**

* Soft delete cho ActiveRecord model.
* **discarded** hiện đại hơn.

---

## 6. Testing

### **rspec-rails**

* Testing framework phổ biến nhất.

### **factory_bot_rails**

* Tạo dữ liệu test.

### **faker**

* Sinh dữ liệu giả cho test.

### **capybara**

* Test giao diện.

### **database_cleaner**

* Giữ database sạch khi test.

---

## 7. Performance & Debugging

### **bullet**

* Phát hiện lỗi N+1 query.

### **rack-mini-profiler**

* Đo thời gian query, render view.

### **pry / pry-rails**

* Debug console mạnh.

### **annotate**

* Tự động thêm schema vào đầu file model.

---

## 8. DevOps / Deploy

### **dotenv-rails**

* Quản lý biến môi trường trong file `.env`.

### **whenever**

* Cron job viết bằng Ruby DSL.

### **figaro**

* Quản lý ENV tương tự dotenv (hiện ít dùng hơn).

---

## 9. Front-end Gems

### **importmap-rails / jsbundling-rails / cssbundling-rails**

* Cách pack asset cho Rails 7+ không cần Webpacker.

### **view_component**

* Tổ chức view dạng component, thay thế partial.

---

# Bộ Base Gems chuẩn cho 1 dự án Rails hiện đại

```ruby
# Authentication & Authorization
gem "devise"
gem "pundit"

# API
gem "rack-cors"
gem "blueprinter"

# Background jobs
gem "sidekiq"
gem "sidekiq-cron"

# Database helpers
gem "pg"
gem "redis"
gem "discard"

# Code optimization
gem "kaminari"
gem "ransack"
gem "friendly_id"

gem "carrierwave"

# Dev tools
gem "pry-rails"
gem "annotate"
gem "bullet"
gem "rack-mini-profiler"

# Testing
group :development, :test do
  gem "rspec-rails"
  gem "factory_bot_rails"
  gem "faker"
  gem "capybara"
end
```

---

## Gợi ý thêm

Tôi có thể tạo thêm:

* **Phiên bản Gemfile hoàn chỉnh cho dự án API-only**
* **Tài liệu Markdown giải thích từng gem chi tiết hơn**
* **File hướng dẫn cài đặt & cấu hình cho từng gem**

Bạn muốn mở rộng phần nào không?
