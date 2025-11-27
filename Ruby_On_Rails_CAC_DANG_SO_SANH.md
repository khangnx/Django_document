# Tổng hợp chi tiết các dạng so sánh trong Ruby & Ruby on Rails

## 1. Toán tử so sánh cơ bản trong Ruby

Ruby hỗ trợ các toán tử so sánh truyền thống:

| Toán tử | Ý nghĩa           |
| ------- | ----------------- |
| `==`    | So sánh giá trị   |
| `!=`    | Khác              |
| `>`     | Lớn hơn           |
| `<`     | Nhỏ hơn           |
| `>=`    | Lớn hơn hoặc bằng |
| `<=`    | Nhỏ hơn hoặc bằng |

Ví dụ:

```ruby
5 == 5   # true
10 > 3   # true
"a" < "b"  # true
```

---

## 2. Toán tử so sánh đặc biệt: `<=>` (Spaceship Operator)

Dùng để sort hoặc so sánh toàn phần.

### Kết quả:

* `-1` : nhỏ hơn
* `0` : bằng
* `1` : lớn hơn
* `nil` : không so sánh được

Ví dụ:

```ruby
3 <=> 5   # -1
5 <=> 5   # 0
7 <=> 5   # 1
```

Ứng dụng trong sort:

```ruby
users.sort { |a, b| a.age <=> b.age }
```

---

## 3. Toán tử pattern match `===`

Được dùng nhiều trong:

* `case ... when`
* Regex
* Range
* Class checking

Ví dụ:

```ruby
Integer === 10      # true
(/abc/) === "zabc"  # true
(1..10) === 7       # true
```

Ví dụ trong routing Rails:

```ruby
constraints(id: /[0-9]+/) do
  resources :users
end
```

---

## 4. So sánh trong ActiveRecord (Rails ORM)

Rails mở rộng cú pháp so sánh khi query.

### 4.1 So sánh bằng Hash

```ruby
User.where(age: 18)
User.where(role: "admin")
```

### 4.2 So sánh với SQL operator

```ruby
User.where("age > ?", 18)
User.where("salary BETWEEN ? AND ?", 1000, 5000)
User.where("name LIKE ?", "%john%")
```

### 4.3 So sánh Range

```ruby
User.where(age: 18..30)
```

### 4.4 So sánh nhiều điều kiện

```ruby
User.where("age > ? AND status = ?", 18, "active")
```

---

## 5. So sánh thời gian trong Rails

Rails cung cấp helper thời gian giúp so sánh dễ hơn:

```ruby
User.where("created_at < ?", 1.day.ago)
User.where("updated_at >= ?", Time.current.beginning_of_day)
User.where(created_at: 3.days.ago..Time.current)
```

---

## 6. So sánh thông qua predicate method (`present?`, `blank?`, `any?`, `none?`)

Các method này là phần mở rộng của Rails.

```ruby
params[:email].present?   # true nếu không nil và không rỗng
user.name.blank?          # true nếu nil hoặc ""
User.any?                 # có record hay không
User.none?                # không có record
```

---

## 7. So sánh trong validation Rails

`validates :field, numericality: { ... }` hỗ trợ nhiều kiểu so sánh.

```ruby
validates :age, numericality: {
  greater_than: 18,
  less_than_or_equal_to: 65
}
```

### Các loại so sánh hỗ trợ:

* `greater_than`
* `greater_than_or_equal_to`
* `less_than`
* `less_than_or_equal_to`
* `other_than`
* `equal_to`

---

## 8. So sánh object trong Rails

Rails override `==` để so sánh theo primary key.

```ruby
u1 = User.find(1)
u2 = User.find(1)

u1 == u2        # true
u1.equal?(u2)   # false (khác địa chỉ object)
```

| Method   | Ý nghĩa                                         |
| -------- | ----------------------------------------------- |
| `==`     | So sánh giá trị hoặc id (ActiveRecord override) |
| `eql?`   | So sánh type + value                            |
| `equal?` | So sánh reference (object id)                   |

---

## 9. So sánh với `in?` và `exclude?`

Phần mở rộng của Rails làm so sánh dễ hơn.

```ruby
5.in?([1, 3, 5])          # true
"admin".in?(%w[user admin]) # true

"guest".exclude?(["admin", "staff"]) # true
```

---

## 10. So sánh trong scope Rails

Scopes thường chứa điều kiện so sánh.

```ruby
scope :adults, -> { where("age >= ?", 18) }
scope :recent, -> { where("created_at > ?", 7.days.ago) }
```

---

## 11. So sánh Symbol và String trong Rails

```ruby
:role == "role"  # false
```

Rails params luôn là string, nên cần chuẩn hóa:

```ruby
params[:role].to_s == "admin"
```

---

## 12. So sánh trong ENUM Rails

Rails enum hỗ trợ so sánh dạng symbol hoặc string.

```ruby
enum status: { active: 0, archived: 1 }

user.active?      # true/false
user.status == "active"  # true
```

---

## 13. So sánh dùng Pattern Matching (Ruby 2.7+)

```ruby
case user
in {name: "John", age: }
  puts "Tên John"
in {age: 18..}
  puts "Trên 18 tuổi"
end
```

---

## 14. So sánh trong Arel (nâng cao)

Khi cần custom query phức tạp.

```ruby
users = User.arel_table
User.where(users[:age].gt(18))
User.where(users[:name].matches("%john%"))
```

---

# Kết luận

Bạn đã có toàn bộ các dạng so sánh trong Ruby và Ruby on Rails:

* Toán tử cơ bản
* Toán tử đặc biệt `<=>`, `===`
* So sánh trong ActiveRecord
* So sánh thời gian
* Predicate method của Rails
* So sánh object
* So sánh enum
* Pattern matching
* Arel nâng cao

Nếu bạn muốn, tôi có thể tạo **file PDF**, **file DOCX**, hoặc **file HTML** từ markdown này.
