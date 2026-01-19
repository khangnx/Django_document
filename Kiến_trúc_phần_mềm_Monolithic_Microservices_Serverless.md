# Kiến trúc phần mềm: Monolithic, Microservices, Serverless, Event-Driven

## 🧱 Monolithic Architecture
- **Định nghĩa**: Ứng dụng được xây dựng thành một khối duy nhất, deploy cùng nhau.  
- **Ưu điểm**:  
  - Dễ phát triển ban đầu, ít phức tạp.  
  - Deploy nhanh, chỉ cần một package.  
- **Nhược điểm**:  
  - Khó mở rộng khi ứng dụng lớn.  
  - Một lỗi nhỏ có thể ảnh hưởng toàn hệ thống.  
- **Khi dùng**: Ứng dụng nhỏ, MVP, startup giai đoạn đầu.

---

## ⚙️ Microservices Architecture
- **Định nghĩa**: Ứng dụng chia thành nhiều dịch vụ nhỏ, độc lập, giao tiếp qua API.  
- **Ưu điểm**:  
  - Dễ mở rộng từng dịch vụ.  
  - Đội ngũ có thể phát triển song song.  
  - Tăng khả năng chịu lỗi.  
- **Nhược điểm**:  
  - Quản lý phức tạp (network, monitoring, CI/CD).  
  - Tốn chi phí hạ tầng.  
- **Khi dùng**: Hệ thống lớn, cần mở rộng linh hoạt, nhiều team cùng phát triển.

---

## ☁️ Serverless Architecture
- **Định nghĩa**: Chạy ứng dụng trên nền tảng cloud, không cần quản lý server, chỉ viết function.  
- **Ưu điểm**:  
  - Tự động scale theo nhu cầu.  
  - Trả tiền theo mức sử dụng.  
  - Giảm gánh nặng vận hành.  
- **Nhược điểm**:  
  - Phụ thuộc vào nhà cung cấp cloud.  
  - Khó kiểm soát hiệu năng trong workload phức tạp.  
- **Khi dùng**: Ứng dụng có workload không liên tục, event-driven, hoặc startup muốn tiết kiệm chi phí.

---

## 🔔 Event-Driven Architecture
- **Định nghĩa**: Hệ thống hoạt động dựa trên sự kiện, các thành phần giao tiếp qua message/event bus.  
- **Ưu điểm**:  
  - Tăng tính linh hoạt, dễ mở rộng.  
  - Giảm coupling giữa các thành phần.  
  - Phù hợp với hệ thống real-time.  
- **Nhược điểm**:  
  - Debug khó hơn vì luồng xử lý phân tán.  
  - Cần hạ tầng message broker (Kafka, RabbitMQ).  
- **Khi dùng**: Hệ thống xử lý dữ liệu real-time, IoT, thương mại điện tử.

---

## 🎯 Cách trả lời khi phỏng vấn
- **Ngắn gọn, có cấu trúc**:  
  “Monolithic là kiến trúc truyền thống, dễ triển khai nhưng khó mở rộng.  
  Microservices chia nhỏ thành nhiều service, dễ scale nhưng phức tạp quản lý.  
  Serverless giúp giảm chi phí vận hành, phù hợp workload không liên tục.  
  Event-driven thì dùng event để giao tiếp, phù hợp hệ thống real-time.”  

- **Thêm ví dụ thực tế**:  
  “Ví dụ, một ứng dụng thương mại điện tử lớn thường dùng microservices kết hợp event-driven để xử lý đơn hàng và thanh toán.”  

- **Cho thấy khả năng phân tích**:  
  Nhấn mạnh rằng bạn biết chọn mô hình tùy theo bối cảnh, không có mô hình nào “tốt nhất” cho mọi trường hợp.
