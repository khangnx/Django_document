# Bài toán thực tế:
```
Tôi có một dự án Laravel và vueJS như sau:
-backend: Laravel
-Frontend: Vuejs
-Database: postgresql
- Có chạy cronjob
-Có 1 RabbitMQ chuyên gữi mail
-Cần load balance 3 instance cùng lúc
Trình bày cách tốt nhất triển khai lên AWS
Nói rõ các dịch vụ cần xài, cách triển khai các dịch vụ đó
Tính chi phí ban đầu cho 1 năm sử dụng
```
# 1. Kiến trúc tổng quan
## Dự án bao gồm:
```
Backend: Laravel
Frontend: VueJS
Database: PostgreSQL
RabbitMQ: Hàng đợi gửi mail
Cronjob
Yêu cầu: Load balancing 3 instance backend
```
## Kiến trúc triển khai trên AWS:
```
EC2: Chạy Laravel backend
ALB (Application Load Balancer): Cân bằng tải cho 3 instance
RDS: PostgreSQL managed service
Amazon MQ (RabbitMQ): Quản lý hàng đợi
S3 + CloudFront: Lưu trữ và phân phối frontend
SES: Gửi email
EventBridge Scheduler: Cronjob
CloudWatch: Giám sát
Route 53: Quản lý DNS
Auto Scaling Group: Đảm bảo HA
```

## 2. Các dịch vụ AWS cần dùng và giải thích
```
**EC2 (Elastic Compute Cloud)**: Chạy các instance ứng dụng Laravel + VueJS, cho phép tùy chỉnh môi trường và cấu hình.
**ALB (Application Load Balancer)**: Phân phối traffic giữa 3 instance backend để đảm bảo cân bằng tải và tính sẵn sàng.
**RDS (Relational Database Service)**: Dịch vụ quản lý cơ sở dữ liệu PostgreSQL, hỗ trợ backup, Multi-AZ và bảo mật.
**Amazon MQ hoặc RabbitMQ trên EC2**: Xử lý hàng đợi gửi mail và các tác vụ bất đồng bộ.
**S3 + CloudFront**: Lưu trữ và phân phối static assets của VueJS, tăng tốc độ tải và giảm tải cho backend.
**Amazon SES**: Gửi email với độ tin cậy cao, thay thế việc tự triển khai SMTP.
**Amazon EFS hoặc S3**: Chia sẻ file giữa các instance nếu ứng dụng cần lưu trữ chung.
**Amazon CloudWatch**: Giám sát hiệu năng, thu thập log và thiết lập cảnh báo; có thể kết hợp EventBridge Scheduler cho cronjob.
**Auto Scaling Group**: Tự động scale số lượng EC2 instance khi tải tăng hoặc giảm.
**VPC + Security Groups**: Thiết lập mạng riêng và bảo mật cho các dịch vụ.
```

## 3. Chi tiết triển khai từng bước (theo cách trả lời chi tiết)
### 3.1 Frontend (VueJS)
Mục tiêu: Build và phân phối VueJS qua CDN.
Các bước chi tiết:
** 1. Build VueJS:**
```
npm install
npm run build
```
→ Thư mục dist sẽ chứa file tĩnh.

**2. Tạo S3 bucket:**
 - Vào S3 → Create bucket.
 - Chọn region.
- Bật hoặc tắt public access tùy theo CloudFront.
- Upload file dist.
** 3. Tạo CloudFront Distribution:Origin: S3 bucket.**
- Bật HTTPS.
- Cấu hình cache policy.
- Trỏ domain về CloudFront bằng Route 53.
### 3.2 Backend (Laravel)
Mục tiêu: Chạy Laravel trên 3 EC2 instance, cân bằng tải bằng ALB.
Các bước chi tiết:

**1. Tạo VPC với subnet public và private.**
**2. Tạo Security Group:
 - ALB: mở port 80/443.**
 - EC2: chỉ cho phép traffic từ ALB.
** 3. Tạo Auto Scaling Group:
- Launch Template: EC2 với script cài PHP, Nginx, Laravel.
- Cài đặt Laravel:Clone code.
- Cấu hình .env trỏ về RDS và RabbitMQ.
**4. Tạo ALB:
- Listener: HTTP/HTTPS.**
- Target Group: EC2 instances.
### 3.3 Database (PostgreSQL)
Mục tiêu: Dùng RDS để quản lý PostgreSQL.
Các bước chi tiết:

 - Tạo Amazon RDS PostgreSQL:
      + Multi-AZ.
      + Instance type db.t3.medium.
      + Thiết lập backup.
      + Security Group: chỉ cho phép từ EC2.
### 3.4 RabbitMQ
Mục tiêu: Xử lý hàng đợi gửi mail.
Các bước chi tiết:

- Dùng Amazon MQ (RabbitMQ):
    - Tạo broker.
    - Cấu hình username/password.
- Cập nhật .env Laravel:
```
  QUEUE_CONNECTION=rabbitmq
  RABBITMQ_HOST=<endpoint>
  RABBITMQ_USER=<user>
  RABBITMQ_PASSWORD=<pass>
```

### 3.5 Cronjob
Mục tiêu: Chạy tác vụ định kỳ.
Các bước chi tiết:

- Dùng EventBridge Scheduler:
    - Tạo rule với cron expression.
- Hoặc cài trực tiếp trên EC2:
```
crontab -e
  * * * * * php /var/www/artisan schedule:run >> /dev/null 2>&1
```

### 3.6 Email (SES)
Mục tiêu: Gửi mail qua SES.
Các bước chi tiết:

- Xác thực domain trong SES.
- Thêm DNS record (DKIM, SPF).
- Cấu hình .env:
```
MAIL_MAILER=ses
  MAIL_HOST=email-smtp.<region>.amazonaws.com
  MAIL_USERNAME=<SMTP user>
  MAIL_PASSWORD=<SMTP password>
  MAIL_PORT=587
```

### 3.7 Giám sát (CloudWatch)
Các bước chi tiết:

- Tạo log group cho EC2.
- Thiết lập alarm cho CPU, RAM.
### 3.8 Route 53
Mục tiêu: Quản lý DNS cho domain.
Các bước chi tiết:

- Tạo Hosted Zone cho domain.
- Tạo bản ghi cho frontend:Alias → CloudFront.
- Tạo bản ghi cho backend:Alias → ALB.
- Thêm bản ghi SES (TXT, CNAME, MX).


## 4. Ước tính chi phí cho 1 năm
Giả sử:

 - 3 EC2 instance loại t3.medium (2 vCPU, 4GB RAM).
 - RDS PostgreSQL db.t3.medium Multi-AZ.
 - Amazon MQ RabbitMQ single-instance.
 - S3 + CloudFront cho frontend.
 - SES gửi khoảng 100k email/tháng.
 - Dung lượng lưu trữ: 100GB cho DB, 50GB cho S3.

**Chi phí ước tính (USD/năm)**

 - EC2: t3.medium ~ $0.0416/giờ → $30/tháng/instance → 3 instance = $90/tháng → $1,080/năm.
 - ALB: ~$18/tháng → $216/năm.
 - RDS PostgreSQL: db.t3.medium Multi-AZ ~ $100/tháng → $1,200/năm.
 - Amazon MQ: ~$50/tháng → $600/năm.
 - S3 + CloudFront: ~$20/tháng → $240/năm.
 - SES: 100k email/tháng ~ $10/tháng → $120/năm.
 - CloudWatch: ~$10/tháng → $120/năm.

==>Tổng cộng ~ $3,576/năm (~85 triệu VND).
