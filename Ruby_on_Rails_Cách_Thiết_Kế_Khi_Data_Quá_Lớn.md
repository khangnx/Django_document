# Ruby on Rails – Xử lý Data Lớn & Report Realtime

## 1️⃣ Nguyên tắc cốt lõi (rất quan trọng)

> ❌ **KHÔNG** tạo report realtime bằng:

* `JOIN` phức tạp trên hàng triệu record
* Aggregate (`SUM`, `COUNT`, `GROUP BY`) trực tiếp từ bảng lớn
* Chạy report sync trong request HTTP

> ✅ **NÊN**:

* Tách **OLTP** (ghi dữ liệu) và **OLAP** (đọc – report)
* Pre-compute / stream dữ liệu
* Cache + async processing

---

## 2️⃣ Kiến trúc khuyến nghị (Rails + Big Data)

```
User
  ↓
Rails API
  ↓
Redis (cache realtime)
  ↓
PostgreSQL (OLTP)
  ↓
Background Job
  ↓
Data Warehouse / Search Engine
  ↓
Report API
```

---

## 3️⃣ Các hướng giải quyết theo mức độ realtime

### 🔹 Level 1 – Near Realtime (1–5 giây) ✅ PHỔ BIẾN NHẤT

**Giải pháp:**

* PostgreSQL (ghi)
* Redis (cache kết quả)
* Background Job (Sidekiq)

**Flow:**

```
User action → Rails → DB
                   ↓
               Sidekiq
                   ↓
            Update Redis Report
```

**Ví dụ code:**

```ruby
# khi có order mới
after_commit :update_report_cache

def update_report_cache
  Sidekiq.perform_async(self.id)
end
```

```ruby
# report đọc từ Redis
Redis.current.get("report:daily_sales")
```

✔ Rất nhanh
✔ Ít load DB
❌ Không real-time 100% (delay vài giây)

---

### 🔹 Level 2 – Real-time streaming (milliseconds–seconds)

**Dùng khi:**

* Dashboard realtime
* Thống kê live (traffic, đơn hàng)

**Stack phổ biến:**

* Kafka / AWS Kinesis
* Redis Streams
* WebSocket / ActionCable

**Flow:**

```
Event → Stream → Consumer → Redis → Dashboard
```

> Rails chỉ đóng vai trò producer / consumer nhẹ

---

### 🔹 Level 3 – Analytics cực lớn (hàng triệu – tỷ record)

👉 **Rails KHÔNG phù hợp để query trực tiếp**

**Giải pháp:**

* PostgreSQL → ETL
* Amazon Redshift / BigQuery / ClickHouse
* Rails chỉ gọi API report

**Flow:**

```
Postgres → ETL → Data Warehouse
                       ↓
                    Report API
```

✔ Query cực nhanh
✔ Scale tốt
❌ Không realtime tuyệt đối

---

## 4️⃣ Kỹ thuật bắt buộc phải dùng

### ✅ 1. Pre-aggregation (tối quan trọng)

❌ Truy vấn trực tiếp:

```sql
SELECT COUNT(*) FROM orders WHERE created_at >= today;
```

✅ Lưu dữ liệu tổng hợp sẵn:

```ruby
daily_reports(date, total_orders, total_amount)
```

Cập nhật bằng background job.

---

### ✅ 2. Cache nhiều tầng

| Tầng      | Công nghệ         |
| --------- | ----------------- |
| App cache | Rails.cache       |
| Realtime  | Redis             |
| CDN       | CloudFront        |
| DB        | Materialized View |

---

### ✅ 3. Materialized View (PostgreSQL)

```sql
CREATE MATERIALIZED VIEW daily_sales AS
SELECT date(created_at), sum(amount)
FROM orders
GROUP BY 1;
```

Refresh:

```sql
REFRESH MATERIALIZED VIEW CONCURRENTLY daily_sales;
```

---

### ✅ 4. Không chạy report trong request

❌

```ruby
def report
  Order.group(:status).count
end
```

✅

```ruby
def report
  Rails.cache.fetch("report:v1", expires_in: 1.minute)
end
```

---

## 5️⃣ Khi nào dùng gì? (Bảng quyết định nhanh)

| Nhu cầu            | Giải pháp               |
| ------------------ | ----------------------- |
| < 1 triệu record   | Postgres + Index        |
| 1–50 triệu         | Redis + Pre-aggregation |
| Dashboard realtime | Redis + Stream          |
| Analytics lớn      | Redshift / ClickHouse   |
| Export report      | Background Job          |

---

## 6️⃣ Gợi ý stack phù hợp (Rails + AWS)

* PostgreSQL (OLTP)
* Redis (realtime cache)
* Sidekiq
* Amazon Redshift (analytics)
* CloudWatch / QuickSight (visual)

---

## 7️⃣ Một câu tóm tắt (rất quan trọng)

> **Rails không tạo report realtime bằng query trực tiếp – mà bằng cache, stream và pre-compute.**
