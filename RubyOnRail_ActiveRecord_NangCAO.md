# 📘 ActiveRecord nâng cao – Khái niệm & Tác dụng (giải thích kỹ thuật đầy đủ)

> Tài liệu này **loại bỏ hoàn toàn ví von**, trình bày **định nghĩa kỹ thuật + tác dụng thực tế** cho từng khái niệm ActiveRecord nâng cao, theo cùng mức chi tiết như phần **Arel**.

---

## 1️⃣ ActiveRecord (ORM)

### Khái niệm

**ActiveRecord** là ORM trong Rails, ánh xạ bảng DB ↔ class Ruby và cung cấp API thao tác dữ liệu.

---

### Ví dụ cụ thể

**Bảng DB**:

```sql
users(id, email, active)
```

**Model**:

```ruby
class User < ApplicationRecord
end
```

**Sử dụng**:

```ruby
user = User.create!(email: 'a@test.com', active: true)
User.find(user.id)
User.where(active: true)
```

➡ Rails tự sinh SQL tương ứng.

---

### Tác dụng

* Cho phép lập trình viên thao tác DB bằng Ruby thay vì SQL thuần
* Chuẩn hóa cách truy cập dữ liệu trong toàn bộ ứng dụng
* Giảm lỗi do SQL injection
* Tăng khả năng maintain và test

---

## 2️⃣ ActiveRecord::Relation

### Khái niệm

ActiveRecord::Relation là **đối tượng mô tả truy vấn**, chưa thực thi SQL.

---

### Ví dụ cụ thể

```ruby
relation = User.where(active: true)
# Chưa query DB

relation = relation.where("email LIKE '%@gmail.com'")
# Vẫn chưa query

relation.to_a
# SQL chạy tại đây
```

➡ Mọi điều kiện được gộp thành **1 câu SQL duy nhất**.

---

### Tác dụng

* Cho phép **chain nhiều điều kiện** trước khi thực thi
* Kết hợp nhiều scope/query thành một câu SQL duy nhất
* Giảm số lượng query gửi đến DB

---

## 3️⃣ Scope

### Khái niệm

Scope là ActiveRecord::Relation được đặt tên.

---

### Ví dụ cụ thể

```ruby
class User < ApplicationRecord
  scope :active, -> { where(active: true) }
  scope :gmail,  -> { where("email LIKE '%@gmail.com'") }
end
```

**Sử dụng**:

```ruby
User.active.gmail
```

➡ Dễ đọc, dễ tái sử dụng.

---

### Tác dụng

* Tái sử dụng logic truy vấn
* Chuẩn hóa điều kiện lọc dữ liệu
* Tăng tính biểu đạt của code (semantic query)

---

## 4️⃣ Merge Scope

### Khái niệm

`merge` kết hợp scope của model khác khi join.

---

### Ví dụ cụ thể

```ruby
class Order < ApplicationRecord
  scope :paid, -> { where(status: 'paid') }
end
```

```ruby
User.joins(:orders).merge(Order.paid)
```

➡ Logic `paid` vẫn nằm trong Order.

---

### Tác dụng

* Giữ logic truy vấn ở đúng model
* Tránh hard-code điều kiện bảng khác
* Duy trì nguyên tắc Single Responsibility

---

## 5️⃣ Query Object Pattern

### Khái niệm

Query Object là class chuyên build query.

---

### Ví dụ cụ thể

```ruby
class OrdersQuery
  def initialize(scope = Order.all)
    @scope = scope
  end

  def paid
    @scope.where(status: 'paid')
  end

  def recent
    @scope.where('created_at > ?', 7.days.ago)
  end
end
```

**Sử dụng**:

```ruby
OrdersQuery.new.paid.recent
```

---

### Tác dụng

* Tránh model bị phình to
* Dễ test từng nhánh query
* Dễ mở rộng khi query ngày càng phức tạp
* Phù hợp cho admin filter, report, search

---

## 6️⃣ Association (Nâng cao)

### 6.1 has_many :through

```ruby
class Membership < ApplicationRecord
  belongs_to :user
  belongs_to :project
end

class User < ApplicationRecord
  has_many :memberships
  has_many :projects, through: :memberships
end
```

➡ Lưu thêm role trong membership.

---

### 6.2 Polymorphic Association

```ruby
class Comment < ApplicationRecord
  belongs_to :commentable, polymorphic: true
end

class Post < ApplicationRecord
  has_many :comments, as: :commentable
end
```

➡ Comment dùng chung cho nhiều model.

---

### 6.3 STI

```ruby
class Payment < ApplicationRecord; end
class CreditCardPayment < Payment; end
class PaypalPayment < Payment; end
```

➡ Phân biệt bằng cột `type`.

---

#### Tác dụng

* Lưu metadata của mối quan hệ
* Query linh hoạt hơn so với HABTM
* Mở rộng nghiệp vụ lâu dài

---

### 6.2 Polymorphic Association

#### Khái niệm

Là quan hệ mà một model có thể thuộc về **nhiều model khác nhau**, thông qua cặp cột:

* `*_type`
* `*_id`

---

#### Tác dụng

* Giảm số bảng
* Linh hoạt khi nhiều entity có cùng hành vi

---

### 6.3 Single Table Inheritance (STI)

#### Khái niệm

Nhiều class Ruby dùng chung **một bảng DB**, phân biệt bằng cột `type`.

---

#### Tác dụng

* Dùng chung schema và logic
* Dễ mở rộng hành vi theo subclass

---

## 7️⃣ Counter Cache

### Ví dụ cụ thể

```ruby
class Comment < ApplicationRecord
  belongs_to :post, counter_cache: true
end
```

**posts table**:

```sql
comments_count integer
```

```ruby
post.comments_count
```

---

### Tác dụng

* Tránh COUNT(*) runtime
* Tăng hiệu năng cho dữ liệu lớn
* Phù hợp cho dashboard, list view

---

## 8️⃣ Touch

### Ví dụ cụ thể

```ruby
class Order < ApplicationRecord
  belongs_to :user, touch: true
end
```

➡ Khi order update → `user.updated_at` thay đổi.

---

### Tác dụng

* Invalidate cache đúng thời điểm
* Theo dõi thay đổi lan truyền

---

## 9️⃣ Callback

### Ví dụ cụ thể

```ruby
class User < ApplicationRecord
  before_save :normalize_email

  private

  def normalize_email
    self.email = email.downcase
  end
end
```

➡ Chuẩn hóa dữ liệu trước khi lưu.

---

### Tác dụng

* Chuẩn hóa dữ liệu
* Gắn logic kỹ thuật nhẹ

⚠️ Không dùng callback cho nghiệp vụ phức tạp

---

## 🔟 Transaction

### Ví dụ cụ thể

```ruby
ActiveRecord::Base.transaction do
  order.save!
  payment.save!
end
```

➡ Một lỗi → rollback toàn bộ.

---

### Tác dụng

* Đảm bảo toàn vẹn dữ liệu
* Tránh trạng thái trung gian không hợp lệ

---

## 1️⃣1️⃣ Locking

### Ví dụ cụ thể

**Optimistic Lock**:

```ruby
add_column :orders, :lock_version, :integer
```

```ruby
order.update!(status: 'paid')
```

**Pessimistic Lock**:

```ruby
order.lock!
```

---

### Tác dụng

* Tránh race condition
* Bảo vệ dữ liệu nhạy cảm (tiền, số lượng)

---

## 1️⃣2️⃣ Eager Loading

### Ví dụ cụ thể

```ruby
users = User.includes(:orders)
users.each { |u| u.orders.size }
```

➡ Tránh N+1 query.

---

### Tác dụng

* Giảm số lượng query
* Cải thiện performance đáng kể

---

## 1️⃣3️⃣ select / pluck / pick

### Ví dụ cụ thể

```ruby
User.select(:id, :email)
User.pluck(:email)
User.pick(:email)
```

---

### Tác dụng

* Giảm memory
* Tăng tốc độ query

---

## 1️⃣4️⃣ Dirty Tracking

### Ví dụ cụ thể

```ruby
user.email = 'new@test.com'
user.email_changed?
user.saved_change_to_email?
```

---

### Tác dụng

* Audit log
* Trigger logic theo thay đổi dữ liệu

---

## 1️⃣5️⃣ Read Model / Database View

### Ví dụ cụ thể

```ruby
class OrderSummary < ApplicationRecord
  self.primary_key = nil
end
```

➡ Model chỉ đọc cho report/search.

---
