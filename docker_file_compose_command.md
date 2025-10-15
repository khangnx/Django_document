

## 📦 Docker Compose

### 🔧 Cấu trúc cơ bản của file `docker-compose.yml`

version: '3.8'
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
      - NGINX_PORT=80
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: example

### 🧩 Giải thích các thành phần:

- services: Khai báo các container.
- image: Tên image Docker cần dùng.
- ports: Mở cổng từ host → container (host:container).
- volumes: Gắn thư mục từ host vào container.
- environment: Biến môi trường truyền vào container.

---

## 🛠️ Các lệnh Docker cơ bản

### 📦 Image & Container

docker build -t myimage .           # Build image từ Dockerfile
docker run -d -p 8080:80 myimage    # Chạy container từ image
docker ps                           # Liệt kê container đang chạy
docker ps -a                        # Liệt kê tất cả container
docker stop <container_id>         # Dừng container
docker rm <container_id>           # Xóa container
docker rmi <image_id>              # Xóa image

### 🔍 Kiểm tra & Gỡ lỗi

docker exec -it <container_id> bash   # Truy cập vào container
docker logs <container_id>           # Xem log container

---

## 🧪 Docker Compose Commands

docker-compose up -d       # Khởi động các service ở chế độ nền
docker-compose down        # Dừng và xóa container, network
docker-compose build       # Build lại các image
docker-compose logs        # Xem log các service
docker-compose ps          # Liệt kê các container đang chạy

---

## 📘 Ví dụ nâng cao về `docker-compose.yml`

version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    networks:
      - app-network

  backend:
    build: ./backend
    environment:
      - DB_HOST=db
      - DB_PORT=5432
    depends_on:
      - db
    networks:
      - app-network

  db:
    image: postgres:13
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: myapp
    networks:
      - app-network

volumes:
  db-data:

networks:
  app-network:
    driver: bridge

### 🔍 Giải thích:

- depends_on: Đảm bảo thứ tự khởi động giữa các service.
- build: Build image từ thư mục chứa Dockerfile.
- networks: Tạo mạng riêng để các container giao tiếp nội bộ.
- volumes: Lưu trữ dữ liệu bền vững cho database

# 🚀 Các lệnh Docker nâng cao

## 🧭 Quản lý mạng

docker network ls                     # Liệt kê các mạng Docker
docker network inspect <network>     # Xem chi tiết mạng
docker network create my-network     # Tạo mạng bridge mới
docker network connect my-network <container>  # Kết nối container vào mạng

## 💾 Quản lý volume

docker volume ls                     # Liệt kê các volume
docker volume create my-volume       # Tạo volume mới
docker volume inspect my-volume      # Xem chi tiết volume
docker volume rm my-volume           # Xóa volume

## 🔍 Kiểm tra hệ thống

docker inspect <container_or_image>  # Xem thông tin chi tiết
docker stats                         # Theo dõi tài nguyên container theo thời gian thực

## 📁 Sao chép file

docker cp <container>:/path/in/container /host/path   # Copy từ container ra host
docker cp /host/path <container>:/path/in/container   # Copy từ host vào container

## 🧹 Dọn dẹp hệ thống

docker system prune               # Xóa container, network, image không dùng
docker system df                  # Xem dung lượng Docker đang sử dụng

