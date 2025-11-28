# TÀI LIỆU ĐẦY ĐỦ: SSO VỚI AWS COGNITO + RUBY ON RAILS + VUE.JS

## 📌 Mục lục
- [1. Tổng quan SSO và Cognito](#1-tổng-quan-sso-và-cognito)
- [2. Kiến trúc tổng thể](#2-kiến-trúc-tổng-thể)
- [3. Cấu hình AWS Cognito cho SSO](#3-cấu-hình-aws-cognito-cho-sso)
- [4. Tích hợp Cognito vào Ruby on Rails (Backend API)](#4-tích-hợp-cognito-vào-ruby-on-rails-backend-api)
- [5. Tích hợp Cognito vào Vue.js (Frontend)](#5-tích-hợp-cognito-vào-vuejs-frontend)
- [6. Luồng SSO đầy đủ](#6-luồng-sso-đầy-đủ)
- [7. Middleware xác thực JWT cho Rails](#7-middleware-xác-thực-jwt-cho-rails)
- [8. Demo luồng token trên Vue.js](#8-demo-luồng-token-trên-vuejs)
- [9. Best Practices](#9-best-practices)

---

# 1. Tổng quan SSO và Cognito

**SSO (Single Sign-On)** cho phép người dùng đăng nhập 1 lần và dùng được cho nhiều ứng dụng trong cùng hệ sinh thái.

AWS Cognito hỗ trợ SSO thông qua:
- **OAuth 2.0**
- **OpenID Connect (OIDC)**
- Hosted UI
- Social Login (Google, Facebook)
- Enterprise Login (SAML)

Cấu trúc SSO:
- Người dùng đăng nhập tại Cognito Hosted UI
- Cognito trả về `authorization code`
- Vue.js đổi code lấy token
- Rails xác thực token

---

# 2. Kiến trúc tổng thể

```
[User]
   ↓
[Vue.js Frontend]
   ↓ OAuth Redirect
[AWS Cognito Hosted UI]
   ↓ Return Code
[Vue.js]
   ↓ Exchange code
[AWS Cognito Token Endpoint]
   ↓
[ID Token / Access Token / Refresh Token]
   ↓
[Ruby on Rails API Server]
   ↓ Verify JWT
[Protected Resources]
```

---

# 3. Cấu hình AWS Cognito cho SSO

### Bước 1 — Tạo User Pool
- Email làm username
- Bật MFA nếu cần
- Create App Client

### Bước 2 — Bật Hosted UI
- Domain: `my-company.auth.ap-southeast-1.amazoncognito.com`
- Allowed OAuth Flows:
  - `code`
- Allowed OAuth Scopes:
  - `openid`
  - `email`
  - `profile`

### Bước 3 — Redirect URIs
Thêm:
- `https://myapp.com/callback`
- `http://localhost:5173/callback` (Vue dev)

---

# 4. Tích hợp Cognito vào Ruby on Rails (Backend API)

Rails chỉ cần xử lý **xác thực JWT** từ Cognito.

### Install gem
```ruby
gem "jwt"
gem "aws-sdk-cognitoidentityprovider"
```

### Middleware verify token
(chi tiết ở phần 7)

### Controller sử dụng token
```ruby
class ProfileController < ApplicationController
  def me
    user = request.env["cognito.user"]
    render json: { user: user }
  end
end
```

---

# 5. Tích hợp Cognito vào Vue.js (Frontend)

## Cài aws-amplify
```bash
npm install aws-amplify
```

## Cấu hình Amplify
```js
import { Auth } from "aws-amplify";

Auth.configure({
  region: "ap-southeast-1",
  userPoolId: "ap-southeast-1_XXXXXX",
  userPoolWebClientId: "XXXXXXXXXXXX",
  oauth: {
    domain: "mydomain.auth.ap-southeast-1.amazoncognito.com",
    redirectSignIn: "http://localhost:5173/callback",
    redirectSignOut: "http://localhost:5173/",
    scope: ["openid", "email", "profile"],
    responseType: "code"
  }
});
```

## Button mở Hosted UI login
```js
Auth.federatedSignIn();
```

## Đổi Code → Token sau login
```js
Auth.currentSession().then(session => {
  console.log("ID Token:", session.getIdToken().getJwtToken());
});
```

---

# 6. Luồng SSO đầy đủ

1. Vue → `Auth.federatedSignIn()`  
2. Cognito Hosted UI xuất hiện  
3. User login  
4. Cognito redirect về `/callback?code=XXXXX`  
5. Vue gọi `Auth.currentSession()` để lấy token  
6. Vue gửi token sang Rails  
7. Rails verify token bằng JWT  
8. Rails trả dữ liệu protected

---

# 7. Middleware xác thực JWT cho Rails

```ruby
class CognitoJWT
  def initialize(app)
    @app = app
    jwks_url = "https://cognito-idp.#{ENV["COGNITO_REGION"]}.amazonaws.com/#{ENV["COGNITO_USER_POOL_ID"]}/.well-known/jwks.json"
    @jwks = JSON.parse(Net::HTTP.get(URI(jwks_url)))
  end

  def call(env)
    auth = env["HTTP_AUTHORIZATION"]
    if auth&.start_with?("Bearer ")
      token = auth.split(" ").last
      payload = decode(token)
      env["cognito.user"] = payload
    end
    @app.call(env)
  rescue JWT::DecodeError => e
    [401, { "Content-Type" => "application/json" }, [{ error: e.message }.to_json]]
  end

  def decode(token)
    header = JWT.decode(token, nil, false).last
    kid = header["kid"]
    key = @jwks["keys"].find { |k| k["kid"] == kid }
    raise "Key not found" unless key

    public_key = OpenSSL::PKey::RSA.new(
      Base64.decode64(key["n"]),
      Base64.decode64(key["e"])
    )

    JWT.decode(token, public_key, true, algorithm: "RS256").first
  end
end
```

---

# 8. Demo luồng token trên Vue.js

```js
async function getProfile() {
  const session = await Auth.currentSession()
  const token = session.getIdToken().getJwtToken()

  const res = await fetch("https://api.myapp.com/profile/me", {
    headers: {
      Authorization: `Bearer ${token}`
    }
  })

  console.log(await res.json())
}
```

---

# 9. Best Practices

### 🔒 Bảo mật
- Không lưu Access Token trong localStorage → dùng Vuex/Pinia + memory
- Refresh Token chỉ nên xử lý bên Frontend
- Bật rotation refresh token trong Cognito

### 🎯 Kiến trúc
- Vue.js chịu trách nhiệm login → token  
- Rails chỉ verify & serve API  
- Cognito chịu trách nhiệm SSO  

### 🔁 Tách môi trường
- dev / staging / production cần khác domain Hosted UI và redirect URL

---

# ✔ Kết luận

Tài liệu này giúp bạn triển khai SSO đầy đủ giữa AWS Cognito, Ruby on Rails và Vue.js.

# Flow tổng quan dưới dạng Sequence Diagram (ASCII)
```
User                   Vue.js SPA             AWS Cognito                Rails API
 |                         |                      |                         |
 |--- Truy cập App ------->|                      |                         |
 |                         |                      |                         |
 |--- Nhấn "Login" ------->|                      |                         |
 |                         |--- Redirect -------->|                         |
 |                         |   đến Hosted UI      |                         |
 |                         |                      |                         |
 |<------ Hiện UI Login (Username/Password) -----|                         |
 |                         |                      |                         |
 |--- Nhập thông tin ------>                      |                         |
 |                         |--- Authenticate ---->|                         |
 |                         |                      |--- Xác thực ---------->|
 |                         |                      |                         |
 |                         |<---- Redirect callback + Code -----------------|
 |                         |                      |                         |
 |--- Frontend gửi Code -> |                      |                         |
 |                         |--- POST /oauth2/token ------------------------>|
 |                         |                      |--- Verify Client ------|
 |                         |                      |--- Trả về JWT Token --->|
 |                         |<-- access + id token + refresh token ----------|
 |                         |                      |                         |
 |--- Gửi access token -->|----------------------------------------------->|
 |                         |                      |--- Verify JWT -------->|
 |                         |                      |--- Tìm/ tạo user ------|
 |                         |                      |--- Trả API response --->|
 |<------------------------|                                                     |
 |                         |                                                     |

```

