# Gem `rack-cors` trong Ruby / Rails

`rack-cors` là một **middleware** cho ứng dụng Ruby/Rails dùng để xử lý **Cross-Origin Resource Sharing (CORS)**, cho phép các request AJAX từ domain khác truy cập API của bạn một cách an toàn.

---

## 🔎 `rack-cors` là gì?

* **Middleware cho Rack**
  `rack-cors` hoạt động như một lớp trung gian trong ứng dụng Rack (bao gồm **Rails, Sinatra**, v.v.).

* **Xử lý CORS**
  Nó thêm các HTTP headers cần thiết như:

  * `Access-Control-Allow-Origin`
  * `Access-Control-Allow-Methods`
  * `Access-Control-Allow-Headers`

* **Giải quyết vấn đề frontend – backend khác domain**
  Ví dụ:

  * Frontend chạy tại `http://localhost:3000`
  * Backend API chạy tại `http://localhost:4000`

  Nếu không có CORS → **trình duyệt sẽ chặn request**.
  `rack-cors` giúp bạn định nghĩa chính sách cho phép các request này.

---

## ⚙️ Cách sử dụng cơ bản

### 1️⃣ Cài đặt gem

```ruby
gem 'rack-cors'
```

Chạy:

```bash
bundle install
```

---

### 2️⃣ Cấu hình trong Rails

Tạo (hoặc chỉnh sửa) file:

```
config/initializers/cors.rb
```

Nội dung:

```ruby
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'http://localhost:3000'  # domain được phép

    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end
```

---

### 3️⃣ Kết quả

Khi frontend gửi request:

* Server sẽ phản hồi kèm **CORS headers**
* Trình duyệt chấp nhận response
* Frontend có thể đọc dữ liệu từ API

---

## 📊 Ưu điểm & Lưu ý

| Nội dung      | Mô tả                                                         |
| ------------- | ------------------------------------------------------------- |
| Dễ cấu hình   | Chỉ cần 1 file initializer                                    |
| Linh hoạt     | Cho phép cấu hình theo domain, path, HTTP method              |
| `origins '*'` | Cho phép tất cả domain (⚠️ không khuyến khích cho production) |

---

## 🚨 Rủi ro & Best Practices

### ❌ Rủi ro bảo mật

* Cho phép mọi domain (`origins '*'`) có thể:

  * Bị lạm dụng API
  * Bị gọi từ website độc hại
  * Dễ bị khai thác nếu API không có auth chặt chẽ

---

### ✅ Best Practices

* **Chỉ định rõ domain frontend**:

```ruby
origins 'https://myapp.com'
```

* **Tách theo môi trường**:

  * Development: có thể mở rộng (`*`)
  * Production: giới hạn domain rõ ràng

* **Kết hợp với authentication**:

  * JWT / OAuth2 / API Key
  * Không phụ thuộc CORS để bảo mật

---

## 👉 Tóm lại

* `rack-cors` giúp ứng dụng **Ruby/Rails xử lý CORS**
* Cho phép frontend và backend **khác domain giao tiếp qua AJAX**
* Cần cấu hình **cẩn thận trong production** để tránh rủi ro bảo mật

📌 *CORS là hàng rào của trình duyệt, không phải cơ chế bảo mật backend – hãy luôn kết hợp xác thực!*

