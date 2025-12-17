# Thiết kế Single Sign-On (SSO) trong Ruby on Rails

## 1. Kiến trúc tổng thể
- **Service Provider (SP):** Ứng dụng Ruby on Rails, không lưu mật khẩu người dùng, chỉ nhận thông tin xác thực từ IDP.
- **Identity Provider (IDP):** Hệ thống quản lý danh tính, xác thực người dùng và phát hành assertion/token cho SP.
- **Giao thức phổ biến:**
  - **SAML 2.0:** Dùng XML assertions, phổ biến trong doanh nghiệp.
  - **OAuth2/OpenID Connect:** Dùng JWT tokens, phổ biến trong web/mobile.

---

## 2. Database tại IDP

### Bảng dữ liệu chính
| Thành phần         | Nội dung lưu trữ                                      | Ghi chú |
|--------------------|-------------------------------------------------------|---------|
| **Users**          | username/email, mật khẩu (hash), trạng thái tài khoản | Hash bằng bcrypt/argon2 |
| **Attributes/Claims** | tên, vai trò, nhóm, quyền                          | Gửi sang SP qua assertion/token |
| **Sessions/Tokens** | refresh token, access token, thời gian hết hạn        | Dùng cho OAuth2/OIDC |
| **Certificates/Keys** | public/private key để ký và xác thực                | Đảm bảo tính toàn vẹn |

---

## 3. Các hệ thống có thể làm IDP
- **Doanh nghiệp:**
  - Active Directory Federation Services (ADFS)
  - Azure Active Directory
- **Cloud Providers:**
  - Google Workspace
  - Okta
  - Auth0
  - OneLogin
- **Tự xây dựng:** Rails có thể đóng vai trò IDP với gem `omniauth-saml` hoặc `devise_saml_authenticatable`.

---

## 4. Triển khai trong Ruby on Rails

### Cấu hình gem
- `omniauth-saml` hoặc `ruby-saml` để kết nối với IDP.
- `devise` để quản lý session trong Rails.

### Luồng xác thực
1. Người dùng truy cập ứng dụng → Rails redirect đến IDP.
2. IDP xác thực → gửi SAML assertion hoặc OIDC token về SP.
3. Rails parse token, tạo session cho người dùng.

### Multi-tenant SSO
- Với SaaS, cần hỗ trợ nhiều IDP khác nhau.
- Có thể dùng `omniauth-multi-provider` để cấu hình động.

---

## 5. Rủi ro & Lưu ý
- **Bảo mật:** Luôn kiểm tra chữ ký số từ IDP để tránh giả mạo.
- **Đồng bộ dữ liệu:** SP chỉ lưu thông tin cần thiết (email, role), không lưu mật khẩu.
- **Mapping attributes:** Role từ Okta có thể khác role từ Azure AD → cần cơ chế mapping.
- **Audit:** IDP thường lưu log đăng nhập để phục vụ kiểm tra bảo mật.

---

## 6. Sơ đồ luồng (mô tả)


```
Người dùng → Rails (SP) → Redirect → IDP → Xác thực → Assertion/Token → Rails (SP) → Session
```


# Khi bạn dùng AWS Cognito làm IDP, dữ liệu xác thực đăng nhập không nằm trong database của ứng dụng Rails nữa, mà được quản lý hoàn toàn bởi Cognito. Cụ thể:

## 🗄️ Nơi lưu dữ liệu xác thực trong Cognito
```
### User Pool (chính yếu):
- Đây là nơi Cognito lưu trữ thông tin người dùng.
- Bao gồm: username/email, mật khẩu (đã hash), trạng thái tài khoản, các thuộc tính (attributes) như tên, số điện thoại, role.
- Cognito tự động quản lý việc mã hóa, hash mật khẩu (PBKDF2, bcrypt, hoặc tương đương).
- Bạn có thể thêm custom attributes nếu cần.
### Identity Pool (tùy chọn):
- Dùng để cấp quyền truy cập tạm thời vào các dịch vụ AWS (S3, DynamoDB…).
- Không lưu mật khẩu, chỉ lưu mapping giữa user pool ID và IAM role.
### Tokens (JWT):
- Sau khi đăng nhập, Cognito phát hành ID token, Access token, Refresh token.
- Các token này không lưu trong database của bạn, mà được gửi về client (browser/app).
- Rails chỉ cần xác thực token bằng public key của Cognito (JWKS endpoint).

## 🔑 Vai trò của ứng dụng Rails
- Rails không lưu mật khẩu hay dữ liệu xác thực gốc.
- Rails chỉ lưu thông tin cần thiết để tạo session (ví dụ: user_id, email, role) sau khi xác thực token từ Cognito.
- Nếu cần đồng bộ dữ liệu (ví dụ: profile, quyền), bạn có thể copy attributes từ Cognito về database của Rails, nhưng mật khẩu và xác thực vẫn nằm ở Cognito.

## 📌 Tóm lại
- Dữ liệu đăng nhập (mật khẩu, xác thực) → Lưu trong User Pool của Cognito.
- Token đăng nhập → Cognito phát hành, client giữ, Rails xác thực.
- Ứng dụng Rails → Chỉ lưu session hoặc thông tin phụ trợ, không quản lý mật khẩu.


