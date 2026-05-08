# API Rate Limiting / Throttling trong Microservices với Laravel / Ruby on Rails trên AWS

API rate limiting/throttling là cơ chế kiểm soát lưu lượng truy cập vào một API để đảm bảo hệ thống hoạt động ổn định, tránh bị quá tải. Nó đề cập đến việc giới hạn số lượng request mà một client có thể gửi trong một khoảng thời gian nhất định.

---

## 📌 Ý nghĩa chính

### Rate limiting
Đặt ngưỡng tối đa cho số request  
Ví dụ: `100 request/phút`

Nếu vượt quá, hệ thống sẽ từ chối hoặc trả về lỗi:

```http
HTTP 429 Too Many Requests
```

### Throttling
Điều chỉnh tốc độ xử lý request, có thể:

- Làm chậm request
- Xếp hàng request
- Phân bổ đều request

Thay vì chặn hoàn toàn.

---

## ⚙️ Mục đích

- Bảo vệ API khỏi bị lạm dụng hoặc tấn công (DDoS, spam)
- Đảm bảo tài nguyên được phân bổ công bằng
- Giữ hiệu năng hệ thống ổn định
- Tránh quá tải database/server
- Kiểm soát chi phí hạ tầng cloud

---

## 🛠️ Ví dụ thực tế

### Ví dụ 1 — Email API
Cho phép tối đa:

```text
500 email/ngày
```

cho mỗi tài khoản.

---

### Ví dụ 2 — Weather API
Cho phép:

```text
60 request/phút
```

để tránh spam request liên tục.

---

# 🚀 Triển khai trong hệ thống Microservices dùng Laravel hoặc Ruby on Rails trên AWS

Có thể triển khai ở:

1. Application Layer
2. Infrastructure Layer

Thông thường production sẽ kết hợp cả hai.

---

# 🧩 1. Application Layer (Laravel / Rails)

---

## Laravel

Laravel có sẵn middleware:

```php
ThrottleRequests
```

Ví dụ:

```php
Route::middleware('throttle:60,1')->group(function () {
    Route::get('/users', 'UserController@index');
});
```

Ý nghĩa:

```text
60 request / 1 phút
```

---

### Theo user hoặc API token

```php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(100)->by($request->user()?->id ?: $request->ip());
});
```

Có thể limit theo:

- User ID
- API key
- IP address
- Tenant
- Subscription plan

---

## Ruby on Rails

Rails thường dùng gem:

```ruby
rack-attack
```

Ví dụ:

```ruby
Rack::Attack.throttle('req/ip', limit: 60, period: 1.minute) do |req|
  req.ip
end
```

Ý nghĩa:

```text
60 request/phút theo IP
```

---

## Redis-based Rate Limiting

Khi microservices scale lớn, thường dùng:

```text
Redis + Token Bucket / Leaky Bucket
```

để shared state giữa nhiều instance/container.

---

## Token Bucket Algorithm

Ý tưởng:

- Mỗi user có một bucket token
- Request sẽ consume token
- Token tự refill theo thời gian
- Hết token → reject request

---

## Leaky Bucket Algorithm

Ý tưởng:

- Request đi vào queue
- Xử lý với tốc độ cố định
- Nếu queue đầy → reject

Giúp traffic mượt hơn.

---

# ☁️ 2. Infrastructure Layer trên AWS

---

# AWS API Gateway

Đây là nơi phổ biến nhất để triển khai rate limiting.

---

## API Gateway Usage Plans

Có thể cấu hình:

- Requests/second
- Burst limit
- Requests/day
- Requests/month

Ví dụ:

```text
100 req/sec
Burst: 200
Quota: 1,000,000/month
```

---

## API Keys

Có thể tạo API key riêng cho từng client:

- Mobile app
- Web app
- Partner API
- Premium customer

Mỗi key có limit riêng.

---

# AWS WAF (Web Application Firewall)

WAF hỗ trợ:

- IP rate limiting
- Anti-DDoS
- Bot protection

Ví dụ:

```text
Block IP nếu > 2000 request / 5 phút
```

---

## Kiến trúc thường dùng

```text
Internet
   ↓
AWS WAF
   ↓
API Gateway
   ↓
ALB
   ↓
ECS / EKS / EC2
   ↓
Laravel / Rails Microservices
```

---

# Redis với AWS ElastiCache

Khi cần:

- Shared rate limiting
- Distributed throttling
- Multi-instance consistency

thì dùng:

```text
AWS ElastiCache Redis
```

---

## Flow hoạt động

```text
Request đến service
    ↓
Check Redis counter
    ↓
Nếu vượt limit → HTTP 429
    ↓
Nếu hợp lệ → xử lý request
```

---

# 🧠 Kiến trúc thực tế cho Microservices

## Layer 1 — API Gateway

Giới hạn coarse-grained:

- Requests/sec
- API quota
- Public API protection

---

## Layer 2 — Service Middleware

Giới hạn business-specific:

- Theo user
- Theo role
- Theo subscription
- Theo endpoint

---

## Layer 3 — Redis Distributed Limiter

Giúp đồng bộ giữa:

- Nhiều ECS container
- Nhiều EC2 instance
- Multiple AZ

---

# 📦 Ví dụ kiến trúc AWS ECS

```text
Client
   ↓
CloudFront
   ↓
AWS WAF
   ↓
API Gateway
   ↓
Application Load Balancer
   ↓
ECS Cluster
   ├── Laravel Service
   ├── Rails Service
   └── Auth Service
           ↓
       ElastiCache Redis
```

---

# 🔥 Best Practices

## 1. Không chỉ limit theo IP

Nên kết hợp:

- User ID
- API Key
- JWT subject
- Tenant ID

---

## 2. Phân biệt Free vs Premium

Ví dụ:

| Plan | Rate Limit |
|---|---|
| Free | 60 req/min |
| Premium | 1000 req/min |

---

## 3. Trả về header rõ ràng

Ví dụ:

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 20
Retry-After: 60
```

---

## 4. Dùng Redis thay vì Memory

Memory local sẽ sai khi:

- Scale nhiều container
- Multiple servers
- Auto scaling

---

## 5. Log và Monitoring

Dùng:

- CloudWatch
- Grafana
- Prometheus

để monitor:

- 429 rate
- Suspicious traffic
- API abuse

---

# ✅ Tóm tắt

| Layer | Công nghệ | Vai trò |
|---|---|---|
| Application | Laravel Throttle / Rack::Attack | Business rate limiting |
| Shared State | Redis ElastiCache | Distributed counter |
| Infrastructure | API Gateway | Global throttling |
| Security | AWS WAF | DDoS & IP protection |

---

# 🎯 Kết luận

Trong hệ thống microservices trên AWS:

- Middleware Laravel/Rails dùng để kiểm soát logic chi tiết
- API Gateway dùng để limit ở mức global
- Redis dùng để đồng bộ counter giữa nhiều service/container
- AWS WAF dùng để chống spam/DDoS

Production system lớn thường kết hợp cả 4 layer để đạt:

- Scalability
- Stability
- Security
- Fair resource allocation
