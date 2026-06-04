### 1. Cơ chế chứng thực của JWT khi có request
- Khi client gửi request kèm **JWT (JSON Web Token)** trong header (thường là `Authorization: Bearer <token>`), server sẽ:
  - **Giải mã token** bằng secret key hoặc public key (nếu dùng RSA/ECDSA).
  - **Xác thực chữ ký** để đảm bảo token chưa bị giả mạo.
  - **Kiểm tra claims** (ví dụ: `exp`, `iss`, `sub`, `aud`) để xác định token còn hạn, đúng nguồn phát hành, và đúng đối tượng.
  - Nếu hợp lệ, server cho phép truy cập tài nguyên; nếu không, trả về lỗi (thường là `401 Unauthorized` hoặc `403 Forbidden`).

---

### 2. Cơ chế chứng thực của CORS khi gọi API
- **CORS (Cross-Origin Resource Sharing)** là cơ chế bảo mật của trình duyệt khi gọi API từ domain khác.
- Khi client gọi API từ một origin khác:
  - Trình duyệt gửi **preflight request** (HTTP `OPTIONS`) để hỏi server có cho phép origin đó không.
  - Server phản hồi với các header như:
    - `Access-Control-Allow-Origin`: chỉ định origin được phép.
    - `Access-Control-Allow-Methods`: liệt kê các phương thức được phép (`GET`, `POST`, …).
    - `Access-Control-Allow-Headers`: cho phép các header tùy chỉnh.
  - Nếu hợp lệ, trình duyệt mới gửi request chính thức. Nếu không, request bị chặn.

---

### 3. Status HTTP API trả về và cách thống nhất trong hệ thống nhiều third-party/microservices
- **Status HTTP** được trả về trong **response header** (ví dụ: `HTTP/1.1 200 OK`).
- Để thống nhất status trong hệ thống phức tạp:
  - **Chuẩn hóa status code** theo chuẩn HTTP (200, 400, 401, 403, 404, 500…).
  - **Mapping nội bộ**: mỗi microservice có thể có lỗi riêng, nhưng cần map về một bộ status chung trước khi trả về cho client.
  - **API Gateway** hoặc **Service Mesh** thường đảm nhận việc này, giúp:
    - Gom lỗi từ nhiều service.
    - Trả về format thống nhất (ví dụ: `{code: 404, message: "Not Found"}`).
  - Có thể định nghĩa **error contract** chung trong toàn hệ thống để tránh sự không đồng bộ.

---

### 4. Cơ chế đánh index và cách vận hành bên trong một index
- **Đánh index** là quá trình tạo cấu trúc dữ liệu (thường là B-Tree, Hash, hoặc Inverted Index) để tăng tốc độ truy vấn.
- **Cách vận hành bên trong index**:
  - Với **B-Tree Index**: dữ liệu được lưu theo dạng cây cân bằng, giúp tìm kiếm theo khóa nhanh (O(log n)).
  - Với **Hash Index**: dùng hàm băm để ánh xạ giá trị → vị trí, truy vấn chính xác rất nhanh nhưng không hỗ trợ range query.
  - Với **Inverted Index** (thường dùng trong search engine): ánh xạ từ từ khóa → danh sách document chứa từ đó.
  - Khi có query:
    - Hệ thống tra cứu index trước để tìm vị trí dữ liệu.
    - Sau đó truy xuất trực tiếp dữ liệu thay vì quét toàn bộ bảng.
  - Index cần được **cập nhật đồng bộ** khi có insert/update/delete để đảm bảo tính chính xác.

---


### 🧭 Flow Debug Microservices

1. **Phát hiện lỗi (Detection)**
   - Sử dụng **metrics & alerting** (Prometheus, Grafana, Datadog).
   - Thiết lập ngưỡng cảnh báo: error rate > 5%, latency > 2s, CPU > 80%.
   - Alert gửi về Slack/Email để đội ngũ biết ngay.

2. **Khoanh vùng lỗi (Isolation)**
   - Dùng **API Gateway logs** để xác định service nào trả về lỗi nhiều nhất.
   - Gắn **Trace ID** vào mỗi request → theo dõi hành trình qua các service.
   - Nếu nhiều service liên quan, dùng **distributed tracing (Jaeger/Zipkin)** để trực quan hóa.

3. **Phân tích chi tiết (Analysis)**
   - Truy vấn log tập trung (ELK, Loki) theo Trace ID.
   - Xem payload request gây lỗi → có phải dữ liệu đầu vào sai?
   - Kiểm tra **status code mapping**: có service nào trả về code không chuẩn (ví dụ 200 nhưng body báo lỗi)?

4. **Tái hiện lỗi (Reproduction)**
   - Lấy payload từ log → replay trong môi trường staging/test.
   - Nếu lỗi chỉ xảy ra khi tải cao → dùng tool load test (JMeter, k6) để giả lập.

5. **Xác định nguyên nhân gốc (Root Cause)**
   - Kiểm tra code logic, DB query, hoặc dependency bên ngoài.
   - Dùng **profiling/APM (New Relic, Datadog APM)** để tìm bottleneck.
   - Nếu liên quan đến network → kiểm tra service mesh (Istio/Linkerd).

6. **Khắc phục (Fix)**
   - Sửa code hoặc cấu hình.
   - Thêm **circuit breaker / retry policy** để giảm tác động khi service con lỗi.
   - Triển khai fix qua **canary release** để kiểm chứng trước khi rollout toàn hệ thống.

7. **Xác nhận & giám sát (Validation)**
   - Theo dõi metrics sau khi fix → error rate giảm chưa?
   - Dùng dashboard để so sánh trước và sau fix.
   - Nếu ổn định → đóng incident.

8. **Học hỏi & phòng ngừa (Postmortem)**
   - Viết báo cáo nguyên nhân gốc (root cause analysis).
   - Cập nhật **error contract** hoặc quy chuẩn logging nếu cần.
   - Thêm test case hoặc monitoring mới để ngăn lỗi tái diễn.


============================================================
<img width="941" height="602" alt="image" src="https://github.com/user-attachments/assets/b40aa9ce-01e0-4651-a7ae-64d70f03c9dd" />
<img width="722" height="742" alt="image" src="https://github.com/user-attachments/assets/e080f24d-7998-47a7-ac2c-a06054ee0d3b" />


