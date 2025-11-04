# Tài liệu: Xây dựng và Tối ưu hóa REST API & gRPC API cho Mobile và Web

---

## 1. REST API là gì?

REST API (Representational State Transfer) là một phong cách kiến trúc cho phép các ứng dụng giao tiếp qua HTTP bằng cách sử dụng các phương thức chuẩn như GET, POST, PUT, DELETE. Dữ liệu thường được trao đổi dưới dạng JSON hoặc XML.

Đặc điểm chính:
- Dựa trên tài nguyên (resource-based).
- Sử dụng HTTP methods chuẩn.
- Stateless (không lưu trạng thái giữa các request).
- Dễ triển khai và phổ biến.

## 2. gRPC API là gì?

gRPC (Google Remote Procedure Call) là một framework RPC hiệu năng cao, sử dụng HTTP/2 và Protocol Buffers để truyền dữ liệu nhị phân. gRPC hỗ trợ streaming hai chiều và đa ngôn ngữ.

Đặc điểm chính:
- Hiệu năng cao nhờ HTTP/2 và Protobuf.
- Hỗ trợ streaming (client, server, bidirectional).
- Tự động sinh code client/server từ file `.proto`.
- Phù hợp cho microservices và hệ thống phân tán.

## 3. Khi nào dùng REST API?
- Ứng dụng web hoặc mobile cần tương thích rộng rãi.
- Giao tiếp với bên thứ ba (public APIs).
- Dữ liệu không yêu cầu tốc độ cực cao.
- Dễ dàng debug qua trình duyệt hoặc Postman.

## 4. Khi nào dùng gRPC API?
- Hệ thống microservices cần hiệu năng cao.
- Truyền dữ liệu lớn hoặc streaming thời gian thực.
- Giao tiếp nội bộ giữa các dịch vụ.
- Yêu cầu giảm độ trễ và tiết kiệm băng thông.

## 5. Ưu và Nhược điểm REST vs gRPC

### REST API
Ưu điểm:
- Phổ biến, dễ triển khai.
- Tương thích tốt với trình duyệt và HTTP tools.
- Dễ debug và test.

Nhược điểm:
- Hiệu năng thấp hơn do dùng JSON và HTTP/1.1.
- Không hỗ trợ streaming native.
- Payload lớn hơn so với Protobuf.

### gRPC API
Ưu điểm:
- Hiệu năng cao, tiết kiệm băng thông.
- Hỗ trợ streaming hai chiều.
- Tự động sinh code đa ngôn ngữ.

Nhược điểm:
- Yêu cầu HTTP/2, khó triển khai trên môi trường cũ.
- Debug phức tạp hơn (dữ liệu nhị phân).
- Ít phổ biến với public APIs.

## 6. Best Practices cho REST API
- Giảm payload size: dùng gzip, chọn trường cần thiết.
- Pagination: cursor-based hoặc offset-based.
- Caching: sử dụng HTTP headers.
- Versioning: `/v1/...` hoặc qua header.
- Bảo mật: HTTPS, OAuth 2.0, JWT.
- Document: OpenAPI/Swagger.

### Ví dụ REST API với Node.js (Express)
```
const express = require('express');
const app = express();
app.use(express.json());

app.get('/api/users', (req, res) => {
  res.json([{ id: 1, name: 'Nguyen Van A' }]);
});

app.post('/api/users', (req, res) => {
  res.status(201).json({ message: 'User created', user: req.body });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```
## 7. Best Practices cho gRPC API
- Định nghĩa rõ ràng trong file `.proto`.
- Sử dụng streaming khi cần.
- Bảo mật: TLS, xác thực.
- Monitoring: logging, metrics.

### Ví dụ file .proto
```
syntax = "proto3";
service UserService {
  rpc GetUser (GetUserRequest) returns (User);
}
message GetUserRequest { int32 id = 1; }
message User { int32 id = 1; string name = 2; }
```

## 8. So sánh REST vs gRPC
|Tiêu chí|REST|gRPC|
|---|---|---|
|Giao thức|HTTP/1.1|HTTP/2|
|Định dạng|JSON|Protobuf|
|Streaming|Không native|Có hỗ trợ|
|Hiệu năng|Trung bình|Cao|
|Tương thích|Rộng rãi|Cần hỗ trợ HTTP/2|


## 6. Xác thực khi dùng REST API và gRPC API
### REST API
```
OAuth 2.0: Chuẩn phổ biến cho xác thực và phân quyền.
JWT (JSON Web Token): Dùng để truyền thông tin xác thực an toàn giữa client và server.
API Key: Đơn giản nhưng kém an toàn hơn.
HTTPS: Bắt buộc để mã hóa dữ liệu truyền tải.
```
### gRPC API
```
TLS (Transport Layer Security): Bảo mật kết nối giữa client và server.
Token-based Authentication: Có thể dùng JWT hoặc OAuth.
Mutual TLS (mTLS): Xác thực hai chiều giữa client và server.
Interceptor: Thêm logic xác thực vào gRPC call
```
## 9. Tài nguyên tham khảo
- https://zuplo.com
- https://grpc.io
- https://swagger.io

