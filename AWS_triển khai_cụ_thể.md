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
  
**3. Tạo CloudFront Distribution:Origin: S3 bucket.**
- Bật HTTPS.
- Cấu hình cache policy.
- Trỏ domain về CloudFront bằng Route 53.

### 3.2 Backend (Laravel)
Mục tiêu: Chạy Laravel trên 3 EC2 instance, cân bằng tải bằng ALB.
Các bước chi tiết:

**1. Tạo VPC với subnet public và private.**

**2. Tạo Security Group:**
 - ALB: mở port 80/443.**
 - EC2: chỉ cho phép traffic từ ALB.
   
**3. Tạo Auto Scaling Group:**
- Launch Template: EC2 với script cài PHP, Nginx, Laravel.
- Cài đặt Laravel:Clone code.
- Cấu hình .env trỏ về RDS và RabbitMQ.
  
**4. Tạo ALB:**
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
=============================================================================
File terraForm cho toàn bộ cấu hình AWS trên

```

provider "aws" {
  region = var.aws_region
}

variable "aws_region" {
  default = "us-east-1"
}

variable "project_name" {
  default = "laravel-vue-app"
}

# VPC
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "${var.project_name}-vpc"
  }
}

# Subnets
resource "aws_subnet" "public" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  map_public_ip_on_launch = true
  availability_zone = "${var.aws_region}a"
  tags = {
    Name = "${var.project_name}-public-subnet"
  }
}

resource "aws_subnet" "private" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "${var.aws_region}b"
  tags = {
    Name = "${var.project_name}-private-subnet"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.main.id
  tags = {
    Name = "${var.project_name}-igw"
  }
}

# Security Groups
resource "aws_security_group" "alb_sg" {
  name        = "${var.project_name}-alb-sg"
  description = "Allow HTTP and HTTPS"
  vpc_id      = aws_vpc.main.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_security_group" "ec2_sg" {
  name        = "${var.project_name}-ec2-sg"
  description = "Allow traffic from ALB"
  vpc_id      = aws_vpc.main.id

  ingress {
    from_port       = 80
    to_port         = 80
    protocol        = "tcp"
    security_groups = [aws_security_group.alb_sg.id]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# Application Load Balancer
resource "aws_lb" "app_alb" {
  name               = "${var.project_name}-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb_sg.id]
  subnets            = [aws_subnet.public.id]
}

resource "aws_lb_target_group" "app_tg" {
  name     = "${var.project_name}-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id
}

resource "aws_lb_listener" "http_listener" {
  load_balancer_arn = aws_lb.app_alb.arn
  port              = 80
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.app_tg.arn
  }
}

# Launch Template cho EC2
resource "aws_launch_template" "app_lt" {
  name_prefix   = "${var.project_name}-lt"
  image_id      = "ami-12345678" # Thay bằng AMI thực tế
  instance_type = "t3.medium"

  network_interfaces {
    security_groups = [aws_security_group.ec2_sg.id]
  }

  tag_specifications {
    resource_type = "instance"
    tags = {
      Name = "${var.project_name}-instance"
    }
  }
}

# Auto Scaling Group
resource "aws_autoscaling_group" "app_asg" {
  name                      = "${var.project_name}-asg"
  desired_capacity          = 3
  max_size                  = 3
  min_size                  = 3
  vpc_zone_identifier       = [aws_subnet.private.id]
  launch_template {
    id      = aws_launch_template.app_lt.id
    version = "$Latest"
  }
  target_group_arns = [aws_lb_target_group.app_tg.arn]
}

# RDS PostgreSQL
resource "aws_db_instance" "app_db" {
  identifier         = "${var.project_name}-db"
  engine             = "postgres"
  instance_class     = "db.t3.medium"
  allocated_storage  = 20
  username           = "admin"
  password           = "password123"
  skip_final_snapshot = true
  multi_az           = true
  vpc_security_group_ids = [aws_security_group.ec2_sg.id]
  db_subnet_group_name   = aws_db_subnet_group.db_subnet.name
}

resource "aws_db_subnet_group" "db_subnet" {
  name       = "${var.project_name}-db-subnet"
  subnet_ids = [aws_subnet.private.id]
}

# S3 Bucket cho frontend
resource "aws_s3_bucket" "frontend_bucket" {
  bucket = "${var.project_name}-frontend"
  acl    = "public-read"
}

# CloudFront Distribution
resource "aws_cloudfront_distribution" "frontend_cf" {
  origin {
    domain_name = aws_s3_bucket.frontend_bucket.bucket_regional_domain_name
    origin_id   = "S3-${aws_s3_bucket.frontend_bucket.id}"
  }

  enabled             = true
  default_root_object = "index.html"

  default_cache_behavior {
    allowed_methods  = ["GET", "HEAD"]
    cached_methods   = ["GET", "HEAD"]
    target_origin_id = "S3-${aws_s3_bucket.frontend_bucket.id}"
    viewer_protocol_policy = "redirect-to-https"
  }

  viewer_certificate {
    cloudfront_default_certificate = true
  }
}

# Amazon MQ (RabbitMQ)
resource "aws_mq_broker" "rabbitmq" {
  broker_name        = "${var.project_name}-mq"
  engine_type        = "RabbitMQ"
  engine_version     = "3.8.22"
  host_instance_type = "mq.t3.micro"
  deployment_mode    = "SINGLE_INSTANCE"
  publicly_accessible = false
  user {
    username = "admin"
    password = "password123"
  }
}

# Route 53 Hosted Zone (giả sử domain đã có)
resource "aws_route53_zone" "main_zone" {
  name = "example.com"
}

# CloudWatch Alarm ví dụ
resource "aws_cloudwatch_metric_alarm" "cpu_alarm" {
  alarm_name          = "${var.project_name}-HighCPU"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = 2
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300
  statistic           = "Average"
  threshold           = 80
  alarm_description   = "This metric monitors EC2 CPU utilization"
  dimensions = {
    AutoScalingGroupName = aws_autoscaling_group.app_asg.name
  }
}

```
