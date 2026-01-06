# Polymorphic Associations & Single Table Inheritance (STI) trong Ruby on Rails

Tài liệu này giải thích **Polymorphic Associations** và **Single Table Inheritance (STI)** trong Ruby on Rails một cách **chi tiết – có ví dụ – có so sánh – có lưu ý thực tế**.

---

## 1. Polymorphic Associations là gì?

### 🔹 Khái niệm
**Polymorphic Association** cho phép **một model liên kết với nhiều model khác nhau** thông qua **cùng một quan hệ**.

➡️ Thay vì tạo nhiều foreign key khác nhau, Rails dùng:
- `*_id`
- `*_type`

---

### 🔹 Ví dụ thực tế

Giả sử:
- `Post` có thể được comment
- `Photo` cũng có thể được comment

➡️ Ta muốn `Comment` **thuộc về Post hoặc Photo**.

---

## 2. Ví dụ Polymorphic Associations

### 2.1. Migration

```ruby
create_table :comments do |t|
  t.text :content
  t.references :commentable, polymorphic: true, index: true
  t.timestamps
end
```

Rails sẽ tạo:
- `commentable_id`
- `commentable_type`

---

### 2.2. Model

#### Comment

```ruby
class Comment < ApplicationRecord
  belongs_to :commentable, polymorphic: true
end
```

#### Post

```ruby
class Post < ApplicationRecord
  has_many :comments, as: :commentable
end
```

#### Photo

```ruby
class Photo < ApplicationRecord
  has_many :comments, as: :commentable
end
```

---

### 2.3. Sử dụng

```ruby
post = Post.first
post.comments.create(content: "Nice post")

photo = Photo.first
photo.comments.create(content: "Beautiful photo")
```

Dữ liệu trong bảng `comments`:

| id | content         | commentable_id | commentable_type |
|----|-----------------|----------------|------------------|
| 1  | Nice post       | 1              | Post             |
| 2  | Beautiful photo | 3              | Photo            |

---

### 2.4. Ưu điểm & Nhược điểm

#### ✅ Ưu điểm
- Linh hoạt
- Giảm số lượng bảng
- Phù hợp với các đối tượng dùng chung hành vi (comment, like, rating)

#### ❌ Nhược điểm
- Không có foreign key constraint ở DB
- Query phức tạp hơn
- Khó optimize khi dữ liệu lớn

---

## 3. Single Table Inheritance (STI) là gì?

### 🔹 Khái niệm
**STI** cho phép **nhiều class Ruby dùng chung một bảng database**.

Rails phân biệt các class bằng cột đặc biệt: `type`

---

### 🔹 Ví dụ thực tế

Giả sử hệ thống thanh toán có:
- CreditCard
- Paypal
- BankTransfer

➡️ Tất cả đều là một loại `Payment`

---

## 4. Ví dụ Single Table Inheritance

### 4.1. Migration

```ruby
create_table :payments do |t|
  t.string :type
  t.decimal :amount
  t.string :status
  t.timestamps
end
```

⚠️ `type` là tên mặc định của Rails (không nên đổi nếu dùng STI)

---

### 4.2. Model

#### Payment (base class)

```ruby
class Payment < ApplicationRecord
end
```

#### CreditCard

```ruby
class CreditCard < Payment
end
```

#### Paypal

```ruby
class Paypal < Payment
end
```

---

### 4.3. Sử dụng

```ruby
CreditCard.create(amount: 100)
Paypal.create(amount: 50)
```

Dữ liệu trong bảng `payments`:

| id | type       | amount |
|----|------------|--------|
| 1  | CreditCard | 100    |
| 2  | Paypal     | 50     |

---

### 4.4. Query

```ruby
Payment.all        # lấy tất cả
CreditCard.all     # chỉ CreditCard
Payment.where(type: 'Paypal')
```

---

### 4.5. Ưu điểm & Nhược điểm

#### ✅ Ưu điểm
- Đơn giản
- Ít bảng
- Dễ dùng với ActiveRecord

#### ❌ Nhược điểm
- Bảng có nhiều cột không dùng hết
- Khó thay đổi schema khi class khác biệt nhiều
- Không phù hợp khi logic khác nhau quá lớn

---

## 5. So sánh Polymorphic vs STI

| Tiêu chí | Polymorphic | STI |
|--------|-------------|-----|
| Số bảng | Nhiều | 1 |
| Mục đích | Quan hệ linh hoạt | Kế thừa model |
| Dữ liệu | Tách biệt | Chung bảng |
| DB constraint | Không mạnh | Có |
| Dùng khi | Chia sẻ quan hệ | Chia sẻ hành vi |

---

## 6. Khi nào nên dùng cái nào?

### ✅ Dùng Polymorphic khi:
- Comment, Like, Attachment
- Nhiều model dùng chung một hành vi

### ✅ Dùng STI khi:
- Các object có cùng bản chất
- Logic gần giống nhau
- Schema không khác biệt nhiều

---

## 7. Lưu ý thực tế (Best Practices)

- ❌ Tránh lạm dụng Polymorphic trong hệ thống lớn
- ❌ Tránh STI nếu mỗi subclass có quá nhiều field riêng
- ✅ Có thể kết hợp STI + Polymorphic

---

## 8. Ví dụ kết hợp STI + Polymorphic

```ruby
class Activity < ApplicationRecord
  belongs_to :trackable, polymorphic: true
end

class Post < ApplicationRecord; end
class Comment < ApplicationRecord; end

class UserActivity < Activity; end
class SystemActivity < Activity; end
```

---

## 9. Tổng kết

- **Polymorphic** → chia sẻ *quan hệ*
- **STI** → chia sẻ *kế thừa & hành vi*
- Chọn đúng giúp code sạch, dễ mở rộng, dễ maintain
