# TÍCH HỢP AWS COGNITO VÀO RUBY ON RAILS

## 📌 Mục lục
- [1. Tạo User Pool trong AWS Cognito](#1-tạo-user-pool-trong-aws-cognito)
- [2. Cài đặt gem trong Rails](#2-cài-đặt-gem-trong-rails)
- [3. Cấu hình ENV](#3-cấu-hình-env)
- [4. Tạo Cognito Service](#4-tạo-cognito-service)
- [5. Tạo Auth Controller](#5-tạo-auth-controller)
- [6. Khai báo routes](#6-khai-báo-routes)
- [7. Middleware xác thực JWT Cognito](#7-middleware-xác-thực-jwt-cognito)
- [8. Dùng token Cognito trong Controller](#8-dùng-token-cognito-trong-controller)

---

# 1. Tạo User Pool trong AWS Cognito

1. Truy cập AWS Console → Cognito → **Create User Pool**
2. Chọn email làm username.
3. Tạo **App Client** (Client ID, Client Secret).
4. Ghi lại các giá trị:
   - **User Pool ID**
   - **App Client ID**
   - **App Client Secret** (nếu bật)
   - **Region**

---

# 2. Cài đặt gem trong Rails

Trong file `Gemfile`:

```ruby
gem "aws-sdk-cognitoidentityprovider"
gem "jwt"
```

Cài đặt:

```bash
bundle install
```

---

# 3. Cấu hình ENV

Lưu vào `.env` hoặc Rails Credentials:

```bash
COGNITO_USER_POOL_ID=ap-southeast-1_XXXXXXX
COGNITO_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxx
COGNITO_CLIENT_SECRET=xxxxxxxxxxxxxxxxxxxxxxxx
COGNITO_REGION=ap-southeast-1
```

---

# 4. Tạo Cognito Service

Tạo file: `app/services/cognito_service.rb`

```ruby
require "aws-sdk-cognitoidentityprovider"

class CognitoService
  def initialize
    @client = Aws::CognitoIdentityProvider::Client.new(
      region: ENV["COGNITO_REGION"]
    )
  end

  def sign_up(email, password)
    @client.sign_up(
      client_id: ENV["COGNITO_CLIENT_ID"],
      username: email,
      password: password,
      user_attributes: [
        { name: "email", value: email }
      ]
    )
  end

  def sign_in(email, password)
    @client.initiate_auth(
      client_id: ENV["COGNITO_CLIENT_ID"],
      auth_flow: "USER_PASSWORD_AUTH",
      auth_parameters: {
        "USERNAME" => email,
        "PASSWORD" => password
      }
    )
  end
end
```

---

# 5. Tạo Auth Controller

```ruby
class AuthController < ApplicationController
  def sign_up
    service = CognitoService.new
    result = service.sign_up(params[:email], params[:password])

    render json: { message: "Check email để xác nhận!", cognito: result }
  rescue Aws::CognitoIdentityProvider::Errors::ServiceError => e
    render json: { error: e.message }, status: 400
  end

  def sign_in
    service = CognitoService.new
    result = service.sign_in(params[:email], params[:password])

    tokens = result.authentication_result

    render json: {
      id_token: tokens.id_token,
      access_token: tokens.access_token,
      refresh_token: tokens.refresh_token
    }
  rescue Aws::CognitoIdentityProvider::Errors::ServiceError => e
    render json: { error: e.message }, status: 400
  end
end
```

---

# 6. Khai báo routes

```ruby
post "/sign_up", to: "auth#sign_up"
post "/sign_in", to: "auth#sign_in"
```

---

# 7. Middleware xác thực JWT Cognito

## Lấy JWKS từ Cognito

```
https://cognito-idp.<REGION>.amazonaws.com/<USER_POOL_ID>/.well-known/jwks.json
```

## Tạo middleware

```ruby
require "net/http"
require "openssl"

class CognitoJWT
  def initialize(app)
    @app = app
    jwks_uri = "https://cognito-idp.#{ENV["COGNITO_REGION"]}.amazonaws.com/#{ENV["COGNITO_USER_POOL_ID"]}/.well-known/jwks.json"
    @jwks = JSON.parse(Net::HTTP.get(URI(jwks_uri)))
  end

  def call(env)
    auth = env["HTTP_AUTHORIZATION"]

    if auth && auth.start_with?("Bearer ")
      token = auth.split(" ").last
      payload = decode(token)
      env["cognito.user"] = payload
    end

    @app.call(env)
  rescue JWT::DecodeError => e
    [401, { "Content-Type" => "application/json" }, [{ error: e.message }.to_json]]
  end

  private

  def decode(token)
    header = JWT.decode(token, nil, false).last
    kid = header["kid"]
    key = @jwks["keys"].find { |k| k["kid"] == kid }

    raise "Key not found!" unless key

    public_key = OpenSSL::PKey::RSA.new(
      Base64.decode64(key["n"]),
      Base64.decode64(key["e"])
    )

    JWT.decode(token, public_key, true, { algorithm: "RS256" }).first
  end
end
```

## Thêm middleware vào Rails

```ruby
config.middleware.use CognitoJWT
```

---

# 8. Dùng token Cognito trong Controller

```ruby
class UsersController < ApplicationController
  def profile
    user = request.env["cognito.user"]
    render json: { user: user }
  end
end
```

```ruby
get "/profile", to: "users#profile"
```

---

# 🎉 Hoàn tất!

Bạn đã có hướng dẫn đầy đủ tích hợp AWS Cognito với Ruby on Rails.
