# 🧠 Giới thiệu về Redis

**Redis** (REmote DIctionary Server) là một **hệ quản trị cơ sở dữ liệu NoSQL** dạng **key-value** hoạt động **chủ yếu trong bộ nhớ RAM**, giúp xử lý dữ liệu **cực nhanh**.  
Redis thường được dùng để **tăng tốc ứng dụng**, **lưu bộ nhớ đệm (cache)**, **hàng đợi (queue)** hoặc **phiên đăng nhập (session store)**.

---

## ⚙️ Redis hoạt động như thế nào?

- **Lưu trữ trong RAM:** Redis lưu dữ liệu trực tiếp trong bộ nhớ RAM thay vì ổ cứng, giúp truy xuất cực nhanh (tính bằng micro giây).  
- **Đơn luồng (Single-threaded):** Redis xử lý các yêu cầu theo thứ tự, tránh xung đột dữ liệu mà không cần cơ chế khóa phức tạp.  
- **Persistence (Lưu trữ lâu dài):**
  - **RDB Snapshot:** Ghi dữ liệu xuống đĩa theo chu kỳ.
  - **AOF (Append Only File):** Ghi log từng thay đổi để khôi phục khi cần.  
- **Replication & Clustering:** Redis hỗ trợ nhân bản dữ liệu (replication) và phân mảnh (sharding) để mở rộng quy mô, tăng hiệu năng và độ tin cậy.

---

## 🧩 Các kiểu dữ liệu chính trong Redis

| Kiểu dữ liệu | Mô tả | Ví dụ sử dụng |
|--------------|-------|----------------|
| **String** | Chuỗi ký tự (văn bản, số, JSON, v.v.) | Lưu token, cache dữ liệu |
| **List** | Danh sách có thứ tự (giống mảng) | Hàng đợi công việc (queue) |
| **Set** | Tập hợp không trùng lặp | Lưu danh sách bạn bè, thẻ tag |
| **Sorted Set (ZSet)** | Tập hợp có xếp hạng (kèm điểm số) | Bảng xếp hạng trò chơi (leaderboard) |
| **Hash** | Tập key-value con trong 1 key | Lưu thông tin người dùng |
| **Stream** | Chuỗi dữ liệu theo thời gian | Xử lý log hoặc sự kiện theo thời gian thực |

---

## 🚀 Ứng dụng phổ biến của Redis

- **Caching:** Tăng tốc ứng dụng bằng cách lưu dữ liệu truy cập thường xuyên.  
- **Session Store:** Lưu thông tin đăng nhập người dùng.  
- **Message Queue:** Xử lý hàng đợi bất đồng bộ.  
- **Leaderboard/Game Ranking:** Dùng *Sorted Set* để xếp hạng người chơi.  
- **Pub/Sub:** Xây dựng hệ thống thông báo thời gian thực.

---

## 📌 Ưu điểm nổi bật

- ⚡ **Tốc độ cực nhanh:** Hàng trăm nghìn yêu cầu mỗi giây.  
- 💡 **Hỗ trợ nhiều kiểu dữ liệu:** String, List, Set, Hash, Sorted Set, Stream...  
- 🔗 **Tích hợp dễ dàng:** Hoạt động tốt với PHP, Python, Node.js, Java, Go...  
- 📈 **Khả năng mở rộng cao:** Hỗ trợ clustering và replication.  

---

## 🏁 Kết luận

Redis là công cụ mạnh mẽ giúp **tăng hiệu năng**, **giảm tải cơ sở dữ liệu chính**, và **xử lý dữ liệu thời gian thực** hiệu quả.  
Nó là lựa chọn lý tưởng cho các hệ thống cần tốc độ cao và khả năng mở rộng linh hoạt.


# Redis mặc định lưu trữ dữ liệu trong bộ nhớ RAM, nên nếu bạn tắt server hoặc Redis bị dừng đột ngột, thì dữ liệu sẽ bị mất — trừ khi bạn đã cấu hình cơ chế lưu trữ lâu dài (persistence). Redis cung cấp hai cơ chế để tránh mất dữ liệu:

## 🧷 1. RDB (Redis Database Backup)
- Redis sẽ tự động tạo snapshot (ảnh chụp) của dữ liệu tại các thời điểm nhất định và lưu vào file .rdb.
- Ưu điểm: nhẹ, nhanh, phù hợp để backup định kỳ.
- Nhược điểm: nếu Redis bị tắt đột ngột, bạn có thể mất dữ liệu mới nhất chưa được snapshot.

## 📝 2. AOF (Append Only File)
- Redis sẽ ghi lại mọi lệnh ghi dữ liệu (write command) vào một file log.
- Khi Redis khởi động lại, nó sẽ phát lại các lệnh này để khôi phục dữ liệu.
- Ưu điểm: độ an toàn cao hơn, ít mất dữ liệu hơn.
- Nhược điểm: file log có thể lớn, cần cấu hình để tối ưu hiệu suất.

## 🔧 3. Kết hợp cả RDB và AOF
Redis cho phép bạn kích hoạt cả hai cơ chế để tận dụng ưu điểm của từng loại: RDB cho backup định kỳ, AOF cho khôi phục chính xác.


# Ngắn gọn:
## ❌ Redis không cache HTML/CSS/JS ở client (trình duyệt).
## ✔️ Redis chỉ cache ở server-side, để backend trả dữ liệu nhanh hơn.

```Giải thích chi tiết
1. Redis làm được gì?

Redis là in-memory database/cache dùng trên server. Nó dùng để cache:

Kết quả query database

API response

Session

Token

Các object JSON, string, list, set...

Redis không gửi trực tiếp HTML/CSS/JS xuống trình duyệt để client tự cache.

2. Cache HTML/CSS/JS ở client là do ai?

Đó là nhiệm vụ của:

-- ✔ HTTP Cache (Client-Side Cache)

Trình duyệt lưu file HTML/CSS/JS dựa vào header:

Cache-Control

ETag

Last-Modified

Expires

Ví dụ: server gửi header:

Cache-Control: public, max-age=31536000


→ trình duyệt lưu file JS/CSS trong 1 năm.

Redis không tham gia phần này.

3. Vậy Redis có liên quan không?

Redis có thể cache HTML đã render sẵn trên server để trả về nhanh hơn.

Ví dụ backend:

Render ra HTML ⇒ lưu vào Redis ⇒ trả nhanh cho client.

Nhưng trình duyệt vẫn cache theo HTTP headers, không phải Redis.

Kết luận
Mục đích	                                          Redis có hỗ trợ?
Cache dữ liệu backend	                                 ✔ Có
Cache HTML đã render ở server	                         ✔ Có
Cache file HTML/CSS/JS tại trình duyệt	              ❌ Không
Thay thế CDN	                                        ❌ Không
```

**Memcached cũng giống Redis ở điểm này:**  
❌ *Không cache HTML/CSS/JS ở **client (browser)**.*  
✔️ *Chỉ cache ở **server-side** để backend lấy dữ liệu nhanh hơn.*

---

## **1. Memcached làm được gì?**
- Cache key–value đơn giản (string, serialized object)  
- Tăng tốc backend, giảm tải database  
- Lưu phiên (session)  
- Cache API response  

Memcached **không thể** điều khiển cache trên trình duyệt.

---

## **2. Cache HTML/CSS/JS ở client là nhiệm vụ của…?**
**HTTP Cache + CDN + Web Server (Nginx/Apache)**  
Trình duyệt cache file static nhờ các header:

## **3. Memcached có liên quan không?**
Có thể dùng Memcached để cache:

- HTML đã render sẵn  
- Kết quả template engine  
- Fragment cache  

→ Vẫn là **server-side**, không phải browser cache.

---

## **4. So sánh nhanh Redis vs Memcached**
| Tính năng | Redis | Memcached |
|----------|--------|-----------|
| Cache server-side | ✔ | ✔ |
| Cache client-side (browser) | ❌ | ❌ |
| Data type | Nhiều | Chỉ key-value |
| Persistence (lưu file) | ✔ | ❌ |
| Pub/Sub | ✔ | ❌ |

---

## **Kết luận**
Redis ❌  
Memcached ❌  

👉 **Không công nghệ cache nào ở backend có thể tự động cache HTML/CSS/JS ở client.**  
👉 Browser chỉ nhận cache thông qua *HTTP headers* hoặc *CDN*.





# Cơ chế Redis Cache trong Ruby on Rails (Localhost & AWS)

Dưới đây là **giải thích rõ ràng – từ gốc rễ đến thực tế vận hành** về **cơ chế cache Redis khi dùng Ruby on Rails**, áp dụng **cả localhost lẫn AWS (ElastiCache / EC2 Redis)**. Nội dung đi theo **flow tư duy hệ thống**, không chỉ liệt kê API.

---

## 1. Redis cache trong Rails là gì (đặt đúng vai trò)

Trong Rails, **Redis thường được dùng như một cache store** để:

* Cache **kết quả SQL** (ActiveRecord objects, IDs…)
* Cache **HTML fragment**
* Cache **JSON API response**
* Cache **data tính toán nặng**

👉 Redis **KHÔNG tự biết SQL hay HTML**, nó chỉ lưu **key → value**.

Rails quyết định:

* Cache cái gì
* Khi nào ghi
* Khi nào đọc
* Khi nào xóa / refresh

---

## 2. Kiến trúc tổng thể (Local & AWS)

```text
Request
   ↓
Rails App
   ↓
Cache Store (Redis)
   ↓
Database (PostgreSQL / MySQL)
```

### Flow chuẩn

1. Request vào Rails
2. Rails **check Redis trước**
3. Có cache → trả về ngay 🚀
4. Không có cache → query DB → render → **ghi vào Redis**
5. Trả response

➡️ **DB chỉ bị hit khi cache miss**

---

## 3. Rails kết nối Redis như thế nào?

### `config/environments/production.rb`

```ruby
config.cache_store = :redis_cache_store, {
  url: ENV["REDIS_URL"],
  connect_timeout: 5,
  read_timeout: 2,
  write_timeout: 2,
  reconnect_attempts: 2
}
```

### Localhost

```bash
REDIS_URL=redis://localhost:6379/0
```

### AWS (ElastiCache)

```bash
REDIS_URL=redis://my-elasticache.xxxxxx.ap-southeast-1.cache.amazonaws.com:6379
```

👉 **Code Rails giống nhau**, chỉ khác endpoint.

---

## 4. Cache SQL hoạt động ra sao?

### Ví dụ phổ biến

```ruby
Rails.cache.fetch("users_active", expires_in: 10.minutes) do
  User.where(active: true).to_a
end
```

### Flow chi tiết

```text
1. Rails hỏi Redis: có key "users_active" không?
2. Nếu có:
     → deserialize → trả kết quả
3. Nếu không:
     → query DB
     → serialize object
     → ghi vào Redis
     → trả kết quả
```

---

## 5. Khi nào cache REFRESH (quan trọng nhất)

Có **4 cơ chế chính**.

---

### ① TTL – hết hạn tự động (phổ biến nhất)

```ruby
expires_in: 10.minutes
```

⏱ Redis **tự xóa key** khi hết thời gian.

```text
Time up
   ↓
Key biến mất
   ↓
Request tiếp theo → query DB → cache lại
```

👉 **Cách an toàn – dễ kiểm soát – dùng nhiều nhất**

---

### ② Manual delete – xóa thủ công khi data thay đổi

#### Ví dụ: user được update

```ruby
after_commit :clear_cache

def clear_cache
  Rails.cache.delete("users_active")
end
```

Flow:

```text
Update DB
   ↓
Xóa cache
   ↓
Request sau → cache miss → query mới
```

👉 **Chuẩn cho dữ liệu quan trọng**

---

### ③ Cache versioning – đổi key khi data đổi

```ruby
cache_key = "users_active_v#{User.maximum(:updated_at).to_i}"

Rails.cache.fetch(cache_key) do
  User.where(active: true).to_a
end
```

* ✔ Không cần delete
* ✔ Cache cũ tự “chết”
* ❌ Nhiều key hơn

---

### ④ Low-level race condition protection

```ruby
Rails.cache.fetch("stats", expires_in: 5.minutes, race_condition_ttl: 10) do
  heavy_query
end
```

👉 Tránh **thundering herd** (nhiều request cùng refresh cache)

---

## 6. Cache HTML / Fragment cache hoạt động thế nào?

### Ví dụ trong view

```erb
<% cache ["user-profile", @user] do %>
  <%= render "profile", user: @user %>
<% end %>
```

Rails tự tạo key:

```text
views/user-profile/users/42-20251216120000
```

➡️ Khi `@user.updated_at` thay đổi → **key đổi → cache auto refresh**

👉 Đây là **cache thông minh nhất của Rails**

---

## 7. Cache API / JSON

```ruby
def index
  render json: Rails.cache.fetch("users_api", expires_in: 1.minute) {
    User.all.as_json
  }
end
```

Flow:

```text
Request → Redis → JSON → Response
```

---

## 8. Redis KHÔNG tự refresh – Rails quyết định

❗ Redis không biết DB thay đổi
❗ Redis không tự sync SQL

👉 Rails phải:

* Set TTL
* Delete cache
* Hoặc đổi key

---

## 9. So sánh Cache Local vs AWS

| Tiêu chí       | Local Redis | AWS ElastiCache |
| -------------- | ----------- | --------------- |
| Tốc độ         | Rất nhanh   | Rất nhanh       |
| Bền dữ liệu    | Thấp        | Cao             |
| Scale          | ❌           | ✅               |
| HA             | ❌           | ✅               |
| Cơ chế refresh | Giống nhau  | Giống nhau      |

➡️ **Logic cache không đổi**

---

## 10. Best practice thực tế

### ✅ Nên cache

* Read nhiều, write ít
* Query nặng
* HTML fragment
* API response

### ❌ Không nên cache

* Dữ liệu thay đổi liên tục
* Dữ liệu yêu cầu realtime tuyệt đối

---

## 11. Một câu tóm tắt cốt lõi

> **Redis chỉ là bộ nhớ key-value, Rails mới là thằng quyết định khi nào cache, khi nào refresh, thông qua TTL, xóa cache, hoặc đổi cache key khi dữ liệu thay đổi.**



==================================

🔑 Các mô hình caching trong Redis
1. Cache-Aside (Lazy Loading)
  - Cách hoạt động: Ứng dụng kiểm tra cache trước. Nếu dữ liệu không có (cache miss), nó sẽ lấy từ database, sau đó ghi vào cache cho lần dùng sau.
  - Ưu điểm:
    - Giảm tải database vì chỉ cache dữ liệu khi cần.
    - Dễ triển khai, phù hợp cho hầu hết ứng dụng.
  - Nhược điểm:
    - Lần truy vấn đầu tiên sẽ chậm (cache miss).
    - Dữ liệu trong cache có thể bị lỗi thời nếu không có cơ chế invalidation.

2. Write-Through
  - Cách hoạt động: Mỗi khi ứng dụng ghi dữ liệu vào database, nó cũng ghi ngay vào cache, chỉ thành công khi c3 2 ghi xong và thành công.
  - Ưu điểm:
    - Cache luôn đồng bộ với database.
    - Truy vấn sau đó nhanh vì dữ liệu đã có sẵn trong cache.
  - Nhược điểm:
    - Tốn chi phí ghi kép (database + cache).
    - Nếu dữ liệu ít được đọc lại, cache có thể chứa nhiều dữ liệu không cần thiết.

3. Write-Behind (Write-Back)
  - Cách hoạt động: Ứng dụng ghi dữ liệu vào cache, Redis sẽ cập nhật database sau (thường theo batch hoặc định kỳ).
  - Ưu điểm:
    - Hiệu năng ghi cao vì ứng dụng chỉ ghi vào cache.
    - Giảm tải database nhờ ghi theo lô.
  - Nhược điểm:
    - Nguy cơ mất dữ liệu nếu cache bị crash trước khi đồng bộ.
    - Độ trễ đồng bộ cao, database có thể không phản ánh ngay dữ liệu mới nhất.
   
📌 Lời khuyên
  - Cache-Aside: phù hợp cho ứng dụng đọc nhiều, ghi ít (ví dụ: website tin tức, e-commerce).
  - Write-Through: tốt cho hệ thống cần dữ liệu nhất quán cao (ví dụ: tài chính, ngân hàng).
  - Write-Behind: hữu ích khi ghi nhiều, đọc ít và chấp nhận độ trễ (ví dụ: logging, analytics).



