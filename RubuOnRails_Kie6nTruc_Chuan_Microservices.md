# 🧩 Kiến trúc chuẩn Ruby on Rails Microservices với JWT

Tài liệu này mô tả **cấu trúc chuẩn để triển khai Ruby on Rails theo kiến trúc Microservices**, bao gồm:

* Auth Service (JWT)
* API Gateway
* AppA / AppB (Business Services)
* Giao tiếp HTTP / gRPC
* Chuẩn folder Rails
* Chuẩn bảo mật & deploy

---

## 1️⃣ Tổng quan kiến trúc

```text
Client (Web / Mobile)
        |
        v
+-------------------+
|   API Gateway     |
| (Auth, Routing)   |
+-------------------+
     |        |
     v        v
+---------+  +---------+
| AuthSvc |  |  AppA   |
| (JWT)   |  | Orders  |
+---------+  +---------+
                  |
                  v
              +---------+
              |  AppB   |
              | Payment |
              +---------+
```

---

## 2️⃣ Trách nhiệm từng service

### 🔐 Auth Service

* Đăng ký / đăng nhập user
* Phát hành JWT (Access + Refresh)
* Verify token
* Quản lý Role / Permission

### 🚪 API Gateway

* Entry point duy nhất
* Verify JWT
* Routing request
* Rate limit / Logging

### 🧠 Business Services (AppA, AppB)

* Chỉ xử lý business logic
* Không biết username/password
* Chỉ tin JWT claims

---

## 3️⃣ Chuẩn JWT sử dụng

### 📌 Payload JWT

```json
{
  "sub": "user_id",
  "email": "user@email.com",
  "roles": ["admin"],
  "exp": 1710000000,
  "iat": 1709990000
}
```

### 📌 Best practices

* Access token: 15–30 phút
* Refresh token: 7–30 ngày
* Ký bằng RS256 (RSA)

---

## 4️⃣ Auth Service – Rails structure

```text
auth_service/
├── app/
│   ├── controllers/
│   │   └── api/v1/auth_controller.rb
│   ├── models/
│   │   └── user.rb
│   ├── services/
│   │   ├── jwt_encoder.rb
│   │   └── jwt_decoder.rb
│   └── policies/
├── config/
│   └── routes.rb
└── db/
```

### Ví dụ phát JWT

```ruby
class JwtEncoder
  SECRET = Rails.application.credentials.jwt_private_key

  def self.encode(payload, exp = 15.minutes.from_now)
    payload[:exp] = exp.to_i
    JWT.encode(payload, SECRET, 'RS256')
  end
end
```

---

## 5️⃣ API Gateway – Vai trò, cấu trúc & triển khai

API Gateway là **trái tim của kiến trúc microservices**, đóng vai trò *entry point duy nhất* cho toàn bộ hệ thống.

---

### 🎯 API Gateway làm gì?

| Chức năng      | Mô tả                              |
| -------------- | ---------------------------------- |
| Authentication | Verify JWT (public key)            |
| Authorization  | Check role / scope                 |
| Routing        | Điều hướng request đến AppA / AppB |
| Aggregation    | Gộp response từ nhiều service      |
| Rate limiting  | Chống abuse                        |
| Logging        | Trace request                      |
| Versioning     | /v1, /v2                           |

❗ **Business logic KHÔNG nằm ở Gateway**

---

## 5.1️⃣ Các cách triển khai API Gateway

### Option 1️⃣: Rails API Gateway (Phổ biến nhất với Rails team)

**Khi dùng**:

* Team Ruby mạnh
* Cần custom logic
* Quy mô vừa

```text
api_gateway/
├── app/
│   ├── controllers/
│   │   └── api/v1/proxy_controller.rb
│   ├── middleware/
│   │   ├── jwt_auth.rb
│   │   └── rate_limit.rb
│   ├── services/
│   │   └── service_router.rb
│   └── concerns/
│       └── current_user.rb
├── config/
│   └── routes.rb
```

---

### Option 2️⃣: Nginx + Lua (Hiệu năng cao)

* Verify JWT ở edge
* Routing cực nhanh
* Khó maintain

---

### Option 3️⃣: Kong / APISIX (Enterprise)

* Plugin-based
* Observability mạnh
* Phức tạp

---

## 5.2️⃣ Cấu trúc Rails API Gateway (Recommended)

### routes.rb

```ruby
namespace :api do
  namespace :v1 do
    post '/auth/login', to: 'auth#login'

    scope '/orders' do
      match '*path', to: 'proxy#orders', via: :all
    end

    scope '/payments' do
      match '*path', to: 'proxy#payments', via: :all
    end
  end
end
```

---

### Middleware xác thực JWT

```ruby
class JwtAuth
  def initialize(app)
    @app = app
  end

  def call(env)
    req = Rack::Request.new(env)
    token = req.get_header('HTTP_AUTHORIZATION')&.split(' ')&.last

    payload = JwtDecoder.decode(token)
    env['current_user'] = payload

    @app.call(env)
  rescue
    [401, {}, ['Unauthorized']]
  end
end
```

---

### Proxy Controller

```ruby
class ProxyController < ApplicationController
  def orders
    forward_to('http://app-a:3000')
  end

  def payments
    forward_to('http://app-b:3000')
  end

  private

  def forward_to(base_url)
    response = Faraday.send(
      request.method.downcase,
      "#{base_url}#{request.fullpath}",
      request.body.read,
      headers
    )

    render plain: response.body, status: response.status
  end

  def headers
    {
      'Authorization' => request.headers['Authorization'],
      'Content-Type' => 'application/json'
    }
  end
end
```

---

## 5.3️⃣ Flow request chi tiết

```text
Client
  │
  │ 1. Authorization: Bearer JWT
  ▼
API Gateway
  │
  │ 2. Verify JWT (public key)
  │ 3. Rate limit
  │ 4. Route
  ▼
AppA / AppB
  │
  │ 5. Business logic
  ▼
Response
```

---

## 5.4️⃣ Deploy API Gateway

### Dockerfile (Rails API)

```dockerfile
FROM ruby:3.3
WORKDIR /app
COPY . .
RUN bundle install
CMD ["bundle", "exec", "puma", "-C", "config/puma.rb"]
```

---

### docker-compose.yml (local)

```yaml
services:
  gateway:
    build: ./api_gateway
    ports:
      - "3000:3000"

  auth:
    build: ./auth_service

  app_a:
    build: ./app_a

  app_b:
    build: ./app_b
```

---

## 5.5️⃣ Best Practices cho API Gateway

✅ Stateless
✅ Không truy DB business
✅ Chỉ dùng public key
✅ Timeout ngắn
✅ Circuit breaker

---

### Option phổ biến

| Tool        | Khi dùng           |
| ----------- | ------------------ |
| Rails API   | Nhẹ, dễ custom     |
| Nginx + Lua | Hiệu năng cao      |
| Kong        | Enterprise, plugin |

### Flow verify token

```ruby
before_action :authenticate!

def authenticate!
  token = request.headers['Authorization']&.split(' ')&.last
  payload = JwtDecoder.decode(token)
  @current_user = payload
rescue
  render json: { error: 'Unauthorized' }, status: 401
end
```

---

## 6️⃣ AppA / AppB – Business service

### Nguyên tắc

* ❌ Không lưu user table
* ✅ Dùng `current_user` từ JWT
* ✅ Validate permission

### Folder chuẩn

```text
app_a/
├── app/
│   ├── controllers/
│   │   └── api/v1/orders_controller.rb
│   ├── services/
│   ├── policies/
│   └── models/
```

### Ví dụ controller

```ruby
class OrdersController < ApplicationController
  before_action :authorize_user

  def index
    render json: Order.where(user_id: current_user['sub'])
  end
end
```

---

## 7️⃣ Giao tiếp giữa services

### HTTP (JSON)

* Dễ debug
* Chậm hơn

### gRPC

* Nhanh
* Schema rõ ràng

```text
API Gateway → gRPC → AppA
```

---

## 8️⃣ Database per service

| Service | Database              |
| ------- | --------------------- |
| Auth    | users, refresh_tokens |
| AppA    | orders                |
| AppB    | payments              |

❗ Không join cross-service

---

## 9️⃣ Bảo mật

* HTTPS everywhere
* JWT public key share
* Rotate key
* Rate limit
* Audit log

---

## 🔟 Deploy (AWS ví dụ)

```text
ALB
 └── API Gateway
      ├── Auth Service
      ├── AppA
      └── AppB
```

* Docker + ECS / EKS
* Secret Manager
* CI/CD độc lập

---

## 1️⃣1️⃣ So sánh Monolith vs Microservices

|            | Monolith | Microservices |
| ---------- | -------- | ------------- |
| Scale      | ❌        | ✅             |
| Deploy     | ❌        | ✅             |
| Complexity | ✅        | ❌             |

---

## 1️⃣2️⃣ Khi nào KHÔNG nên dùng

* Team nhỏ
* Business chưa rõ
* Overhead DevOps lớn

---

## 📎 Kết luận

👉 Rails Microservices + JWT phù hợp khi:

* Team lớn
* Hệ thống scale
* Nhiều client (Web / Mobile)

---

✍️ Author: Rails Architecture Guide
