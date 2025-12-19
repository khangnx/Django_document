# 🖥️ Các công cụ Monitoring ngoài Datadog

## 1. Open-source (miễn phí, tự triển khai)
- **Prometheus + Grafana**
  - Chuẩn công nghiệp cho Kubernetes và container.
  - Grafana cung cấp dashboard trực quan, Prometheus mạnh về metrics.
- **Zabbix**
  - Giám sát hạ tầng truyền thống (server, network).
  - Miễn phí, nhưng UI hơi cũ và khó mở rộng cho cloud-native.
- **SigNoz**
  - Open-source thay thế Datadog, hỗ trợ APM, logs, metrics, tracing.
  - Tích hợp OpenTelemetry.
- **Uptrace**
  - Tập trung vào tracing và metrics, tiết kiệm chi phí.
  - Hỗ trợ OpenTelemetry.

## 2. Thương mại (SaaS, cloud-native)
- **New Relic**
  - Mạnh về APM (Application Performance Monitoring).
  - Full-stack observability, tính phí theo dung lượng dữ liệu.
- **Dynatrace**
  - Enterprise scale, nổi bật với AI-driven monitoring.
  - Tự động phát hiện vấn đề, giá cao.
- **Splunk Observability Cloud**
  - Mạnh về phân tích logs và bảo mật.
  - Tích hợp tốt với SIEM, phù hợp cho compliance.
- **Better Stack**
  - Tập trung vào log management và uptime monitoring.
  - Giao diện hiện đại, chi phí thấp hơn Datadog.

---

## 📊 So sánh nhanh

| Công cụ            | Loại hình       | Điểm mạnh chính                  | Hạn chế |
|--------------------|-----------------|----------------------------------|---------|
| Datadog            | SaaS            | Toàn diện, tích hợp đa dạng      | Chi phí cao |
| Prometheus+Grafana | Open-source     | Chuẩn K8s, dashboard đẹp          | Cần tự quản lý |
| Zabbix             | Open-source     | Mạnh về hạ tầng truyền thống      | Khó mở rộng cloud |
| SigNoz             | Open-source     | APM + logs + tracing, miễn phí    | Cộng đồng nhỏ |
| Uptrace            | Open-source     | Tích hợp OpenTelemetry, tiết kiệm | Ít tính năng nâng cao |
| New Relic          | SaaS            | APM mạnh, full-stack observability| Tính phí theo dữ liệu |
| Dynatrace          | SaaS            | AI-driven, enterprise scale       | Giá cao |
| Splunk             | SaaS/Hybrid     | Logs + bảo mật mạnh               | Phức tạp, tốn chi phí |
| Better Stack       | SaaS            | Log management hiện đại           | Ít tính năng APM |

---

## ⚠️ Lưu ý khi chọn công cụ
- **Quy mô hệ thống:** Vài server → Zabbix hoặc Prometheus đủ.  
- **Cloud-native/microservices:** Datadog, New Relic, Dynatrace, hoặc SigNoz phù hợp hơn.  
- **Chi phí:** Open-source tiết kiệm nhưng cần đội ngũ kỹ thuật; SaaS tiện lợi nhưng tốn kém.  
- **Bảo mật & compliance:** Splunk hoặc Dynatrace thường được chọn trong enterprise.




# Công cụ xem CloudWatch trên AWS – Giải thích chi tiết

Tài liệu này mô tả **từng công cụ dùng để xem và khai thác CloudWatch trên AWS**, bao gồm:

* Dùng để làm gì
* Ưu điểm
* Nhược điểm
* Khi nào nên dùng
* Ví dụ thực tế (Rails / AWS)

---

## 1️⃣ AWS Management Console – CloudWatch Console

### Dùng để làm gì?

* Xem **metrics** (CPU, RAM, Disk, ALB, RDS…)
* Xem **logs**
* Tạo **alarm**
* Tạo **dashboard**
* Debug lỗi production

👉 Đây là **giao diện web chính thức của AWS** để làm việc với CloudWatch.

---

### Ưu điểm ✅

* Không cần cài đặt
* Chính xác 100% (native AWS)
* Dễ dùng cho người mới
* Quản lý alarm, log, metric trong một nơi

### Nhược điểm ❌

* UI hơi chậm khi log lớn
* Dashboard đơn giản
* Không tối ưu cho hệ thống rất lớn

### Khi nào nên dùng?

* Debug nhanh
* Setup ban đầu
* Production nhỏ – vừa

### Ví dụ thực tế

* Kiểm tra **Rails server CPU cao**
* Xem `production.log`
* Tạo alarm CPU > 80%

---

## 2️⃣ CloudWatch Metrics

### Dùng để làm gì?

* Theo dõi **sức khỏe hạ tầng**
* Phát hiện bottleneck
* Trigger auto scaling

### Ví dụ metric phổ biến

* EC2: `CPUUtilization`
* ALB: `TargetResponseTime`
* RDS: `DatabaseConnections`

---

### Ưu điểm ✅

* Có sẵn mặc định
* Độ tin cậy cao
* Tích hợp Auto Scaling

### Nhược điểm ❌

* RAM / Disk cần cài agent
* Metric mặc định khá low-level
* Không query phức tạp

### Khi nào nên dùng?

* Monitoring infrastructure
* Alert theo threshold

---

## 3️⃣ CloudWatch Logs

### Dùng để làm gì?

* Lưu trữ log tập trung
* Debug application
* Audit lỗi production

### Ví dụ log

* Rails log
* Nginx / Apache log
* System log

---

### Ưu điểm ✅

* Không cần quản lý server log
* Scale tốt
* Tích hợp chặt với AWS

### Nhược điểm ❌

* Query khó hơn SQL
* Chi phí tăng nếu log nhiều
* UI chưa thân thiện

### Khi nào nên dùng?

* Production log
* Không muốn vận hành ELK

---

## 4️⃣ CloudWatch Logs Insights

### Dùng để làm gì?

* Truy vấn & phân tích log nhanh

```sql
filter @message like /ERROR/
| stats count(*) by bin(5m)
```

---

### Ưu điểm ✅

* Truy vấn rất nhanh
* Không cần index
* Phân tích theo thời gian tốt

### Nhược điểm ❌

* Ngôn ngữ truy vấn riêng
* Không realtime 100%
* Khó lưu query dài hạn

### Khi nào nên dùng?

* Debug production
* Phân tích lỗi theo thời gian

---

## 5️⃣ CloudWatch Dashboards

### Dùng để làm gì?

* Gom nhiều metrics vào một màn hình
* Theo dõi real-time

---

### Ưu điểm ✅

* Native AWS
* Setup nhanh
* Miễn phí cơ bản

### Nhược điểm ❌

* Ít biểu đồ nâng cao
* Không filter động

### Khi nào nên dùng?

* Ops theo dõi hệ thống
* Dashboard nội bộ

---

## 6️⃣ CloudWatch Alarms + SNS

### Dùng để làm gì?

* Gửi cảnh báo khi metric vượt ngưỡng

### Ví dụ

* CPU > 80%
* ALB 5xx > 5

---

### Ưu điểm ✅

* Ổn định
* Gắn Auto Scaling
* Nhiều kênh alert

### Nhược điểm ❌

* Dễ alert fatigue
* Chỉ threshold-based

### Khi nào nên dùng?

* Production
* On-call system

---

## 7️⃣ AWS CLI

### Dùng để làm gì?

* Xem metric bằng command line
* Automation

---

### Ưu điểm ✅

* Phù hợp DevOps
* Script hóa dễ
* CI/CD friendly

### Nhược điểm ❌

* Không trực quan
* Không phù hợp non-tech

### Khi nào nên dùng?

* Automation
* Debug nhanh trên server

---

## 8️⃣ Amazon Managed Grafana

### Dùng để làm gì?

* Dashboard nâng cao
* Gom nhiều nguồn data

---

### Ưu điểm ✅

* UI đẹp
* Query mạnh
* Phù hợp hệ thống lớn

### Nhược điểm ❌

* Có chi phí
* Setup IAM phức tạp

### Khi nào nên dùng?

* Production lớn
* Microservices

---

## 9️⃣ Grafana tự host (EC2 / Docker)

### Dùng để làm gì?

* Thay thế Managed Grafana
* Toàn quyền kiểm soát

---

### Ưu điểm ✅

* Tiết kiệm chi phí
* Custom cao

### Nhược điểm ❌

* Phải tự vận hành
* Tự lo backup & security

### Khi nào nên dùng?

* Có team DevOps
* Muốn kiểm soát hoàn toàn

---

## 🔟 So sánh nhanh

| Tool          | Mục đích     | Khuyến nghị |
| ------------- | ------------ | ----------- |
| AWS Console   | Debug        | ⭐⭐⭐⭐⭐       |
| Logs Insights | Log analysis | ⭐⭐⭐⭐        |
| Dashboards    | Monitoring   | ⭐⭐⭐         |
| Alarms        | Alert        | ⭐⭐⭐⭐⭐       |
| Grafana       | Advanced     | ⭐⭐⭐⭐        |

---

## 🎯 Gợi ý cho Rails + AWS

### Bắt đầu

* CloudWatch Console
* Logs + Logs Insights
* Alarms + SNS

### Mở rộng

* Dashboards
* Grafana

---

📌 Có thể mở rộng tiếp:

* Monitoring Rails / Sidekiq / Redis
* So sánh CloudWatch vs ELK vs Datadog
* Setup Grafana + CloudWatch step-by-step
* Runbook xử lý sự cố production
