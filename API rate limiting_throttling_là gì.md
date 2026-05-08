```API rate limiting/throttling là cơ chế kiểm soát lưu lượng truy cập vào một API để đảm bảo hệ thống hoạt động ổn định, tránh bị quá tải. Nó đề cập đến việc giới hạn số lượng request mà một client có thể gửi trong một khoảng thời gian nhất định.

📌 Ý nghĩa chính
Rate limiting: Đặt ngưỡng tối đa cho số request (ví dụ: 100 request/phút). Nếu vượt quá, hệ thống sẽ từ chối hoặc trả về lỗi (thường là HTTP 429 Too Many Requests).

Throttling: Điều chỉnh tốc độ xử lý request, có thể làm chậm hoặc phân bổ đều các request thay vì chặn hẳn.

⚙️ Mục đích
Bảo vệ API khỏi bị lạm dụng hoặc tấn công (ví dụ: DDoS).

Đảm bảo tài nguyên hệ thống được phân bổ công bằng giữa nhiều người dùng.

Giữ cho hiệu năng ổn định, tránh tình trạng nghẽn mạng hoặc server quá tải.

🛠️ Ví dụ thực tế
Một dịch vụ email API cho phép tối đa 500 email/ngày cho mỗi tài khoản.

Một API thời tiết chỉ cho phép 60 request/phút để tránh việc người dùng liên tục spam dữ liệu.

👉 Nói ngắn gọn: Rate limiting/throttling là cách để kiểm soát tần suất gọi API nhằm bảo vệ hệ thống và đảm bảo công bằng cho tất cả người dùng.
```

Vậy một hệ thống microservices dùng Laravel hoac ruby on rails thì làm sao triển khai cơ chế này ? Nếu dùng AWS
Triển khai rate limiting/throttling trong hệ thống microservices dùng Laravel hoặc Ruby on Rails trên AWS có nhiều cách, tùy vào mức độ bạn muốn kiểm soát ở application layer hay infrastructure layer.

🛠️ 1. Ở Application Layer (Laravel / Rails)
Middleware:

Laravel có sẵn middleware ThrottleRequests để giới hạn số request theo thời gian. Bạn có thể cấu hình trong routes/api.php như:

php
Route::middleware('throttle:60,1')->group(function () {
    Route::get('/users', 'UserController@index');
});
→ Giới hạn 60 request mỗi phút cho route này.

Rails có thể dùng gem như Rack::Attack để định nghĩa rule rate limiting:

ruby
Rack::Attack.throttle('req/ip', limit: 60, period: 1.minute) do |req|
  req.ip
end
→ Giới hạn 60 request/phút theo IP.

Token bucket / Leaky bucket algorithm:
Nếu cần kiểm soát phức tạp hơn (ví dụ: phân biệt user tier: free vs premium), bạn có thể tự implement bằng Redis để lưu số lượng request và reset theo thời gian.

☁️ 2. Ở Infrastructure Layer (AWS)
AWS cung cấp nhiều dịch vụ hỗ trợ rate limiting mà bạn có thể tận dụng:

API Gateway

Cho phép cấu hình Usage Plans và API Keys để giới hạn số request/second hoặc request/day cho từng client.

Tích hợp dễ dàng với Lambda, ECS, hoặc backend Laravel/Rails.

AWS WAF (Web Application Firewall)

Có thể đặt rule để giới hạn số request từ một IP trong khoảng thời gian nhất định.

Hữu ích để chống DDoS hoặc spam.

Elastic Load Balancer (ALB/NLB)

Không có rate limiting trực tiếp, nhưng có thể kết hợp với WAF hoặc API Gateway để kiểm soát.

Redis/Memcached trên ElastiCache

Dùng để lưu trạng thái request count cho từng user/IP, triển khai custom throttling logic.

📊 Kiến trúc gợi ý
Client → AWS API Gateway (rate limiting theo plan)

API Gateway → Microservices (Laravel/Rails)

Trong service, dùng middleware (ThrottleRequests hoặc Rack::Attack) để kiểm soát chi tiết hơn.

Nếu cần logic phức tạp → Redis (ElastiCache) để track request count.

Nếu chống spam/DDoS → thêm AWS WAF trước API Gateway.

👉 Tóm lại:

Nhanh gọn: Dùng middleware có sẵn trong Laravel/Rails.

Quy mô lớn, nhiều client: Dùng AWS API Gateway Usage Plans.

Bảo mật nâng cao: Kết hợp AWS WAF + Redis để kiểm soát chi tiết.
