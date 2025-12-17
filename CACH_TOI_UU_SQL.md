# 🚀 CÁCH TỐI ƯU CÂU SQL (SQL Query Optimization)

Tài liệu này tổng hợp các **kỹ thuật tối ưu câu SQL** theo hướng **thực tế – dễ áp dụng**, đặc biệt phù hợp với **Ruby on Rails + PostgreSQL/MySQL**.

---

## 1️⃣ Chỉ `SELECT` những cột cần thiết

❌ **Không nên**

```sql
SELECT * FROM users;
```

✅ **Nên**

```sql
SELECT id, name, email FROM users;
```

🔎 **Lý do**:

* Giảm dung lượng dữ liệu đọc từ disk
* Giảm memory và network
* Dễ tận dụng **Index-only scan**

👉 **Rails**

```ruby
User.select(:id, :name, :email)
```

---

## 2️⃣ Dùng INDEX đúng cách (QUAN TRỌNG NHẤT)

### 📌 Tạo index cho các cột dùng trong:

* `WHERE`
* `JOIN`
* `ORDER BY`
* `GROUP BY`

```sql
CREATE INDEX idx_users_email ON users(email);
```

👉 **Rails migration**

```ruby
add_index :users, :email
```

---

### 🔥 Composite Index (index nhiều cột)

```sql
CREATE INDEX idx_orders_user_status
ON orders(user_id, status);
```

❗ **Thứ tự cột trong index rất quan trọng**

```sql
WHERE user_id = 1 AND status = 'paid'   -- ✅ Tối ưu
WHERE status = 'paid'                   -- ❌ Kém hiệu quả
```

---

## 3️⃣ Tránh dùng hàm trong `WHERE` (làm mất index)

❌ **Không tối ưu**

```sql
SELECT * FROM users
WHERE DATE(created_at) = '2025-12-01';
```

✅ **Tối ưu**

```sql
SELECT * FROM users
WHERE created_at >= '2025-12-01'
  AND created_at <  '2025-12-02';
```

👉 **Rails**

```ruby
where(created_at: Date.parse("2025-12-01").all_day)
```

---

## 4️⃣ Tránh `LIKE '%keyword%'` nếu có thể

❌

```sql
WHERE name LIKE '%john%';
```

👉 Không sử dụng được index

✅ **Giải pháp**

* `LIKE 'john%'`
* Full-text search (PostgreSQL `tsvector`, Elasticsearch)

```sql
WHERE name LIKE 'john%';
```

---

## 5️⃣ Dùng `LIMIT` càng sớm càng tốt

❌

```sql
SELECT * FROM orders
ORDER BY created_at DESC;
```

✅

```sql
SELECT * FROM orders
ORDER BY created_at DESC
LIMIT 20;
```

👉 **Rails**

```ruby
Order.order(created_at: :desc).limit(20)
```

---

## 6️⃣ Tối ưu `JOIN`

### ❌ JOIN dư thừa

```sql
SELECT users.*
FROM users
JOIN profiles ON profiles.user_id = users.id;
```

👉 Nếu không dùng dữ liệu từ `profiles` → **bỏ JOIN**

---

### ✅ JOIN + index

```sql
CREATE INDEX idx_profiles_user_id ON profiles(user_id);
```

---

## 7️⃣ Dùng `EXISTS` thay vì `IN` (với subquery lớn)

❌

```sql
SELECT *
FROM users
WHERE id IN (SELECT user_id FROM orders);
```

✅

```sql
SELECT *
FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```

📌 `EXISTS` dừng ngay khi tìm thấy record đầu tiên

---

## 8️⃣ Tránh N+1 Query (Rails)

❌

```ruby
users.each do |user|
  user.posts.count
end
```

✅

```ruby
User.includes(:posts)
```

Hoặc:

```ruby
User.joins(:posts).group("users.id")
```

---

## 9️⃣ Dùng `EXPLAIN / EXPLAIN ANALYZE`

```sql
EXPLAIN ANALYZE
SELECT * FROM users WHERE email = 'a@gmail.com';
```

### 🔎 Cần chú ý:

| Dấu hiệu   | Ý nghĩa                     |
| ---------- | --------------------------- |
| Seq Scan   | Full table scan (nguy hiểm) |
| Index Scan | Tốt                         |
| Cost cao   | Query chậm                  |
| Rows lớn   | Lọc chưa hiệu quả           |

---

## 🔟 Dùng Cache thay vì query DB nhiều lần

👉 **Rails + Redis**

```ruby
Rails.cache.fetch("users/#{id}", expires_in: 5.minutes) do
  User.find(id)
end
```

---

## 1️⃣1️⃣ Batch query thay vì query nhiều lần

❌

```ruby
ids.each { |id| User.find(id) }
```

✅

```ruby
User.where(id: ids)
```

---

## 1️⃣2️⃣ Khi dữ liệu RẤT LỚN

| Trường hợp      | Giải pháp             |
| --------------- | --------------------- |
| Search          | Elasticsearch         |
| Report realtime | Redis Streams / Kafka |
| Analytics       | Redshift              |
| Log             | ClickHouse            |

---

## 📌 Checklist nhanh

* ✅ Có index chưa?
* ✅ Có `SELECT *` không?
* ✅ Có `EXPLAIN` chưa?
* ✅ Có dùng hàm trong `WHERE` không?
* ✅ Có N+1 query không?
* ✅ Có cache được không?

---

📎 *Bạn có thể copy toàn bộ file này để làm tài liệu nội bộ hoặc checklist tối ưu SQL cho dự án Rails.*
