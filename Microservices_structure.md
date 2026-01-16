# 📘 Tài liệu tổng hợp: API Gateway và Service Registry trong hệ thống Microservices

## 1. Giới thiệu

Trong kiến trúc **Microservices**, việc quản lý giao tiếp giữa các service là yếu tố then chốt.
Hai thành phần thường được sử dụng để đảm bảo hệ thống hoạt động trơn tru là:

- **API Gateway**
- **Service Registry / Service Discovery**

👉 Hai thành phần này **không thay thế nhau** mà **bổ sung cho nhau**.

---

## 2. API Gateway

### Chức năng chính

- **Single Entry Point**  
  Client chỉ giao tiếp với API Gateway, không gọi trực tiếp microservice.

- **Routing thông minh**  
  Định tuyến request đến đúng microservice dựa trên URL, header hoặc payload.

- **Cross-cutting concerns**
  - Xác thực & phân quyền (SSO, OAuth2, JWT)
  - Rate limiting
  - Caching
  - Logging & Monitoring

- **Protocol Translation**
  - HTTP ↔ gRPC
  - HTTP ↔ WebSocket

- **Load Balancing**
  - Phân phối request đến nhiều instance của service

### Ưu điểm

- Đơn giản hóa giao tiếp với client
- Tập trung bảo mật và giám sát
- Dễ mở rộng khi thêm service mới

### Nhược điểm

- Tăng độ trễ do thêm một lớp trung gian
- Có thể trở thành **Single Point of Failure** nếu không triển khai HA

---

## 3. Service Registry & Service Discovery

### Chức năng chính

- **Service Registry**
  - Lưu trữ thông tin endpoint của các microservices

- **Service Discovery**
  - Cho phép các service tự động tìm thấy nhau

### Mô hình phổ biến

#### Client-side Discovery
- Service tự hỏi registry để lấy địa chỉ service khác

#### Server-side Discovery
- Gateway hoặc Load Balancer hỏi registry và định tuyến request

### Công cụ thường dùng

- Consul
- Eureka (Netflix)
- Zookeeper
- Kubernetes Service / DNS

### Ưu điểm

- Tự động cập nhật khi service thêm hoặc bớt
- Hỗ trợ scale động
- Tránh hard-code địa chỉ service

### Nhược điểm

- Cần thêm hạ tầng registry
- Tăng độ phức tạp hệ thống

---

## 4. So sánh API Gateway và Service Registry

| Tiêu chí | API Gateway | Service Registry |
|--------|------------|------------------|
| Vai trò | Entry point cho client | Danh bạ nội bộ cho service |
| Định tuyến | URL, header, payload | Thông tin đăng ký service |
| Bảo mật | Auth, rate limit, logging | Không quản lý user security |
| Khả năng mở rộng | Dễ thêm service mới | Dễ thêm/bớt instance |
| Ai sử dụng | Client | Gateway hoặc microservices |

---

## 5. Phối hợp API Gateway và Service Registry

1. Client gửi request đến **API Gateway**
2. Gateway xử lý:
   - Authentication / Authorization
   - Logging
   - Rate limiting
3. Gateway hỏi **Service Registry** để tìm endpoint phù hợp
4. Gateway forward request đến microservice
5. Microservice đăng ký/huỷ đăng ký với registry khi start/shutdown

---

## 6. Kết luận

- **API Gateway**
  - Quản lý giao tiếp từ client vào hệ thống
  - Xử lý bảo mật và các tiện ích chung

- **Service Registry / Discovery**
  - Quản lý danh bạ nội bộ
  - Giúp service tìm nhau tự động

👉 Kết hợp cả hai giúp hệ thống:
- Dễ mở rộng
- Bảo mật
- Đáng tin cậy

---

## 7. Minh họa kiến trúc (mô tả)

