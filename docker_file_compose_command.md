

## 📦 Docker Compose

### 🔧 Cấu trúc cơ bản của file `docker-compose.yml`

```version: '3.8'
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
```

### 🧩 Giải thích các thành phần:

- services: Khai báo các container.
- image: Tên image Docker cần dùng.
- ports: Mở cổng từ host → container (host:container).
- volumes: Gắn thư mục từ host vào container.
- environment: Biến môi trường truyền vào container.

---

## 🛠️ Các lệnh Docker cơ bản

### 📦 Image & Container

```docker build -t myimage .           # Build image từ Dockerfile
docker run -d -p 8080:80 myimage    # Chạy container từ image
docker ps                           # Liệt kê container đang chạy
docker ps -a                        # Liệt kê tất cả container
docker stop <container_id>         # Dừng container
docker rm <container_id>           # Xóa container
docker rmi <image_id>              # Xóa image
```

### 🔍 Kiểm tra & Gỡ lỗi
```
docker exec -it <container_id> bash   # Truy cập vào container
docker logs <container_id>           # Xem log container
```

---

## 🧪 Docker Compose Commands
```

docker-compose up -d       # Khởi động các service ở chế độ nền
docker-compose down        # Dừng và xóa container, network
docker-compose build       # Build lại các image
docker-compose logs        # Xem log các service
docker-compose ps          # Liệt kê các container đang chạy
```

---

## 📘 Ví dụ nâng cao về `docker-compose.yml`

```version: '3.8'

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
```

### 🔍 Giải thích:
```
- depends_on: Đảm bảo thứ tự khởi động giữa các service.
- build: Build image từ thư mục chứa Dockerfile.
- networks: Tạo mạng riêng để các container giao tiếp nội bộ.
- volumes: Lưu trữ dữ liệu bền vững cho database
Tức là: Volumes là một cơ chế lưu trữ dữ liệu bên ngoài container, được Docker quản lý. Khi bạn khai báo volumes, bạn đang tạo một vùng lưu trữ mà container có thể đọc/ghi dữ liệu vào, nhưng dữ liệu đó không bị mất khi container bị xóa hoặc khởi động lại. 
Ví dụ khi container badabase bị xóa, volumes của nó vẫn còn tại cd /var/lib/docker/volumes/my_db_data/_data, ta vào đây mà lấy lại.
```

# 🚀 Các lệnh Docker nâng cao

## 🧭 Quản lý mạng
```
docker network ls                     # Liệt kê các mạng Docker
docker network inspect <network>     # Xem chi tiết mạng
docker network create my-network     # Tạo mạng bridge mới
docker network connect my-network <container>  # Kết nối container vào mạng
```

## 💾 Quản lý volume
```
docker volume ls                     # Liệt kê các volume
docker volume create my-volume       # Tạo volume mới
docker volume inspect my-volume      # Xem chi tiết volume
docker volume rm my-volume           # Xóa volume
```

## 🔍 Kiểm tra hệ thống
```
docker inspect <container_or_image>  # Xem thông tin chi tiết
docker stats                         # Theo dõi tài nguyên container theo thời gian thực
```

## 📁 Sao chép file
```
docker cp <container>:/path/in/container /host/path   # Copy từ container ra host
docker cp /host/path <container>:/path/in/container   # Copy từ host vào container
```

## 🧹 Dọn dẹp hệ thống
```
docker system prune               # Xóa container, network, image không dùng
docker system df                  # Xem dung lượng Docker đang sử dụng
```


===========================
# 📦 TỔNG HỢP KIẾN THỨC DOCKER
===========================

## I. Dockerfile là gì?
---------------------
Dockerfile là một tập tin văn bản chứa các lệnh để xây dựng một Docker image. Nó giống như một công thức để tạo ra môi trường chạy ứng dụng.

### Ví dụ Dockerfile cho ứng dụng Node.js:
---------------------------------------
```FROM node:18
WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

## II. Các lệnh phổ biến trong Dockerfile
---------------------------------------
| Lệnh        | Ý nghĩa |
|-------------|--------|
| FROM        | Chỉ định image nền để xây dựng image mới |
| LABEL       | Gán metadata cho image |
| RUN         | Thực thi lệnh trong quá trình build image |
| CMD         | Lệnh mặc định khi container chạy |
| ENTRYPOINT  | Giống CMD nhưng không bị ghi đè khi truyền tham số |
| COPY        | Sao chép file từ máy host vào image |
| ADD         | Giống COPY nhưng hỗ trợ giải nén và tải từ URL |
| WORKDIR     | Thiết lập thư mục làm việc trong container |
| ENV         | Thiết lập biến môi trường |
| EXPOSE      | Mở cổng để container giao tiếp ra ngoài |
| VOLUME      | Tạo điểm mount cho volume |
| USER        | Chỉ định người dùng để chạy lệnh |
| ARG         | Khai báo biến dùng trong quá trình build |
| HEALTHCHECK | Kiểm tra tình trạng container |
| ONBUILD     | Thiết lập lệnh thực thi khi image được dùng làm base image |

### III. Docker Compose là gì?
---------------------------
Docker Compose dùng để định nghĩa và chạy nhiều container cùng lúc. Thường dùng cho các ứng dụng có nhiều thành phần như web server, database, cache...

Ví dụ docker-compose.yml cho Node.js + MongoDB:
------------------------------------------------
```version: '3.8'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - mongo
    environment:
      - MONGO_URL=mongodb://mongo:27017/mydb

  mongo:
    image: mongo:latest
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
```

## IV. Có thể chỉ dùng Dockerfile mà không cần Docker Compose không?
------------------------------------------------------------------
✅ Có. Nếu ứng dụng chỉ có một service đơn giản, bạn có thể:

1. Viết Dockerfile
2. Build image:
   
   docker build -t ten-image .
   
5. Chạy container:

   docker run -p 3000:3000 ten-image

❌ Nên dùng Docker Compose khi:
- Có nhiều service (web + database + cache)
- Cần cấu hình mạng, volume, biến môi trường phức tạp

Tóm lại:
--------
| Trường hợp | Dùng Dockerfile | Dùng Docker Compose |
|------------|------------------|----------------------|
| 1 service đơn giản | ✅ | ❌ |
| Nhiều service       | ❌ | ✅ |
| Cấu hình phức tạp   | ❌ | ✅ |


# Cách gọi dockerFile từ DockerCompose
```
my-app/
├── Dockerfile
├── app.js
├── package.json
└── docker-compose.yml
```
Thì sẽ gọi như sau:

```
version: '3.8'

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
```

# 🧩 Giải thích:

build:: Chỉ định Docker Compose sẽ build image từ Dockerfile.

context: . → thư mục hiện tại là nơi chứa Dockerfile và mã nguồn.
dockerfile: Dockerfile → tên file Dockerfile (mặc định là Dockerfile, có thể bỏ dòng này nếu tên không đổi).


ports:: ánh xạ cổng từ container ra ngoài.
environment:: thiết lập biến môi trường cho container.

