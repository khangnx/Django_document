# ⭐ Các dịch vụ AWS thường dùng (Tổng hợp & Dễ hiểu)

## 1. Compute (Máy chủ, chạy ứng dụng)
| Dịch vụ | Mô tả |
|--------|-------|
| EC2 | Máy chủ ảo, toàn quyền cài đặt. |
| Lambda | Serverless, chạy code không cần quản lý server. |
| ECS | Chạy Docker container (orchestrator của AWS). |
| EKS | Kubernetes được AWS quản lý. |
| Lightsail | Hosting đơn giản, rẻ, kiểu VPS. |

## 2. Storage (Lưu trữ)
| Dịch vụ | Mô tả |
|--------|-------|
| S3 | Lưu file, image, video (object storage). |
| EBS | Ổ đĩa gắn vào EC2 (SSD/HDD). |
| EFS | File system chia sẻ giữa nhiều EC2. |
| Glacier | Lưu trữ giá rẻ, backup, archive. |

## 3. Database
| Dịch vụ | Mô tả |
|--------|-------|
| RDS | Database SQL: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server. |
| Aurora | Database SQL hiệu năng cao của AWS. |
| DynamoDB | NoSQL key–value, scale lớn. |
| ElastiCache | Redis/Memcached để cache tốc độ cao. |
| Redshift | Data Warehouse để phân tích dữ liệu lớn. |

## 4. Networking
| Dịch vụ | Mô tả |
|--------|-------|
| VPC | Mạng riêng, subnet, routing. |
| Route 53 | DNS, domain, health check. |
| API Gateway | Làm API, throttling, auth, kết hợp Lambda. |
| CloudFront | CDN phân phối nội dung toàn cầu. |
| Load Balancer (ALB/ELB/NLB) | Cân bằng tải. |

## 5. Security / IAM
| Dịch vụ | Mô tả |
|--------|-------|
| IAM | Tài khoản, quyền, roles, policies. |
| Cognito | User login/signup, OAuth2, JWT. |
| KMS | Quản lý khóa mã hóa. |
| WAF | Web Application Firewall. |
| Shield | Chống DDoS. |
| Secrets Manager | Lưu secrets, password, API key. |

## 6. Developer Tools (CI/CD)
| Dịch vụ | Mô tả |
|--------|-------|
| CodeBuild | Build application (compile, test). |
| CodePipeline | Pipeline CI/CD tự động. |
| CodeDeploy | Deployment cho EC2 / ECS. |
| Cloud9 | Web IDE. |


# So sánh AWS CodeBuild và AWS CodePipeline

## 🎯 Tóm tắt

-   **CodeBuild = Công cụ build code (CI).**\
-   **CodePipeline = Công cụ tự động hóa toàn bộ CI/CD.**

------------------------------------------------------------------------

## 1. CodeBuild là gì?

**CodeBuild** là dịch vụ chuyên dùng để:

-   Build code\
-   Chạy unit test\
-   Tạo artifact (zip, jar, docker image...)\
-   Push image lên ECR\
-   Không cần server, auto scale

File cấu hình chính: `buildspec.yml`.

------------------------------------------------------------------------

## 2. CodePipeline là gì?

**CodePipeline** là dịch vụ điều phối (orchestrator) giúp tự động hóa:

-   Lấy source\
-   Build (gọi CodeBuild)\
-   Test\
-   Deploy (Lambda, EC2, ECS...)\
-   Manual approval

**CodePipeline không build**, nó chỉ sắp xếp các bước.

------------------------------------------------------------------------

## 3. Bảng so sánh CodeBuild vs CodePipeline

  -----------------------------------------------------------------------
  Tiêu chí       AWS CodeBuild             AWS CodePipeline
  -------------- ------------------------- ------------------------------
| Tiêu chí | AWS CodeBuild | AWS CodePipeline |
|---------|----------------|-------------------|
| Vai trò | Build & test code | Tự động hóa CI/CD |
| Làm được gì | Build, test, tạo artifact | Điều phối các bước build, test, deploy |
| Tự chạy được? | ✔️ Có | ❌ Không (cần CodeBuild để build) |
| File config | buildspec.yml | Pipeline JSON/YAML |
| Loại dịch vụ | Compute (build server) | Orchestration (CI/CD workflow) |
| Hỗ trợ Docker | ✔️ Build image | ❌ Không build được |
| Thường dùng cho | Build app, unit test | CI/CD end-to-end |
                                   
------------------------------------------------------------------------

## 4. Khi nào dùng dịch vụ nào?

### ✔️ Dùng CodeBuild khi:

-   Bạn chỉ cần build/test\
-   Dùng GitHub Actions/GitLab CI gọi CodeBuild\
-   Không cần deploy tự động

### ✔️ Dùng CodePipeline khi:

-   Cần CI/CD đầy đủ\
-   Deploy tự động khi có commit\
-   Cần quy trình review, approve

------------------------------------------------------------------------

## 5. Ví dụ flow CI/CD tiêu chuẩn AWS

    Source (GitHub)
          ↓
    Build (CodeBuild)
          ↓
    Deploy (CodeDeploy / ECS / Lambda)
          ↓
    Notify (SNS/Slack)




## 7. Monitoring & Logging
| Dịch vụ | Mô tả |
|--------|-------|
| CloudWatch | Logging, metrics, alert. |
| CloudTrail | Lưu lại lịch sử hoạt động API. |
| X-Ray | Tracing request, debug microservices. |

## 8. Analytics
| Dịch vụ | Mô tả |
|--------|-------|
| Athena | Query S3 bằng SQL. |
| Glue | ETL (extract-transform-load). |
| Kinesis | Streaming data real-time. |
| QuickSight | Dashboard BI. |

## 9. Messaging & Integration
| Dịch vụ | Mô tả |
|--------|-------|
| SQS | Queue phân tán. |
| SNS | Pub/Sub gửi thông báo. |
| EventBridge | Event bus kết nối nhiều service. |

## 10. Machine Learning
| Dịch vụ | Mô tả |
|--------|-------|
| SageMaker | Build, train, deploy ML model. |
| Rekognition | Nhận diện hình ảnh/video. |
| Polly | TTS – Text to Speech. |
| Transcribe | Speech to text. |

## 🔥 Nếu bạn cần theo từng mục đích

### 👉 Deploy web/app
- EC2
- ALB
- ECS / EKS / Lambda
- RDS
- S3
- CloudFront
- Route 53

### 👉 Lưu trữ file
- S3
- CloudFront
- Glacier

### 👉 Microservices
- ECS/EKS
- API Gateway
- SQS/SNS/EventBridge
- CloudWatch + X-Ray

### 👉 Data / BI
- S3
- Athena
- Glue
- Redshift
- QuickSight
