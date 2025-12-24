# Hướng dẫn cấu hình Datadog để giám sát AWS Logs

## 1. Tạo IAM Role trong AWS

- Truy cập **AWS Management Console**
- Vào dịch vụ **IAM**
- Tạo **New Role**
- Chọn **Trusted entity** là AWS service (thường là EC2 hoặc Lambda, tùy mục đích)
- Gán các quyền cần thiết:
  - Có thể dùng policy có sẵn cho Datadog
  - Hoặc tạo **custom policy** cho phép:
    - CloudWatch Metrics
    - CloudWatch Logs
    - EC2, RDS, Lambda (nếu cần)
- Hoàn tất tạo role
- Lưu lại **Role ARN** (sẽ dùng ở bước Datadog)

---

## 2. Cấu hình AWS Integration trong Datadog

- Đăng nhập vào **Datadog Dashboard**
- Vào **Integrations → AWS**
- Chọn **Configure**
- Nhập **Role ARN** đã tạo ở bước 1
- Chọn:
  - AWS Regions cần theo dõi
  - Các dịch vụ AWS cần monitor (EC2, RDS, Lambda, S3, …)

---

## 3. Bật Metrics và Logs

### 3.1 Metrics
- Trong phần cấu hình integration:
  - Chọn các dịch vụ AWS cần thu thập metrics
  - Bật **Detailed Monitoring** nếu cần (ví dụ EC2)

### 3.2 Logs (CloudWatch Logs)
- Bật **Log Collection**
- Cấu hình:
  - CloudWatch Log Groups
  - Log Streams cần forward sang Datadog
- Datadog sẽ pull logs trực tiếp từ CloudWatch

---

## 4. Thiết lập Dashboard và Monitor

- Vào **Dashboards**
  - Tạo dashboard mới cho AWS
  - Thêm biểu đồ CPU, Memory, Network, Error rate…
- Vào **Monitors**
  - Tạo alert cho:
    - CPU cao
    - Memory gần full
    - Error log xuất hiện
    - Lambda timeout / error

---

## 5. Kiểm tra Integration

- Kiểm tra trong Datadog:
  - Metrics có xuất hiện hay chưa
  - Logs có được ingest hay không
- Đối chiếu với CloudWatch để đảm bảo dữ liệu chính xác

---

## 6. Bảo trì & Tối ưu

- Định kỳ rà soát:
  - IAM permissions
  - AWS services đang monitor
- Tối ưu:
  - Dashboard để dễ quan sát
  - Monitor để giảm alert noise
- Cập nhật cấu hình khi hạ tầng AWS thay đổi

---

## Kết luận

Datadog + AWS giúp bạn:
- Theo dõi **metrics + logs + alert** tập trung
- Giám sát hạ tầng cloud theo thời gian thực
- Phát hiện và xử lý sự cố nhanh hơn

---
