# 🔐 Nguyên Tắc Bảo Vệ Trong Lập Trình Web

## 1. Tuân Thủ OWASP Top 10

OWASP Top 10 là danh sách các mối đe dọa bảo mật phổ biến nhất đối với ứng dụng web:

- **Injection**: Tránh SQL injection bằng cách sử dụng truy vấn có tham số (prepared statements).
- **Broken Authentication**: Bảo vệ quy trình đăng nhập, sử dụng xác thực đa yếu tố (MFA).
- **Sensitive Data Exposure**: Mã hóa dữ liệu nhạy cảm, sử dụng HTTPS.
- **Security Misconfiguration**: Cấu hình bảo mật đúng cách, tránh để lộ thông tin hệ thống.
- **Cross-Site Scripting (XSS)**: Lọc và mã hóa đầu vào người dùng.
- **Insecure Deserialization**: Tránh xử lý dữ liệu không tin cậy khi giải mã.
- **Insufficient Logging & Monitoring**: Ghi log đầy đủ và giám sát hoạt động bất thường.

## 2. Xác Thực và Phân Quyền

- Sử dụng các chuẩn xác thực như **OAuth2**, **OpenID Connect**.
- Áp dụng **RBAC (Role-Based Access Control)** để kiểm soát quyền truy cập.
- Không tin tưởng dữ liệu từ client – luôn xác minh ở phía server.

## 3. Bảo Vệ Dữ Liệu

- Mã hóa dữ liệu khi lưu trữ và truyền tải (AES, TLS).
- Không lưu mật khẩu dạng plain text – sử dụng hash (bcrypt, Argon2).
- Giới hạn quyền truy cập vào dữ liệu nhạy cảm.

## 4. Kiểm Tra và Cập Nhật Thường Xuyên

- Thực hiện kiểm thử bảo mật định kỳ:
  - **Static Code Analysis**
  - **Penetration Testing**
- Cập nhật thư viện, framework để vá lỗ hổng bảo mật.
- Sử dụng công cụ như **Snyk**, **SonarQube**, **Burp Suite**.

## 5. Bảo Vệ API

- Áp dụng **rate limiting** để ngăn chặn abuse.
- Sử dụng **token-based authentication** (JWT).
- Kiểm tra và xác thực dữ liệu đầu vào từ API.

## 6. Chính Sách Bảo Mật Nội Dung (CSP)

- Thiết lập **Content Security Policy** để ngăn chặn XSS.
- Hạn chế nguồn tài nguyên được phép tải về từ các domain đáng tin cậy.

## 7. Chống Tấn Công CSRF

- Sử dụng **CSRF token** trong các form.
- Thiết lập **SameSite cookie policy** để ngăn chặn yêu cầu từ domain khác.

## 8. Giám Sát và Phản Ứng Sự Cố

- Thiết lập hệ thống giám sát, cảnh báo khi có hành vi bất thường.
- Có quy trình phản ứng sự cố rõ ràng:
  - Cô lập
  - Phân tích
  - Khắc phục
  - Báo cáo
