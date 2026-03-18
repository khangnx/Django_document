🖥️ Horizon là gì?
• 	Horizon là package chính thức của Laravel để quản lý Redis queues.
• 	Nó cung cấp dashboard đẹp mắt (dùng Vue.js) hiển thị real-time: số lượng job, job thành công/thất bại, retry, throughput, runtime metrics.
• 	Cấu hình bằng code: tất cả worker, supervisor, balancing được định nghĩa trong file .
• 	Miễn phí, open-source, dễ tích hợp vào dự án Laravel.

⚙️ Cài đặt & Cấu hình
1. 	Yêu cầu: Laravel 8+, Redis đã cài đặt và chạy.
2. 	Cài đặt:
```
composer require laravel/horizon
php artisan horizon:install
php artisan migrate
```
2. 	→ Tạo bảng quản lý job và file config/horizon.php
3. 	Chạy Horizon:
```
php artisan horizon
```
3. 	→ Mở dashboard tại /horizon
4. 	Cấu hình supervisor trong : config/horizon.php
```
'supervisor-1' => [
    'connection' => 'redis',
    'queue' => ['default'],
    'balance' => 'auto',
    'processes' => 3,
    'tries' => 3,
],
```

📊 Tính năng chính
• 	Dashboard real-time: theo dõi job đang chạy, job fail, retry.
• 	Metrics: throughput (số job/giây), runtime trung bình, failure rate.
• 	Job management: retry job fail, xóa job, tag job để dễ quản lý.
• 	Balancing strategies:
• 	Simple balancing: chia đều job cho worker.
• 	Auto balancing: tự động điều chỉnh theo tải.
• 	Authorization: kiểm soát quyền truy cập dashboard bằng .

📌 Ví dụ thực tế
• 	E-commerce: dùng queue để gửi email xác nhận đơn hàng. Horizon giúp giám sát số lượng email gửi thành công/thất bại, retry khi lỗi SMTP.
• 	CRM: xử lý job import dữ liệu lớn. Horizon hiển thị throughput, giúp tối ưu số lượng worker.
• 	Notification system: theo dõi job push notification, dễ phát hiện lỗi khi có nhiều job fail liên tục.

🚨 Lợi ích & Rủi ro
• 	Lợi ích: trực quan, dễ quản lý, tối ưu hiệu năng, giảm downtime.
• 	Rủi ro: chỉ hỗ trợ Redis (không dùng được cho database queue hoặc SQS), cần cấu hình Redis ổn định.
• 	Khuyến nghị: nếu hệ thống queue lớn, nhiều job phức tạp → Horizon là lựa chọn tối ưu.

👉 Tóm lại: Horizon là công cụ giám sát queue mạnh mẽ nhất trong hệ sinh thái Laravel, đặc biệt khi bạn dùng Redis. Nó không chỉ giúp theo dõi job mà còn tối ưu worker và xử lý lỗi nhanh chóng.
