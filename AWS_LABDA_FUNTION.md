# Tài liệu tổng hợp về AWS Lambda và kiến trúc serverless

## 1. AWS Lambda là gì?

AWS Lambda là dịch vụ serverless computing của Amazon Web Services cho phép chạy code mà không cần quản lý server. Người dùng chỉ cần viết hàm, upload lên Lambda, và dịch vụ sẽ tự động scale theo nhu cầu.

## 2. Các trường hợp sử dụng phổ biến

- Xử lý dữ liệu real-time: phân tích log, clickstream, dữ liệu IoT.

- Xây dựng API serverless: kết hợp với Amazon API Gateway để tạo RESTful hoặc GraphQL API.

- Cron jobs và automation: chạy các tác vụ định kỳ như backup, cleanup.

- Xử lý file/media: tự động xử lý file khi upload lên S3 (ví dụ: tạo thumbnail).

- IoT backend: xử lý dữ liệu từ thiết bị thông minh.

- Machine Learning inference: triển khai mô hình ML để dự đoán hoặc phân loại.

- Bảo mật và quản lý hệ thống: xác thực người dùng, dọn dẹp tài nguyên.

## 3. Hạn chế của AWS Lambda

- Thời gian chạy tối đa: 15 phút cho mỗi function.

- Không phù hợp cho tác vụ dài hoặc cần nhiều CPU liên tục (ví dụ: training ML).

- Chi phí có thể tăng nhanh nếu số lượng request lớn.

## 4. Kiến trúc serverless điển hình

- CodeCommit: là một dịch vụ version control dựa trên Git, được AWS quản lý hoàn toàn.(Chứa source code hoặc có thể dùng GitHub..)

 + Để tiết kei65m chi phí có thể dùng S3 để chứa source code(S3 cũng quản lý được version như bên dưới)
   
    ```
    1. Enable Versioning: Go to your S3 bucket in the AWS Management Console, and enable versioning. Once it’s enabled, every upload will create a new version of that object.

    2.Upload Your Code: Each time you push a new version of your code, simply upload it to the same S3 bucket. S3 will keep track of each version.

    3. Accessing Previous Versions: If you need to revert to an older version, you can simply choose the previous version from the bucket and download it, or even restore it as the current version.

    3. Automate with Scripts: You can use the AWS CLI or SDKs to automate the process, making it easier to manage versions programmatically.
 ```

- API Gateway: nhận request từ client.

- AWS Lambda: xử lý logic nghiệp vụ.

- Amazon RDS/DynamoDB: lưu trữ dữ liệu.

- Amazon S3: lưu trữ file tĩnh.

- Luồng hoạt động ví dụ

- Client gửi request đến API Gateway.

- API Gateway chuyển request vào Lambda.

- Lambda xử lý logic, kết nối RDS/DynamoDB.

- Trả kết quả về cho client.

## 5. Ngôn ngữ lập trình hỗ trợ

- AWS Lambda hỗ trợ nhiều ngôn ngữ:

- Node.js (JavaScript/TypeScript) → phổ biến nhất cho API serverless.

- Python → mạnh cho xử lý dữ liệu, AI/ML.

- Java → phù hợp với hệ thống enterprise.

- C# (.NET Core) → cho team Microsoft stack.

- o → hiệu năng cao.

- Ruby → ít phổ biến nhưng vẫn được hỗ trợ.

### 👉 Node.js và Python là hai lựa chọn phổ biến nhất vì cold start nhanh và nhiều thư viện hỗ trợ.

## 6. API Gateway: dịch vụ AWS hay tự viết?

-  AWS API Gateway: dịch vụ managed, tích hợp sẵn với Lambda, hỗ trợ REST/WebSocket, có sẵn bảo mật và scaling.

-  Tự viết API Gateway: dùng framework (Express, Spring Boot, FastAPI) chạy trên EC2/ECS/Kubernetes.

## So sánh nhanh
### So sánh AWS API Gateway và Tự viết API

| Tiêu chí     | AWS API Gateway              | Tự viết |
|--------------|-----------------------------|---------|
| Triển khai   | Nhanh, tích hợp sẵn          | Phải tự code và deploy |
| Scaling      | Tự động                     | Phải tự quản lý |
| Bảo mật      | Có sẵn IAM, Cognito          | Phải tự tích hợp |
| Chi phí      | Trả theo request            | Tốn chi phí server + vận hành |
| Tùy biến     | Hạn chế                     | Toàn quyền |

## 7. Lambda kết nối với ứng dụng Ruby on Rails trên EC2

- Có thể kết nối theo nhiều cách:

- HTTP/HTTPS API: Lambda gọi API của Rails app.

- Qua VPC nội bộ: đặt Lambda cùng VPC với EC2 để gọi qua private IP hoặc load balancer.

- Qua hàng đợi/pub-sub: Rails app publish message vào SQS/SNS/Kafka, Lambda consume.

- Qua database chung: Lambda và Rails cùng dùng RDS.

So sánh nhanh

## Các cách kết nối giữa hệ thống (Rails – Lambda – Service khác)

| Cách kết nối        | Ưu điểm            | Nhược điểm                 | Khi nên dùng |
|---------------------|--------------------|----------------------------|--------------|
| HTTP/HTTPS API      | Dễ triển khai      | Phải expose ra internet    | Rails đã có API |
| VPC nội bộ          | Bảo mật            | Cấu hình phức tạp          | Muốn giữ nội bộ |
| SQS / SNS / Kafka   | Decoupling         | Tốn thêm tầng messaging    | Hệ thống event-driven |
| Database chung      | Nhanh              | Rủi ro transaction         | Lambda xử lý dữ liệu |


## 8. Kết luận

AWS Lambda là công cụ mạnh mẽ để xây dựng hệ thống serverless, sự kiện-driven, tiết kiệm chi phí. Kết hợp với API Gateway, RDS/DynamoDB, và S3, bạn có thể tạo kiến trúc backend hoàn chỉnh mà không cần quản lý server. Lambda cũng có thể tích hợp với ứng dụng Rails trên EC2 qua API, VPC, messaging hoặc database chung tùy nhu cầu.
