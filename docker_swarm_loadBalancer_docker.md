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
