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
