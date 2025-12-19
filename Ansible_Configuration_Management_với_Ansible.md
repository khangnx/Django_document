# 📘 Configuration Management với Ansible

## 1. Giới thiệu Ansible
- **Ansible**: Công cụ tự động hóa mã nguồn mở, ra mắt năm 2012.  
- **Agentless**: Không cần cài đặt agent, chỉ dùng SSH để kết nối.  
- **Ứng dụng chính**:
  - Quản lý cấu hình (*Configuration Management*)  
  - Triển khai ứng dụng (*Application Deployment*)  
  - Tự động hóa hạ tầng IT (*IT Automation*)  

---

## 2. Khái niệm cơ bản
- **Inventory**: Danh sách máy chủ Ansible quản lý.  
- **Ad-hoc Commands**: Lệnh nhanh để thực hiện tác vụ đơn giản.  
- **Playbooks**: Kịch bản YAML mô tả cấu hình và hành động.  
- **Modules**: Thành phần có sẵn để thực hiện tác vụ (copy file, cài package, quản lý service).  

---

## 3. Quy trình sử dụng
1. **Cài đặt Ansible** trên máy điều khiển (Control Node).  
2. **Khai báo Inventory** (danh sách máy chủ).  
3. **Viết Playbook** bằng YAML để mô tả cấu hình.  
4. **Chạy Playbook** để áp dụng cấu hình lên các máy chủ.  

---

## 4. Ưu điểm
| Tiêu chí       | Điểm mạnh |
|----------------|-----------|
| Triển khai     | Agentless, dễ dàng kết nối qua SSH |
| Ngôn ngữ       | YAML – dễ đọc, dễ viết |
| Khả năng mở rộng | Quản lý hàng nghìn máy chủ |
| Tích hợp       | Hỗ trợ đa nền tảng (Linux, Windows, Cloud) |
| Bảo mật        | Sử dụng SSH, không cần mở thêm port |

---

## 5. Thách thức
- **Quy mô lớn**: Cần tối ưu Inventory và Playbooks.  
- **Debug YAML**: Dễ lỗi thụt dòng.  
- **Bảo mật SSH**: Quản lý key SSH cẩn thận.  

---

## 6. Ví dụ cơ bản: Quản lý Nginx
### Cấu trúc

```
ansible-project/
├── inventory.ini
├── playbook.yml
└── files/nginx.conf
```

## 7. Tổ chức chuyên nghiệp với Roles
### Cấu trúc

```
roles/
└── nginx/
    ├── tasks/main.yml
    ├── handlers/main.yml
    ├── files/nginx.conf
    ├── templates/index.html.j2
    └── vars/main.yml
	
```
### Playbook chính
```
- name: Triển khai webserver với Nginx
  hosts: webservers
  become: true
  roles:
    - nginx
```

## 8. Ví dụ nâng cao: LAMP Stack (Linux, Apache, MySQL, PHP)
### Playbook chính

```
- name: Triển khai LAMP stack
  hosts: webservers
  become: true
  roles:
    - apache
    - mysql
    - php
```

### Role Apache
```
- name: Cài đặt Apache
  apt:
    name: apache2
    state: present
    update_cache: yes

```
### Role MySQL
```
- name: Cài đặt MySQL
  apt:
    name: mysql-server
    state: present
    update_cache: yes

- name: Tạo database
  mysql_db:
    name: myapp_db
    state: present
```

### Role PHP
```
- name: Cài đặt PHP
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - php
    - libapache2-mod-php
    - php-mysql


```
## 9. Ứng dụng thực tế
- Triển khai WordPress: Kết hợp LAMP stack + WordPress.
- Quản lý hạ tầng Cloud: AWS, Azure, GCP.
- CI/CD pipelines: Tích hợp với Jenkins, GitLab CI.

## 🎯 Kết luận
- Ansible là công cụ mạnh mẽ cho Configuration Management.
- Playbooks phù hợp cho dự án nhỏ.
- Roles giúp tổ chức chuyên nghiệp, dễ mở rộng.
- Có thể triển khai từ dịch vụ đơn giản (Nginx) đến ứng dụng phức tạp (LAMP, WordPress).
