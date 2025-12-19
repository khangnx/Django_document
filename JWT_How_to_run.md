# Ruby on Rails với JWT cho hệ thống Microservices SSO

## 🧩 Các thành phần chính của JWT
JWT gồm 3 phần, nối bằng dấu chấm (`.`):

1. **Header**
   - Chứa thuật toán ký (`alg`) và loại token (`typ`).
   - Ví dụ:
     ```json
     {
       "alg": "HS256",
       "typ": "JWT"
     }
     ```

2. **Payload (Claims)**
   - Chứa dữ liệu về user và quyền hạn.
   - Loại claims:
     - **Registered claims**: chuẩn (iss, exp, sub, aud).
     - **Public claims**: định nghĩa chung.
     - **Private claims**: tùy chỉnh (user_id, role, scopes).
   - Ví dụ:
     ```json
     {
       "sub": "1234567890",
       "name": "Nguyen Van A",
       "role": "admin",
       "exp": 1734600000
     }
     ```

3. **Signature**
   - Được tạo bằng cách mã hóa `Header + Payload` với secret/private key.
   - Đảm bảo token không bị giả mạo.

---

## 🔑 Nguyên lý chính khi dùng JWT cho SSO trong microservices
- **Auth Service (Identity Provider):**
  Rails app chuyên trách đăng nhập, xác thực user. Sau khi thành công, phát hành JWT.
- **JWT Token:**
  - Chứa thông tin user (ID, roles, scopes).
  - Được ký bằng secret hoặc private key (HS256 hoặc RS256).
  - Có thời hạn (expiration).
- **Microservices:**
  - Nhận request kèm JWT trong header (`Authorization: Bearer <token>`).
  - Xác thực chữ ký và hạn token.
- **SSO Flow:**
  Người dùng đăng nhập một lần tại Auth Service → nhận JWT → dùng JWT để truy cập nhiều microservice.

---

## ⚙️ Các bước triển khai trong Rails

1. **Tạo Rails API-only app cho Auth Service**
   ```bash
   rails new auth_service --api'
   ```
  ### Thêm gem
  
  ```
  gem 'bcrypt'
  gem 'jwt'
```

2. ** Sinh JWT khi login thành công

```
payload = { user_id: user.id, exp: (Time.now + 2.hours).to_i }
token = JWT.encode(payload, Rails.application.secret_key_base, 'HS256')
render json: { token: token }
```

3. **Xác thực JWT ở microservice khác

```
def authenticate_request
  header = request.headers['Authorization']
  token = header.split(' ').last if header
  begin
    decoded = JWT.decode(token, Rails.application.secret_key_base, true, { algorithm: 'HS256' })
    @current_user_id = decoded[0]['user_id']
  rescue JWT::DecodeError
    render json: { error: 'Unauthorized' }, status: :unauthorized
  end
end

```

4. ** SSO mở rộng với OAuth/OpenID Connect Có thể tích hợp với Auth0, Okta, hoặc OmniAuth để quản lý danh tính tập trung.


===========================================================================

# 🔄 Luồng đi của một request trong hệ thống SSO microservices

## 1. Client login
- Người dùng gửi thông tin đăng nhập (username/password) đến **Auth Service**.

## 2. Auth Service xác thực
- Auth Service kiểm tra thông tin đăng nhập.
- Nếu hợp lệ → tạo JWT (chứa user_id, role, exp).
- Trả JWT về cho client.

## 3. Client gọi microservice khác
- Client gửi request đến microservice A/B/C.
- JWT được đính kèm trong header:
```
Authorization: Bearer <jwt_token>
```

## 4. Microservice xác thực JWT
- Microservice nhận request → kiểm tra chữ ký JWT bằng secret/public key.
- Nếu hợp lệ và chưa hết hạn → giải mã payload → lấy thông tin user.
- Nếu không hợp lệ → trả về `401 Unauthorized`.

## 5. Microservice xử lý logic
- Dựa vào claims (role, scopes) để quyết định quyền truy cập.
- Thực hiện nghiệp vụ và trả kết quả về cho client.

---

## 🌐 Giao thức sử dụng giữa các microservices
- **HTTP/HTTPS (REST API):** phổ biến nhất, JWT truyền qua header.
- **gRPC (HTTP/2):** hiệu năng cao, JWT truyền qua metadata.
- **Message Queue (Kafka, RabbitMQ):** JWT có thể nhúng vào message header trong hệ thống event-driven.

👉 Thực tế: đa số hệ thống microservices Rails dùng **HTTPS REST API** để giao tiếp.


=============================
   
   
   
   
   
   
   
   
   JWT authentication is a popular method for securing APIs. Here’s a quick rundown of how it works:
# 1. User Login:

When a user logs in with their credentials (like username and password), the Rails backend authenticates them.

# 2. Token Generation:

Upon successful authentication, the backend generates a JWT. This token typically contains a payload with user information (like user ID) and is signed with a secret key.

# 3. Token Storage:

The frontend (Vue.js) receives this token and typically stores it in local storage or a secure cookie.

# 4. Making Authenticated Requests:

For subsequent requests, the frontend includes the JWT in the Authorization header, usually as a Bearer token.

# 5. Token Verification:

The Rails backend verifies the token on each request by checking its signature and ensuring it hasn’t expired.

# 6. Handling Token Expiry:

If the token expires, the frontend will need to prompt the user to log in again or refresh the token if you have a refresh token mechanism in place.


======================================================================================================
  Handling an expired token usually involves one of a few strategies:

# 1. Refresh Tokens:

Alongside the access token (your JWT), you can issue a refresh token. The refresh token has a longer lifespan and can be used to obtain a new access token without requiring the user to log in again.

When the access token expires, the frontend can use the refresh token to request a new access token from the backend.

# 2. Re-login Prompt:

If you don’t use refresh tokens, then once the access token expires, the frontend should prompt the user to log in again. This ensures the user’s session is still secure.

# 3. Silent Token Renewal:

Some applications implement a silent renewal process, where the frontend can automatically use the refresh token in the background to get a new access token without interrupting the user’s experience.

# 4. Error Handling:

On the frontend, you can handle token expiration by checking for specific error responses from the backend (like a 401 Unauthorized status). When you get such a response, you can either attempt a token refresh or redirect the user to the login page.



==============================================



======================

# JWT Authentication trong Ruby on Rails

Tài liệu này mô tả **đầy đủ quy trình triển khai JWT (JSON Web Token)** trong Ruby on Rails, dùng cho REST API, Microservices, API Gateway.

---

## 1. JWT là gì?

JWT (JSON Web Token) là chuẩn mở dùng để truyền thông tin an toàn giữa client và server dưới dạng token.

### Cấu trúc JWT gồm 3 phần:
- **Header**  
  Chứa thông tin thuật toán ký (ví dụ: HS256)

- **Payload**  
  Chứa dữ liệu người dùng:
  - user_id
  - role
  - exp (expiration time)

- **Signature**  
  Dùng để đảm bảo token không bị thay đổi

---

## 2. Quy trình hoạt động JWT

### 🔑 Sinh JWT khi Login
1. Client gửi `email` + `password`
2. Server xác thực user
3. Nếu hợp lệ:
   - Sinh **Access Token** (ngắn hạn)
   - Sinh **Refresh Token** (dài hạn)
4. Trả token về client

---

### ✅ Kiểm tra JWT khi gọi API
1. Client gửi request kèm header:
   ```
   Authorization: Bearer <access_token>
   ```
2. Server:
   - Giải mã token
   - Kiểm tra chữ ký
   - Kiểm tra hạn sử dụng (exp)
3. Nếu hợp lệ → cho phép truy cập

---

### 🔄 Refresh Token
1. Access Token hết hạn
2. Client gửi `refresh_token`
3. Server kiểm tra refresh token
4. Nếu hợp lệ → cấp access token mới
5. Nếu refresh token hết hạn → yêu cầu login lại

---

## 3. Cài đặt gem JWT

```ruby
# Gemfile
gem 'jwt'
```

```bash
bundle install
```

---

## 4. Service xử lý JWT

**File:** `app/lib/json_web_token.rb`

```ruby
class JsonWebToken
  SECRET_KEY = Rails.application.secret_key_base

  def self.encode(payload, exp = 15.minutes.from_now)
    payload[:exp] = exp.to_i
    JWT.encode(payload, SECRET_KEY)
  end

  def self.decode(token)
    body = JWT.decode(token, SECRET_KEY)[0]
    HashWithIndifferentAccess.new(body)
  rescue JWT::ExpiredSignature, JWT::DecodeError
    nil
  end
end
```

---

## 5. Controller Login (AuthController)

```ruby
class AuthController < ApplicationController
  def login
    user = User.find_by(email: params[:email])

    if user&.authenticate(params[:password])
      access_token  = JsonWebToken.encode({ user_id: user.id })
      refresh_token = JsonWebToken.encode({ user_id: user.id }, 7.days.from_now)

      render json: {
        access_token: access_token,
        refresh_token: refresh_token
      }, status: :ok
    else
      render json: { error: 'Invalid email or password' }, status: :unauthorized
    end
  end
end
```

---

## 6. Middleware kiểm tra JWT

```ruby
class ApplicationController < ActionController::API
  before_action :authorize_request

  private

  def authorize_request
    header = request.headers['Authorization']
    token = header.split(' ').last if header

    decoded = JsonWebToken.decode(token)

    if decoded
      @current_user = User.find(decoded[:user_id])
    else
      render json: { error: 'Unauthorized or token expired' }, status: :unauthorized
    end
  end
end
```

---

## 7. Orders Controller (API có bảo vệ)

```ruby
class OrdersController < ApplicationController
  def get_order
    orders = @current_user.orders

    render json: {
      user: @current_user.email,
      orders: orders.map do |o|
        {
          id: o.id,
          product: o.product_name,
          price: o.price
        }
      end
    }, status: :ok
  end
end
```

---

## 8. Refresh Token Controller

```ruby
class TokensController < ApplicationController
  skip_before_action :authorize_request, only: [:refresh]

  def refresh
    refresh_token = params[:refresh_token]
    decoded = JsonWebToken.decode(refresh_token)

    if decoded
      new_access_token = JsonWebToken.encode({ user_id: decoded[:user_id] })
      render json: { access_token: new_access_token }, status: :ok
    else
      render json: { error: 'Invalid or expired refresh token' }, status: :unauthorized
    end
  end
end
```

---

## 9. Demo Request / Response

### 🔐 Login

**Request**
```http
POST /auth/login
```

```json
{
  "email": "nguyen@example.com",
  "password": "123456"
}
```

**Response**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6..."
}
```

---

### 📦 Gọi API lấy Orders

```http
GET /orders/get_order
Authorization: Bearer <access_token>
```

```json
{
  "user": "nguyen@example.com",
  "orders": [
    { "id": 1, "product": "Laptop Dell", "price": 1200 },
    { "id": 2, "product": "Chuột Logitech", "price": 50 }
  ]
}
```

---

### 🔄 Refresh Token

```http
POST /tokens/refresh
```

```json
{
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6..."
}
```

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6..."
}
```

---

## 10. Sơ đồ luồng hoạt động

```text
[Login]
   ↓
Server cấp Access + Refresh Token
   ↓
Client gọi API bằng Access Token
   ↓
Access Token hết hạn
   ↓
Client gửi Refresh Token
   ↓
Server cấp Access Token mới
```

---

## 11. Best Practices

- Access Token: 10–15 phút
- Refresh Token: 7–30 ngày
- Không lưu JWT trong LocalStorage (ưu tiên HttpOnly Cookie)
- Có thể dùng Redis để blacklist token khi logout

---

## 12. Phù hợp cho

- REST API
- SPA (Vue / React)
- Mobile App
- Microservices
- API Gateway + Auth Service

