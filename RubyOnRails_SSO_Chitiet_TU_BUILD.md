# 📘 Tài liệu chi tiết: Thiết kế Single Sign-On (SSO) trong Ruby on Rails

## 1. SSO là gì?

**Single Sign-On (SSO)** là cơ chế cho phép người dùng **đăng nhập một lần** nhưng có thể truy cập **nhiều hệ thống / ứng dụng khác nhau** mà không cần đăng nhập lại.

Ví dụ:

* Đăng nhập Google → dùng Gmail, YouTube, Google Drive
* Công ty có: HR system, CRM, Admin portal → login 1 lần

---

## 2. Khi nào cần SSO trong Rails?

Nên dùng SSO khi:

* Có **nhiều ứng dụng Rails** dùng chung user
* Rails + Mobile App + Frontend SPA (Vue/React)
* Muốn tích hợp đăng nhập bằng Google, Azure AD, Keycloak, Okta
* Microservices / hệ thống phân tán

Không nên dùng nếu:

* Ứng dụng nhỏ, 1 app duy nhất
* Không có nhu cầu tích hợp bên ngoài

---

## 3. Các mô hình SSO phổ biến

### 3.1 OAuth2 / OpenID Connect (Khuyên dùng)

👉 Chuẩn hiện đại, bảo mật cao

Provider:

* Google
* Azure AD
* Keycloak
* Auth0
* Okta

Rails đóng vai trò:

* **Client / Relying Party**

---

### 3.2 Central Authentication Server (CAS)

* Có **1 server auth trung tâm**
* Các app redirect về server này để login

❌ Ít dùng cho dự án mới

---

### 3.3 JWT-based Internal SSO

* Công ty tự xây Auth Service
* Phát hành JWT token

⚠️ Cần đội ngũ backend mạnh

---

## 4. Kiến trúc SSO tiêu chuẩn (OAuth2 / OIDC)

```
[ User ]
   ↓
[ Browser / App ]
   ↓ redirect
[ Identity Provider ] (Google / Keycloak)
   ↓ code
[ Rails App ]
   ↓ token
[ Backend APIs ]
```

---

## 5. Thiết kế SSO trong Ruby on Rails (Chi tiết)

### 5.1 Các thành phần chính

| Thành phần        | Vai trò                  |
| ----------------- | ------------------------ |
| Identity Provider | Xác thực người dùng      |
| Rails App         | Client + quản lý session |
| User Model        | Lưu thông tin user       |
| Token             | Xác thực giữa services   |

---

## 6. OAuth2 Flow (Authorization Code)

### 6.1 Các bước

1. User click **Login with SSO**
2. Rails redirect sang IdP
3. User login tại IdP
4. IdP trả về `authorization_code`
5. Rails đổi code → access_token
6. Lấy user info
7. Tạo session hoặc JWT

---

## 7. Triển khai SSO trong Rails bằng OmniAuth

### 7.1 Gem cần dùng

```ruby
gem 'omniauth'
gem 'omniauth-google-oauth2'
```

---

### 7.2 Cấu hình OmniAuth

```ruby
# config/initializers/omniauth.rb
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :google_oauth2,
           ENV['GOOGLE_CLIENT_ID'],
           ENV['GOOGLE_CLIENT_SECRET'],
           scope: 'email profile'
end
```

---

### 7.3 Routes

```ruby
get '/auth/:provider/callback', to: 'sessions#create'
get '/auth/failure', to: redirect('/')
```

---

### 7.4 Sessions Controller

```ruby
class SessionsController < ApplicationController
  def create
    auth = request.env['omniauth.auth']

    user = User.find_or_create_by(email: auth.info.email) do |u|
      u.name = auth.info.name
      u.provider = auth.provider
      u.uid = auth.uid
    end

    session[:user_id] = user.id
    redirect_to root_path
  end
end
```

---

## 8. Thiết kế Database cho SSO

```ruby
create_table :users do |t|
  t.string :email
  t.string :name
  t.string :provider
  t.string :uid
  t.timestamps
end
```

---

## 9. SSO cho nhiều Rails App

### 9.1 Cách 1: Shared Identity Provider

* Tất cả app dùng chung Google / Keycloak
* Mỗi app là OAuth client riêng

✅ Dễ mở rộng

---

### 9.2 Cách 2: Central Auth Service

* 1 Rails app làm Auth Server
* App khác verify JWT

```
[ Auth Service ] → JWT
        ↓
[ Rails App A ]
[ Rails App B ]
```

---

## 10. JWT trong SSO nội bộ

### 10.1 Tạo JWT

```ruby
token = JWT.encode(
  { user_id: user.id, exp: 24.hours.from_now.to_i },
  Rails.application.secret_key_base
)
```

### 10.2 Verify JWT

```ruby
decoded = JWT.decode(token, secret)
```

---

## 11. SSO với Frontend (Vue.js)

Flow:

1. Vue redirect sang IdP
2. Rails nhận callback
3. Rails trả JWT
4. Vue lưu token (memory / cookie)

⚠️ Không lưu JWT trong localStorage nếu có XSS

---

## 12. Logout trong SSO

Có 3 cấp độ:

* Logout local Rails
* Logout IdP
* Global logout (OIDC)

---

## 13. Bảo mật khi thiết kế SSO

✔ HTTPS bắt buộc
✔ Validate state param
✔ Token expiration
✔ Refresh token
✔ Scope tối thiểu
✔ CSRF protection

---

## 14. So sánh các giải pháp SSO

| Giải pháp         | Phù hợp              |
| ----------------- | -------------------- |
| OmniAuth + Google | App nhỏ – trung bình |
| Keycloak          | Enterprise           |
| Auth0             | SaaS nhanh           |
| Custom JWT        | Nội bộ               |

---

## 15. Best Practices

* Không tự viết OAuth nếu không cần
* Tách Auth Service nếu scale lớn
* Log & audit đăng nhập
* Rotate secret định kỳ

---

## 16. Tóm tắt ngắn gọn

> **SSO trong Rails nên dùng OAuth2 / OpenID Connect với OmniAuth hoặc Keycloak. Chỉ tự xây JWT SSO khi hệ thống lớn và kiểm soát tốt bảo mật.**

---

## 17. Identity Provider (IdP) là gì? (Chi tiết)

### 17.1 IdP đóng vai trò gì?

Identity Provider (IdP) là **hệ thống chịu trách nhiệm xác thực người dùng** và phát hành **identity / token** cho các ứng dụng khác (Rails app, mobile, frontend).

IdP làm các việc:

* Lưu user gốc (email, password, MFA, social login)
* Xác thực (login, logout)
* Phát hành token (Authorization Code, Access Token, ID Token)
* Quản lý session SSO
* Quản lý role / group (tuỳ hệ thống)

---

## 18. Database trong IdP được thiết kế như thế nào?

> ⚠️ Quan trọng: **Rails App KHÔNG lưu password khi dùng SSO**

### 18.1 Các bảng cốt lõi trong IdP (khái niệm)

### 👤 users

| field         | ý nghĩa                       |
| ------------- | ----------------------------- |
| id            | user id nội bộ                |
| email         | định danh chính               |
| password_hash | hash mật khẩu (bcrypt/argon2) |
| status        | active / locked               |
| created_at    |                               |

---

### 🔐 credentials / identities

Dùng khi 1 user có nhiều cách login (password, Google, Azure…)

| field        | ý nghĩa                 |
| ------------ | ----------------------- |
| user_id      | liên kết user           |
| provider     | local / google / github |
| provider_uid | uid từ provider         |

---

### 🪪 sessions

| field      | ý nghĩa       |
| ---------- | ------------- |
| user_id    | ai đang login |
| session_id | SSO session   |
| expires_at | hết hạn       |

---

### 🎟 oauth_clients

| field         | ý nghĩa   |
| ------------- | --------- |
| client_id     | app Rails |
| client_secret | secret    |
| redirect_uri  | callback  |
| scopes        | quyền     |

---

### 🎫 oauth_tokens

| field         | ý nghĩa   |
| ------------- | --------- |
| access_token  | token     |
| refresh_token | refresh   |
| user_id       | chủ token |
| client_id     | app       |
| expires_at    | hạn       |

---

## 19. Rails App lưu database ra sao khi dùng SSO?

### 19.1 Nguyên tắc

✔ Rails app **KHÔNG lưu password**
✔ Rails app chỉ lưu **identity mapping**

---

### 19.2 Bảng users trong Rails

```ruby
create_table :users do |t|
  t.string :email
  t.string :name
  t.string :provider
  t.string :uid
  t.timestamps
end
```

Giải thích:

* `provider`: google / keycloak / azure
* `uid`: id user bên IdP

---

## 20. Các loại Identity Provider phổ biến

### 20.1 Social IdP

| IdP      | Khi dùng     |
| -------- | ------------ |
| Google   | App public   |
| GitHub   | Dev tools    |
| Facebook | Consumer app |

---

### 20.2 Enterprise IdP

| IdP      | Đặc điểm               |
| -------- | ---------------------- |
| Keycloak | Open-source, self-host |
| Azure AD | Doanh nghiệp Microsoft |
| Okta     | SaaS enterprise        |
| Auth0    | Nhanh, trả phí         |

---

### 20.3 Internal / Custom IdP

* Công ty tự xây Auth Service
* Dùng OAuth2 / OIDC
* Phát JWT

Dùng khi:

* Microservices
* Yêu cầu bảo mật cao

---

## 21. SSO Flow chi tiết (Authorization Code + OIDC)

```
User → Rails App → Redirect → IdP
User login tại IdP
IdP → authorization_code
Rails → IdP (exchange code)
Rails ← access_token + id_token
Rails → create session / JWT
```

---

## 22. ID Token vs Access Token (Rất quan trọng)

| Token        | Dùng để               |
| ------------ | --------------------- |
| ID Token     | Thông tin user (OIDC) |
| Access Token | Gọi API               |

Rails thường:

* Dùng **ID Token** để tạo user
* Dùng **Access Token** để gọi service

---

## 23. Multi-App SSO (Nhiều Rails App)

### 23.1 Shared IdP

* 1 IdP
* N Rails app

Lợi ích:

* Login 1 lần
* Quản lý tập trung

---

### 23.2 User mapping

```
IdP user_id = 123
Rails A user.uid = 123
Rails B user.uid = 123
```

---

## 24. Logout hoạt động như thế nào?

### 24.1 Local logout

* Xoá session Rails

### 24.2 IdP logout

* Xoá session IdP

### 24.3 Global logout (OIDC)

* Logout toàn hệ thống

---

## 25. Bảo mật ở mức kiến trúc

✔ PKCE (bắt buộc cho SPA)
✔ Rotate client_secret
✔ Encrypt token
✔ MFA tại IdP
✔ Audit log login

---

## 26. Khi nào nên tự xây IdP?

| Nên                | Không nên              |
| ------------------ | ---------------------- |
| >10 services       | App nhỏ                |
| Microservices      | Team nhỏ               |
| Yêu cầu compliance | Không có security team |

---

## 27. Mapping kiến trúc thực tế

| Kiến trúc      | Gợi ý       |
| -------------- | ----------- |
| Rails monolith | OmniAuth    |
| Rails + Vue    | OIDC + PKCE |
| Multi Rails    | Keycloak    |
| Enterprise     | Azure AD    |

---

📌 Nếu bạn muốn:

* Mình sẽ **vẽ sơ đồ database IdP**
* So sánh **Keycloak vs tự build**
* Thiết kế **Auth Service bằng Rails**
* Viết **SSO checklist cho production**

---

# 🧠 PHẦN CHUYÊN SÂU: TỰ BUILD IDENTITY PROVIDER (IdP) BẰNG RUBY ON RAILS

## 28. Tổng quan kiến trúc Auth Service (IdP tự build)

### 28.1 Mục tiêu của Auth Service

Auth Service là **một Rails application độc lập**, chịu trách nhiệm:

* Quản lý user gốc (source of truth)
* Xác thực (login, logout, MFA)
* Phát hành token (OAuth2 / OIDC)
* Là trung tâm SSO cho toàn hệ thống

```
[ User ]
   ↓
[ Browser / Mobile / SPA ]
   ↓
[ Auth Service (Rails IdP) ]
   ↓ JWT / Token
[ Rails App A | Rails App B | API ]
```

---

## 29. Phạm vi mà IdP tự build PHẢI đảm nhiệm

### 29.1 Những gì IdP **bắt buộc** phải làm

✔ Login / Logout
✔ Password hashing
✔ Token issuing
✔ Token verification
✔ Session management
✔ Account lock / revoke

### 29.2 Những gì **không nên** làm trong app business

❌ Login logic
❌ Password storage
❌ Token generate

---

## 30. Thiết kế Database chi tiết cho Auth Service

### 30.1 users (nguồn sự thật)

```ruby
create_table :users do |t|
  t.string :email, null: false
  t.string :password_digest
  t.string :status, default: 'active'
  t.datetime :last_login_at
  t.timestamps
end
```

Giải thích:

* `password_digest`: bcrypt / argon2
* `status`: active / locked / disabled

---

### 30.2 identities (đa phương thức đăng nhập)

```ruby
create_table :identities do |t|
  t.references :user
  t.string :provider
  t.string :provider_uid
  t.timestamps
end
```

Dùng khi:

* Password + Google + Azure

---

### 30.3 oauth_clients (ứng dụng đăng ký)

```ruby
create_table :oauth_clients do |t|
  t.string :client_id
  t.string :client_secret
  t.string :redirect_uri
  t.string :scopes
  t.timestamps
end
```

---

### 30.4 oauth_authorization_codes

```ruby
create_table :oauth_authorization_codes do |t|
  t.string :code
  t.references :user
  t.references :oauth_client
  t.datetime :expires_at
end
```

---

### 30.5 oauth_tokens

```ruby
create_table :oauth_tokens do |t|
  t.string :access_token
  t.string :refresh_token
  t.references :user
  t.references :oauth_client
  t.datetime :expires_at
end
```

---

### 30.6 revoked_tokens (thu hồi token)

```ruby
create_table :revoked_tokens do |t|
  t.string :jti
  t.datetime :revoked_at
end
```

---

## 31. OAuth2 / OIDC Flow – Chi tiết từng bước

### 31.1 Authorization Code Flow

1. Client redirect user đến `/authorize`
2. User login tại Auth Service
3. Auth Service tạo authorization_code
4. Redirect về client
5. Client đổi code → token

---

## 32. Token Design (CỰC KỲ QUAN TRỌNG)

### 32.1 Access Token (JWT)

```json
{
  "sub": "user_id",
  "aud": "client_id",
  "exp": 1712345678,
  "scope": "profile email"
}
```

✔ Ngắn hạn (15–30 phút)

---

### 32.2 Refresh Token

* Lưu DB
* Hạn dài
* Rotate mỗi lần dùng

---

## 33. Ký và verify JWT

```ruby
JWT.encode(payload, private_key, 'RS256')
```

✔ Dùng RSA key
✔ Public key để app khác verify

---

## 34. Rails App khác verify token thế nào?

```ruby
JWT.decode(token, public_key, true, algorithm: 'RS256')
```

Rails App:

* Không gọi Auth Service mỗi request
* Chỉ verify chữ ký

---

## 35. Session vs Token (SSO thực tế)

| Trường hợp | Dùng             |
| ---------- | ---------------- |
| Browser    | Session + Cookie |
| Mobile     | JWT              |
| API        | JWT              |

---

## 36. Logout & Revoke Token

* Logout browser → xoá session
* Logout global → revoke refresh token
* Blacklist access token (jti)

---

## 37. Bảo mật bắt buộc khi tự build IdP

🚨 Nếu thiếu → NGUY HIỂM

✔ HTTPS
✔ PKCE
✔ Rotate refresh token
✔ Rate limit login
✔ MFA (TOTP)
✔ Audit log

---

## 38. Sai lầm chết người khi tự build IdP

❌ Dùng HS256 chung secret
❌ Không revoke token
❌ Không kiểm tra audience
❌ Access token quá dài hạn

---

## 39. Khi nào KHÔNG nên tự build IdP?

* Team nhỏ
* Thiếu security knowledge
* Không có audit/compliance

---

## 40. Kết luận thực tế (RẤT QUAN TRỌNG)

> **Tự build IdP = tự chịu trách nhiệm bảo mật. Chỉ nên làm khi bạn hiểu rõ OAuth2 / OIDC, JWT, cryptography và vận hành production.**

---

📌 Nếu bạn muốn tiếp:

* Vẽ **sequence diagram OAuth2**
* Viết **Auth Service skeleton Rails**
* Checklist **production security**
* So sánh **Keycloak vs tự build (thực tế)**
