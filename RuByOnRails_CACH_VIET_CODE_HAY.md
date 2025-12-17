# 📘 Giải thích chi tiết `delegate` và `present? / blank? / nil?` trong Ruby on Rails

Tài liệu này đi sâu vào **2 phần rất hay gặp nhưng dễ gây nhầm lẫn** trong Rails:

1. `delegate` – Ủy quyền method
2. `present?`, `blank?`, `nil?` – Kiểm tra trạng thái dữ liệu

Viết theo hướng **thực tế – dễ nhớ – đúng tư duy Rails** để bạn có thể copy học hoặc dùng làm tài liệu nội bộ.

---

## 1️⃣ `delegate` trong Ruby on Rails

### 1. `delegate` là gì?

`delegate` cho phép **class hiện tại gọi method của object khác như thể method đó thuộc về chính nó**.

Nói đơn giản:

> "Thay vì viết `object_a.object_b.method`, ta viết `object_a.method`"

---

### 2. Ví dụ cơ bản (chưa dùng delegate)

```ruby
class Order < ApplicationRecord
  belongs_to :user
end

order.user.name
order.user.email
```

⛔ Nhược điểm:

* Viết dài
* Lặp lại `.user`
* Khó đọc khi chain nhiều tầng

---

### 3. Dùng `delegate`

```ruby
class Order < ApplicationRecord
  belongs_to :user

  delegate :name, :email, to: :user
end
```

Giờ có thể viết:

```ruby
order.name
order.email
```

👉 Rails tự hiểu:

```ruby
order.user.name
```

---

### 4. `delegate` hoạt động như thế nào?

Rails ngầm tạo method:

```ruby
def name
  user.name
end
```

👉 Nhưng **viết gọn và sạch hơn rất nhiều**.

---

### 5. `delegate` với `allow_nil`

Nếu `user` có thể là `nil`:

```ruby
class Order < ApplicationRecord
  belongs_to :user, optional: true

  delegate :name, to: :user, allow_nil: true
end
```

Kết quả:

```ruby
order.name # => nil (không crash)
```

Nếu KHÔNG có `allow_nil`:

```ruby
order.name # ❌ NoMethodError
```

---

### 6. Delegate nhiều method

```ruby
delegate :name, :email, :phone, to: :user
```

---

### 7. Delegate sang object khác (không chỉ association)

```ruby
class Invoice
  def total
    1_000_000
  end
end

class Order
  def invoice
    Invoice.new
  end

  delegate :total, to: :invoice
end

Order.new.total # => 1000000
```

---

### 8. Khi nào NÊN dùng delegate?

✅ Nên dùng khi:

* Model đóng vai trò "wrapper"
* Giảm chain dài (`a.b.c.d`)
* Code đọc dễ hơn

❌ Không nên dùng khi:

* Logic phức tạp
* Delegate quá nhiều method → khó hiểu source

---

### 9. So sánh `delegate` vs `method thủ công`

| Cách          | Ưu điểm          | Nhược điểm |
| ------------- | ---------------- | ---------- |
| delegate      | Gọn, chuẩn Rails | Ẩn logic   |
| method thường | Rõ ràng          | Dài dòng   |

---

## 2️⃣ `present?`, `blank?`, `nil?` trong Rails

### 1. `nil?` – kiểm tra đúng `nil`

```ruby
nil.nil?        # true
"".nil?         # false
[].nil?         # false
```

👉 Chỉ đúng khi object = `nil`.

---

### 2. `empty?` – object rỗng

```ruby
"".empty?      # true
[].empty?       # true
{}.empty?       # true
```

⛔ Nhưng:

```ruby
nil.empty? # ❌ NoMethodError
```

---

### 3. `blank?` – Rails mở rộng (RẤT HAY)

`blank?` trả về `true` nếu object:

* `nil`
* rỗng
* chỉ chứa khoảng trắng

```ruby
nil.blank?        # true
"".blank?         # true
"   ".blank?      # true
[].blank?         # true
{}.blank?         # true
false.blank?      # true
```

👉 Cực kỳ tiện trong validate & view.

---

### 4. `present?` – ngược lại của `blank?`

```ruby
"hello".present?   # true
[1,2].present?     # true
nil.present?       # false
"".present?        # false
```

---

### 5. So sánh tổng hợp

| Giá trị | nil?  | empty? | blank? | present? |
| ------- | ----- | ------ | ------ | -------- |
| nil     | true  | ❌      | true   | false    |
| ""      | false | true   | true   | false    |
| "  "    | false | false  | true   | false    |
| []      | false | true   | true   | false    |
| [1]     | false | false  | false  | true     |
| false   | false | ❌      | true   | false    |

---

### 6. Ví dụ thực tế trong Rails

#### a. Validate params

```ruby
return if params[:email].blank?
```

#### b. View condition

```erb
<% if @user.avatar.present? %>
  <img src="<%= @user.avatar %>" />
<% end %>
```

#### c. Controller logic

```ruby
if current_user.blank?
  redirect_to login_path
end
```

---

## 🧠 Ghi nhớ nhanh

> `nil?` → chỉ nil
> `empty?` → rỗng
> `blank?` → nil + rỗng + khoảng trắng
> `present?` → có giá trị hợp lệ

---

## ✅ Kết luận

* `delegate` giúp **code gọn – đúng style Rails – dễ đọc**
* `blank?/present?` là **vũ khí mạnh nhất của Rails khi xử lý dữ liệu**

📌 Rails developer tốt **phải dùng 2 nhóm này thật nhuần nhuyễn**.

---

✋ Nếu bạn muốn:

* Mở rộng thêm `delegate` vs Decorator
* So sánh `blank?` với JavaScript / PHP
* Liên hệ với RSpec test case

👉 Cứ nói, mình viết tiếp cho bạn.

# 📘 Tổng hợp CÁC CÚ PHÁP & CÁCH VIẾT ĐẶC BIỆT trong Ruby on Rails

> Tài liệu này tổng hợp **toàn bộ kiến thức đã trao đổi** về các cú pháp, quy ước và cách viết đặc trưng của Ruby & Ruby on Rails.
>
> Mục tiêu: **dễ đọc – dễ nhớ – áp dụng ngay trong dự án Rails thực tế**.

---

## 1️⃣ Dấu `?` và `!` trong tên method

### `?` – Predicate method (trả về boolean)

```ruby
user.active?
order.paid?
file.exist?
```

* Luôn trả về `true` hoặc `false`
* Dùng trong điều kiện `if`, `unless`

❌ Không nên trả về object hoặc string.

---

### `!` – Method nguy hiểm / mạnh hơn

```ruby
user.save!
user.update!
user.destroy!
```

So sánh:

```ruby
user.save   # false nếu lỗi
user.save!  # raise exception
```

👉 Dùng trong service, job, logic quan trọng (fail fast).

---

## 2️⃣ `self` trong Ruby

### a. `self.method` – class method

```ruby
class User
  def self.active
    where(active: true)
  end
end

User.active
```

---

### b. `self` để phân biệt setter

```ruby
def name=(value)
  self.name = value
end
```

Nếu không có `self` → Ruby hiểu là local variable.

---

## 3️⃣ `*args`, `**kwargs`, `&block`

### `*args` – nhiều tham số

```ruby
def sum(*numbers)
  numbers.sum
end
```

---

### `**kwargs` – keyword arguments

```ruby
def create_user(name:, age:)
end

create_user(age: 20, name: "An")
```

---

### `&block` – nhận block

```ruby
def around
  puts "Before"
  yield
  puts "After"
end
```

```ruby
around { puts "Hello" }
```

---

## 4️⃣ `{}` vs `do...end`

* `{}` → ngắn, 1 dòng, ưu tiên giá trị trả về
* `do...end` → nhiều dòng, ưu tiên logic

```ruby
users.map { |u| u.name }
```

```ruby
users.each do |u|
  send_email(u)
  log(u)
end
```

---

## 5️⃣ `&:` – Symbol to Proc

```ruby
users.map(&:name)
```

Tương đương:

```ruby
users.map { |u| u.name }
```

⚠ Chỉ dùng khi method đơn giản.

---

## 6️⃣ `||=` – Memoization

```ruby
@user ||= User.find(id)
```

* Nếu `@user` đã có → dùng lại
* Nếu `nil` → query DB

---

## 7️⃣ Safe navigation `&.`

```ruby
user&.profile&.avatar_url
```

* Không crash nếu object = nil
* Rất hay dùng trong view & API

---

## 8️⃣ Hash shorthand (Ruby ≥ 2.7)

```ruby
name = "An"
age = 20

{ name:, age: }
```

Tương đương:

```ruby
{ name: name, age: age }
```

---

## 9️⃣ `tap`

```ruby
user.tap do |u|
  u.name = "An"
  u.save!
end
```

* Luôn trả về object ban đầu
* Dùng để chain hoặc debug

---

## 🔟 `scope` trong Rails

```ruby
scope :active, -> { where(active: true) }
```

```ruby
User.active.order(:created_at)
```

---

## 1️⃣1️⃣ Callback (`before_action`, `after_commit`)

```ruby
before_action :authenticate_user!

after_commit :send_email, on: :create
```

⚠ Không nên lạm dụng → khó debug.

---

## 1️⃣2️⃣ `render` vs `redirect_to`

| render            | redirect_to |
| ----------------- | ----------- |
| Không đổi URL     | Đổi URL     |
| Không request mới | Request mới |

---

## 1️⃣3️⃣ `nil?`, `empty?`, `blank?`, `present?`

### `nil?`

```ruby
nil.nil?      # true
"".nil?       # false
```

---

### `empty?`

```ruby
"".empty?    # true
[].empty?     # true
```

⚠ `nil.empty?` → lỗi.

---

### `blank?` (Rails)

```ruby
nil.blank?     # true
"".blank?      # true
"   ".blank?   # true
[].blank?      # true
false.blank?   # true
```

---

### `present?`

```ruby
"hello".present?  # true
nil.present?      # false
```

---

### Bảng so sánh

| Giá trị | nil?  | empty? | blank? | present? |
| ------- | ----- | ------ | ------ | -------- |
| nil     | true  | ❌      | true   | false    |
| ""      | false | true   | true   | false    |
| "  "    | false | false  | true   | false    |
| []      | false | true   | true   | false    |
| [1]     | false | false  | false  | true     |
| false   | false | ❌      | true   | false    |

---

## 1️⃣4️⃣ `try`

```ruby
user.try(:name)
```

Code mới nên dùng:

```ruby
user&.name
```

---

## 1️⃣5️⃣ `delegate`

```ruby
class Order < ApplicationRecord
  belongs_to :user

  delegate :name, :email, to: :user, allow_nil: true
end
```

```ruby
order.name   # thay cho order.user.name
```

👉 Rails tự sinh method proxy.

---

## 1️⃣6️⃣ Service Object – `.call`

```ruby
class CreateUser
  def self.call(params)
    new(params).call
  end

  def initialize(params)
    @params = params
  end

  def call
    User.create!(@params)
  end
end
```

```ruby
CreateUser.call(params)
```

---

## 1️⃣7️⃣ Concern

```ruby
module Trackable
  extend ActiveSupport::Concern

  included do
    before_create :track
  end
end
```

* Chia sẻ logic
* Tránh fat model

---

## 1️⃣8️⃣ `enum`

```ruby
enum status: { pending: 0, paid: 1, shipped: 2 }
```

Rails sinh:

```ruby
order.paid?
order.shipped!
```

---

## 🧠 Tổng kết cốt lõi

> Ruby on Rails ưu tiên **quy ước + cú pháp ngắn gọn**
> → Code đọc như ngôn ngữ tự nhiên
> → Ít code nhưng nhiều ý nghĩa

---

📌 Tài liệu phù hợp cho:

* Người mới học Rails
* Junior → Mid Rails Developer
* Note nội bộ / onboarding team

---

✋ Bạn có thể copy toàn bộ file Markdown này để lưu học hoặc chia sẻ.


