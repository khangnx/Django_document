# 📘 Cheatsheet Dịch vụ AWS Thường Dùng

## 🖥️ Compute Services (Dịch vụ tính toán)

| Dịch vụ | Mô tả | Dùng để làm gì | Giá cả |
|--------|-------|----------------|--------|
| **EC2** | Ví dụ: Khởi tạo một máy chủ Ubuntu để chạy website WordPress.<br> Máy chủ ảo có thể tùy chỉnh cấu hình | Chạy ứng dụng, website, backend | Tính theo giờ sử dụng, loại instance |
| **Lambda** | Ví dụ: Tự động resize ảnh khi người dùng upload lên S3.<br> Chạy code không cần quản lý server | Tự động hóa, xử lý sự kiện | Tính theo số lần gọi và thời gian chạy |
| **Elastic Beanstalk** | Ví dụ: Triển khai ứng dụng Flask bằng Git push.<br> Triển khai ứng dụng tự động | Web app, API, backend | Miễn phí dịch vụ, trả tiền cho tài nguyên sử dụng |
| **Lightsail** | Ví dụ: Tạo VPS để chạy blog cá nhân bằng WordPress.<br> VPS đơn giản, dễ dùng | Website nhỏ, blog, app đơn giản | Gói cố định từ $3.5/tháng |

## 📦 Storage Services (Lưu trữ)

| Dịch vụ | Mô tả | Dùng để làm gì | Giá cả |
|--------|-------|----------------|--------|
| **S3** | Ví dụ: Lưu trữ ảnh sản phẩm cho website thương mại điện tử.<br> Lưu trữ đối tượng (object storage) | Lưu file, ảnh, video, backup | Tính theo GB/tháng và số lần truy cập |
| **EBS** | Ví dụ: Gắn ổ đĩa cho EC2 để lưu database MySQL.<br> Lưu trữ dạng block cho EC2 | Lưu dữ liệu hệ điều hành, database | Tính theo GB/tháng |
| **EFS** | Ví dụ: Chia sẻ thư mục giữa nhiều EC2 trong hệ thống xử lý dữ liệu.<br> Lưu trữ dạng file, chia sẻ giữa EC2 | File server, chia sẻ dữ liệu | Tính theo GB/tháng |
| **Glacier** | Ví dụ: Lưu trữ bản backup hàng tháng của hệ thống kế toán.<br> Lưu trữ lâu dài, chi phí thấp | Backup, lưu trữ dữ liệu ít truy cập | Rẻ hơn S3, nhưng truy xuất chậm |

## 🗃️ Database Services (Cơ sở dữ liệu)

| Dịch vụ | Mô tả | Dùng để làm gì | Giá cả |
|--------|-------|----------------|--------|
| **RDS** | Ví dụ: Tạo cơ sở dữ liệu PostgreSQL cho ứng dụng ERP.<br> Cơ sở dữ liệu quan hệ (MySQL, PostgreSQL...) | Web app, hệ thống ERP, CRM | Tính theo loại DB, instance, storage |
| **DynamoDB** | Ví dụ: Lưu dữ liệu người dùng cho ứng dụng chat real-time.<br> NoSQL, hiệu suất cao | Ứng dụng real-time, IoT | Tính theo request và dung lượng |
| **Aurora** | Ví dụ: Chạy hệ thống đặt vé máy bay với hiệu suất cao.<br> DB hiệu năng cao, tương thích MySQL/PostgreSQL | App lớn, cần hiệu suất cao | Cao hơn RDS, nhưng tối ưu hơn |

## 🧪 Machine Learning & AI

| Dịch vụ | Mô tả | Dùng để làm gì | Giá cả |
|--------|-------|----------------|--------|
| **SageMaker** | Ví dụ: Huấn luyện mô hình dự đoán nhu cầu khách hàng.<br> Nền tảng xây dựng, huấn luyện ML model | AI, phân tích dữ liệu, dự đoán | Tính theo tài nguyên sử dụng |
| **Rekognition** | Ví dụ: Phân tích ảnh camera để phát hiện khuôn mặt.<br> Nhận diện hình ảnh, video | Phân tích ảnh, bảo mật, giám sát | Tính theo số ảnh/video xử lý |

## 🔐 Security & Identity

| Dịch vụ | Mô tả | Dùng để làm gì | Giá cả |
|--------|-------|----------------|--------|
| **IAM** | Ví dụ: Tạo user chỉ có quyền đọc dữ liệu từ S3.<br> Quản lý người dùng và quyền truy cập | Bảo mật hệ thống AWS | Miễn phí |
| **Cognito** | Ví dụ: Xác thực người dùng đăng nhập vào ứng dụng mobile.<br> Xác thực người dùng cho ứng dụng | Đăng nhập, quản lý người dùng | Tính theo số người dùng hoạt động |

## 🌐 Networking & CDN

| Dịch vụ | Mô tả | Dùng để làm gì | Giá cả |
|--------|-------|----------------|--------|
| **VPC** | Ví dụ: Tạo mạng riêng cho hệ thống backend và database.<br> Mạng riêng ảo | Tạo môi trường mạng riêng cho EC2 | Miễn phí, trả tiền cho tài nguyên |
| **CloudFront** | Ví dụ: Phân phối ảnh sản phẩm đến người dùng toàn cầu.<br> CDN phân phối nội dung toàn cầu | Tăng tốc website, video | Tính theo GB truyền tải và request |

## 📊 Monitoring & Management

| Dịch vụ | Mô tả | Dùng để làm gì | Giá cả |
|--------|-------|----------------|--------|
| **CloudWatch** | Ví dụ: Theo dõi CPU và RAM của EC2 để cảnh báo khi quá tải.<br> Giám sát tài nguyên AWS | Theo dõi hiệu suất, cảnh báo | Miễn phí cơ bản, trả tiền cho logs |
| **CloudTrail** | Ví dụ: Ghi lại lịch sử truy cập API để phục vụ audit.<br> Ghi lại hoạt động API | Audit, bảo mật, compliance | Miễn phí cơ bản |



# Các cách tính phí trogn AWS
## ✅ 1. Pay-as-you-go (Trả theo mức sử dụng)

Bạn chỉ trả tiền cho tài nguyên đã dùng.
Ví dụ:

EC2: tính theo giờ hoặc giây chạy instance.
S3: tính theo dung lượng lưu trữ (GB/tháng) + số request.
Lambda: tính theo số lần gọi và thời gian thực thi (ms).




## ✅ 2. On-Demand Pricing

Không cam kết dài hạn.
Giá cao hơn nhưng linh hoạt.
Phù hợp cho workload biến động hoặc thử nghiệm.


## ✅ 3. Reserved Instances

Cam kết sử dụng trong 1 hoặc 3 năm.
Giảm giá đến 72% so với On-Demand.
Áp dụng cho EC2, RDS, ElastiCache...


## ✅ 4. Savings Plans

Cam kết chi tiêu tối thiểu (USD/giờ) trong 1 hoặc 3 năm.
Linh hoạt hơn Reserved Instances vì áp dụng cho nhiều dịch vụ (EC2, Fargate, Lambda).


## ✅ 5. Spot Instances

Mua tài nguyên dư thừa với giá rẻ (giảm đến 90%).
Có thể bị thu hồi khi AWS cần tài nguyên.
Phù hợp cho batch job, xử lý không yêu cầu liên tục.


## ✅ 6. Free Tier

Miễn phí giới hạn trong 12 tháng hoặc vĩnh viễn cho một số dịch vụ.
Ví dụ: 750 giờ EC2 t2.micro/tháng, 5GB S3.


## ✅ 7. Data Transfer Pricing

Tính phí dựa trên lượng dữ liệu ra/vào AWS.
Inbound (vào AWS): thường miễn phí.
Outbound (ra Internet): tính phí theo GB.


## ✅ 8. Additional Costs

Storage Class (S3 Standard vs Glacier).
API Request (S3 PUT/GET).
Load Balancer: tính theo giờ + số request.
Managed Services: phí quản lý (ví dụ RDS có phí instance + storage + backup).

## Hình Minh họa

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/1977a08c-60b8-4e9d-95b3-9ae27aaf35b9" />



### Lambda và S3 không tự chứng thực người dùng trực tiếp, mà việc chứng thực thường được thực hiện thông qua AWS Identity and Access Management (IAM) hoặc các dịch vụ trung gian như Amazon Cognito, API Gateway, hoặc ứng dụng backend. Dưới đây là cách phổ biến để chứng thực và phân quyền người dùng khi sử dụng Lambda và S3:

## 1. Chứng thực qua Amazon Cognito (phổ biến cho ứng dụng web/mobile)

Người dùng đăng nhập qua Cognito (hoặc liên kết với Google, Facebook, v.v.).
Cognito cấp JWT token cho người dùng.
Token này được gửi kèm trong request đến API Gateway.
API Gateway xác thực token và chuyển request đến Lambda.
Lambda có thể dùng IAM role để truy cập S3 nếu cần.


Ưu điểm: Tách biệt rõ ràng giữa chứng thực và xử lý logic, dễ mở rộng.


## 2. Chứng thực qua API Gateway + Lambda

API Gateway có thể cấu hình để xác thực bằng:

JWT token (qua Cognito hoặc bên thứ ba).
Lambda Authorizer: một hàm Lambda chuyên kiểm tra token và trả về quyền truy cập.


Sau khi xác thực, request được chuyển đến Lambda chính để xử lý.
Lambda có thể truy cập S3 bằng IAM role gắn với nó.


## 3. Chứng thực trong Lambda (ít phổ biến hơn)

Lambda tự kiểm tra token hoặc thông tin người dùng trong request.
Nếu hợp lệ, Lambda thực hiện hành động (ví dụ: đọc/ghi S3).
Cần đảm bảo IAM role của Lambda có quyền truy cập S3.


Cách này đơn giản nhưng không tối ưu cho bảo mật và mở rộng.


## 4. Phân quyền truy cập S3

S3 không chứng thực người dùng cuối, mà dựa vào IAM role hoặc pre-signed URL:

Lambda có thể tạo pre-signed URL cho phép người dùng tải lên hoặc tải xuống file từ S3 mà không cần quyền IAM.
Hoặc dùng IAM policy để giới hạn quyền truy cập theo user hoặc nhóm.

Tóm tắt luồng phổ biến:
Người dùng → Cognito → API Gateway → Lambda → S3

Hoặc:
Người dùng → App → Lambda → S3 (với pre-signed URL)

