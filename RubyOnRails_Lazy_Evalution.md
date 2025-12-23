# 📘 Ruby – Lazy Enumeration (Lazy Enumerator)

## 1. Lazy Enumeration là gì?

**Lazy Enumeration** là tính năng trong Ruby (được giới thiệu từ **Ruby 2.0**) cho phép duyệt dữ liệu theo kiểu **“lười”**  
→ chỉ **tính toán phần tử khi thực sự cần**, thay vì xử lý toàn bộ collection ngay lập tức.

Tính năng này đặc biệt hữu ích khi:
- Làm việc với **tập dữ liệu lớn**
- Chuỗi xử lý dài (`map`, `select`, `reject`, …)
- Xử lý **dòng dữ liệu vô hạn (infinite sequence)**

---

## 2. Vấn đề của Enumerator thông thường (Eager Evaluation)

Ví dụ **không dùng lazy**:

```ruby
(1..1_000_000)
  .map { |x| x * 2 }
  .select { |x| x.even? }
  .first(5)
```

### ❌ Nhược điểm
- `map` xử lý **1 triệu phần tử**
- `select` xử lý **1 triệu phần tử**
- Trong khi chỉ cần **5 phần tử đầu tiên**

⛔ Tốn CPU và bộ nhớ không cần thiết.

---

## 3. Giải pháp: Lazy Enumerator

Dùng `.lazy` để chuyển Enumerator sang **lazy mode**:

```ruby
(1..1_000_000)
  .lazy
  .map { |x| x * 2 }
  .select { |x| x.even? }
  .first(5)
```

### ✅ Lợi ích
- Ruby chỉ xử lý **từng phần tử một**
- Dừng ngay khi đủ **5 kết quả**
- Không duyệt toàn bộ **1 triệu phần tử**

👉 Hiệu năng và bộ nhớ được cải thiện rõ rệt.

---

## 4. Lazy Enumerator hoạt động như thế nào?

### So sánh Eager vs Lazy

| Tiêu chí | Eager (mặc định) | Lazy |
|--------|------------------|------|
| Cách xử lý | Xử lý toàn bộ | Xử lý từng phần tử |
| Bộ nhớ | Tốn nhiều | Tiết kiệm |
| Infinite sequence | ❌ Không dùng được | ✅ Dùng được |
| Dữ liệu lớn | ❌ Kém hiệu quả | ✅ Rất hiệu quả |

---

## 5. Ví dụ với Infinite Sequence

```ruby
numbers = (1..Float::INFINITY).lazy

numbers
  .select { |x| x % 3 == 0 }
  .map { |x| x * 10 }
  .first(5)
```

**Kết quả:**
```ruby
[30, 60, 90, 120, 150]
```

👉 Nếu không dùng `.lazy` → chương trình sẽ **chạy vô hạn và bị treo**.

---

## 6. Các method phổ biến hỗ trợ Lazy

Lazy Enumerator hỗ trợ hầu hết các method của `Enumerable`:
- `map`
- `select`
- `reject`
- `take`
- `drop`
- `flat_map`
- `with_index`

Ví dụ:

```ruby
(1..100).lazy
  .drop(10)
  .take(5)
  .to_a
```

---

## 7. Khi nào nên dùng Lazy Enumeration?

### ✅ Nên dùng khi:
- Dữ liệu lớn
- Chuỗi xử lý nhiều bước
- Chỉ cần **một phần nhỏ kết quả**
- Xử lý stream, log, file lớn
- Infinite sequence

### ❌ Không cần dùng khi:
- Dữ liệu nhỏ
- Cần toàn bộ kết quả
- Muốn code đơn giản, dễ đọc

---

## 8. Lazy Enumeration trong Ruby on Rails

Ví dụ thực tế trong Rails:

```ruby
User
  .where(active: true)
  .find_each
  .lazy
  .select { |u| u.orders.count > 10 }
  .take(5)
```

👉 Thường dùng kết hợp với:
- `find_each`
- Batch processing
- Background jobs

---

## 9. Tóm tắt

- Lazy Enumeration được giới thiệu từ **Ruby 2.0**
- Kích hoạt bằng `.lazy`
- Giúp tối ưu **hiệu năng và bộ nhớ**
- Rất mạnh khi làm việc với **dữ liệu lớn hoặc vô hạn**
