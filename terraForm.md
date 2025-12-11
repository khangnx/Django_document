# 🌍 Kiến thức cơ bản về Terraform

## ✅ Terraform là gì?
Terraform là một công cụ mã nguồn mở do HashiCorp phát triển, dùng để **quản lý hạ tầng dưới dạng mã (Infrastructure as Code - IaC)**. Bạn mô tả hạ tầng bằng các file `.tf` thay vì cấu hình thủ công trên giao diện web của AWS, Azure, GCP...

---

## 🎯 Mục đích chính của Terraform
- Tự động hóa việc triển khai hạ tầng.
- Đảm bảo tính nhất quán giữa các môi trường (Dev, Staging, Prod).
- Quản lý phiên bản và kiểm soát thay đổi.
- Tái sử dụng cấu hình qua modules.
- Quản lý trạng thái hạ tầng (state).

---

## 📦 Cấu trúc dự án Terraform phổ biến
- `main.tf`: Khai báo tài nguyên chính.
- `provider.tf`: Định nghĩa nhà cung cấp (AWS, Azure, GCP...).
- `variables.tf`: Khai báo biến sử dụng trong dự án.
- `outputs.tf`: Xuất ra các giá trị sau khi triển khai.
- `backend.tf`: Cấu hình nơi lưu trữ trạng thái (state).
- `modules/`: Chứa các module tái sử dụng.
- `envs/`: Biến môi trường cho từng môi trường triển khai.
- `tfvars/`: File chứa giá trị biến cho từng môi trường.

---

## 🔁 Quy trình hoạt động của Terraform
1. Viết mã hạ tầng trong các file `.tf`.
2. Chạy `terraform init` để khởi tạo dự án.
3. Chạy `terraform plan` để xem trước thay đổi.
4. Chạy `terraform apply` để triển khai hạ tầng.
5. Terraform lưu trạng thái vào `terraform.tfstate`.

---

## 🧪 Ví dụ thực tế
Bạn muốn triển khai:
- 1 VPC
- 3 EC2 instance
- 1 RDS database
- 1 S3 bucket

→ Chỉ cần viết cấu hình `.tf` và chạy `terraform apply`. Không cần thao tác thủ công trên AWS Console.

---

## ⚖️ So sánh Terraform với các công cụ khác

| Công cụ         | Ngôn ngữ cấu hình | Quản lý trạng thái | Hỗ trợ đa nền tảng | Tự động hóa |
|----------------|-------------------|---------------------|--------------------|-------------|
| Terraform      | HCL               | Có (tfstate)        | Có                 | Có          |
| CloudFormation | JSON/YAML         | Có (stack)          | Chỉ AWS            | Có          |
| Ansible        | YAML              | Không               | Có                 | Có          |

---

## 📚 Tài nguyên học tập
- https://developer.hashicorp.com/terraform
- https://learn.hashicorp.com/terraform
- https://github.com/hashicorp/terraform


# Để chạy Terraform trên máy local và deploy hạ tầng lên AWS, bạn cần thực hiện theo các bước sau:

## 🛠 Chuẩn bị môi trường
- Cài Terraform: tải từ terraform.io và thêm vào PATH.
- Cài AWS CLI: tải từ AWS CLI.
- Cấu hình AWS credentials:
```
aws configure
```

- Nhập AWS Access Key ID, AWS Secret Access Key, region (ví dụ: ap-southeast-1 cho Singapore).

## 📂 Tạo project Terraform

1. Tạo thư mục dự án:
```
mkdir terraform-aws-demo && cd terraform-aws-demo
```

2. Tạo file main.tf với nội dung cơ bản:
```
provider "aws" {
  region = "ap-southeast-1"
}

resource "aws_s3_bucket" "example" {
  bucket = "my-terraform-demo-bucket-12345"
  acl    = "private"
}
```
## 🚀 Quy trình chạy Terraform

1. Khởi tạo Terraform:
``
terraform init
 → tải plugin AWS provider.
```

2. Kiểm tra plan:
```
terraform plan
→ hiển thị những gì sẽ được tạo.
```
3. Deploy lên AWS:
```
terraform apply
→ xác nhận yes để Terraform tạo resource.
```

4. Xóa hạ tầng khi không cần nữa:
```
terraform destroy
```


## ⚡ Lưu ý quan trọng
- Luôn dùng IAM user với quyền hạn tối thiểu thay vì root account.
- Có thể tách file thành variables.tf, outputs.tf để dễ quản lý.
- Dùng Terraform state (terraform.tfstate) để theo dõi hạ tầng đã deploy.
- Nếu làm việc nhóm, nên lưu state trong S3 + DynamoDB để tránh xung đột.


