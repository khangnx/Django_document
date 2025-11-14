# Triển khai Ruby on Rails lên AWS với Docker + Docker Swarm (3 Instance)

## 🧩 Sơ đồ kiến trúc

                        ┌───────────────────────────────────┐
                        │         AWS Application LB         │
                        │      (HTTP/HTTPS Load Balancer)    │
                        └───────────────────────────────────┘
                                       │
              ┌────────────────────────┼─────────────────────────┐
              │                        │                         │
              ▼                        ▼                         ▼
    ┌──────────────────┐   ┌──────────────────┐     ┌──────────────────┐
    │ EC2 Instance #1   │   │ EC2 Instance #2  │     │ EC2 Instance #3  │
    │ (Swarm Manager)   │   │ (Swarm Worker)   │     │ (Swarm Worker)   │
    │ Docker Engine     │   │ Docker Engine    │     │ Docker Engine    │
    │ Rails Container   │   │ Rails Container  │     │ Rails Container  │
    │ Nginx Container   │   │ Nginx Container  │     │ Nginx Container  │
    └──────────────────┘   └──────────────────┘     └──────────────────┘
              │                        │                         │
              └────────────────────────┴─────────────────────────┘
                              Docker Swarm Overlay Network
                                       (VXLAN)

## 🏗 Chi tiết thành phần

### 1. Application Load Balancer (ALB)

-   Nhận request HTTP/HTTPS
-   Forward vào các node Swarm
-   Health check

### 2. 3 EC2 Instances

-   1 Manager
-   2 Workers
-   Chạy Docker Engine, Rails, Nginx
-   Giao tiếp qua overlay network

### 3. Containers

-   Nginx reverse proxy
-   Rails (Puma)
-   Optional: Sidekiq

### 4. Database

-   Khuyến nghị dùng Amazon RDS
-   Không chạy DB trong Docker Swarm

## 🚀 Luồng hoạt động

1.  Client → ALB\
2.  ALB → EC2 node\
3.  Nginx → Rails\
4.  Rails → DB\
5.  Trả response

## 📚 Docker Swarm Commands

### Create Swarm

    docker swarm init

### Join Worker

    docker swarm join --token <token> <manager-ip>:2377

### Deploy Stack

    docker stack deploy -c docker-compose.yml rails_app

## docker-compose.yml (sample)

``` yaml
version: '3.8'

services:
  web:
    image: myrailsapp:latest
    ports:
      - "3000:3000"
    deploy:
      replicas: 3
      restart_policy:
        condition: on-failure
    environment:
      DATABASE_URL: ${DATABASE_URL}
    networks:
      - appnet

  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    deploy:
      replicas: 3
    networks:
      - appnet

networks:
  appnet:
    driver: overlay
```
# Docker Swarm đóng vai trò gì?

Docker Swarm **không xử lý request trực tiếp từ client**, mà là
**orchestrator điều phối container**, tương tự Kubernetes.\
Nó đảm bảo toàn bộ hệ thống container hoạt động ổn định, phân phối hợp
lý và tự phục hồi khi có lỗi.

------------------------------------------------------------------------

## 1️⃣ Scheduling (Phân phối container)

-   Khi chạy `docker stack deploy`, Swarm quyết định container nằm ở EC2
    node nào.
-   Đảm bảo đúng số lượng replica.
-   Tự khởi động lại container khi lỗi.
-   Khi một node chết → tự reschedule container sang node còn sống.

------------------------------------------------------------------------

## 2️⃣ Service Discovery

-   Swarm tự tạo DNS nội bộ cho mỗi service.
-   Ứng dụng có thể gọi nhau qua tên "service name", không cần IP.
-   Giúp Rails ↔ Redis ↔ Nginx ↔ Sidekiq giao tiếp dễ dàng.

------------------------------------------------------------------------

## 3️⃣ Internal Load Balancing (Routing Mesh)

-   Khi một request đi vào service `web`, Swarm phân phối request đều
    giữa:
    -   rails-1
    -   rails-2
    -   rails-3
-   Không cần cài thêm load balancer nội bộ.

------------------------------------------------------------------------

## 4️⃣ Overlay Network (VXLAN)

-   Tạo mạng ảo giúp container giữa các EC2 giao tiếp như cùng LAN.
-   Không cần mở từng port thủ công giữa EC2 nodes.
-   Đảm bảo bảo mật và routing nội bộ tự động.

------------------------------------------------------------------------

## 5️⃣ Auto Healing

-   Container chết → Swarm tự restart.
-   Node chết → Swarm tự chạy container ở node khác.
-   Đảm bảo hệ thống luôn hoạt động ổn định.

------------------------------------------------------------------------

## 6️⃣ Rolling Updates & Rollback

-   Khi chạy `docker service update`, Swarm cập nhật từng container theo
    batch.
-   Nếu phiên bản mới lỗi → tự rollback về phiên bản trước.
-   Tránh downtime khi deploy.

------------------------------------------------------------------------

## 🎯 Tóm tắt vai trò của Docker Swarm

  Thành phần              Vai trò
  ----------------------- ------------------------------
  **Scheduler**           Phân phối container vào node
  **Overlay Network**     Kết nối container đa node
  **Routing Mesh**        Load balancing nội bộ
  **Service Discovery**   DNS nội bộ cho services
  **Auto Healing**        Tự phục hồi khi lỗi
  **Rolling Update**      Cập nhật không downtime

Docker Swarm giúp quản lý cluster container đơn giản, gọn nhẹ, phù hợp
với hệ thống có quy mô vừa đến trung bình.
