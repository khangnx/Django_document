# Ruby `*args`, `**kwargs`, `&block` – Giải thích chi tiết (OOP & Rails)

Tài liệu này tổng hợp **đầy đủ – có hệ thống – dễ hiểu** về ba khái niệm cực kỳ quan trọng trong Ruby:

* `*args`
* `**kwargs`
* `&block`

Phù hợp cho:

* Người học Ruby từ cơ bản → nâng cao
* Ruby on Rails developer
* Ôn phỏng vấn
* Hiểu code framework & gem

---

## 1️⃣ `*args` – Splat Operator (Positional Arguments)

### 1.1 Khái niệm

```ruby
def method(*args)
end
```

👉 `*args` có nghĩa là:

> Nhận **0, 1 hoặc nhiều tham số** và **gom tất cả thành một Array**

---

### 1.2 Ví dụ cơ bản

```ruby
def demo(*args)
  p args
end

demo(1)
# => [1]

demo(1, 2, 3)
# => [1, 2, 3]

demo()
# => []
```

➡️ `args` **luôn là Array**

---

### 1.3 Bung mảng khi gọi method

```ruby
arr = [1, 2, 3]
demo(*arr)
```

Tương đương:

```ruby
demo(1, 2, 3)
```

---

### 1.4 Ứng dụng trong Rails (Service Pattern)

```ruby
class ApplicationService
  def self.call(*args)
    new(*args).call
  end
end
```

Gọi:

```ruby
CreateOrder.call(user, params)
```

---

## 2️⃣ `**kwargs` – Keyword Arguments

### 2.1 Khái niệm

```ruby
def method(**kwargs)
end
```

👉 `**kwargs` có nghĩa là:

> Nhận tất cả **keyword arguments** và gom chúng thành một **Hash**

---

### 2.2 Ví dụ cơ bản

```ruby
def demo(**kwargs)
  p kwargs
end

demo(a: 1, b: 2)
# => {:a=>1, :b=>2}

demo()
# => {}
```

➡️ `kwargs` **luôn là Hash**

---

### 2.3 Bung keyword arguments

```ruby
options = { a: 1, b: 2 }
demo(**options)
```

---

### 2.4 Vì sao nên dùng `**kwargs`

* Tránh nhầm thứ tự tham số
* Code dễ đọc
* Dễ mở rộng về sau

---

### 2.5 Ví dụ Rails chuẩn

```ruby
class CreateOrder
  def initialize(user:, params:, notify: true)
    @user = user
    @params = params
    @notify = notify
  end
end
```

Gọi:

```ruby
CreateOrder.new(
  user: current_user,
  params: order_params,
  notify: false
)
```

---

## 3️⃣ `&block` – Block Parameter

### 3.1 Block là gì?

```ruby
3.times do |i|
  puts i
end
```

➡️ `do ... end` hoặc `{}` chính là **block**

---

### 3.2 Nhận block bằng `&block`

```ruby
def demo(&block)
  block.call if block
end

demo { puts "Hello" }
```

👉 `&block` biến block thành **Proc object**

---

### 3.3 `yield` vs `&block`

```ruby
def demo
  yield if block_given?
end
```

| `yield`           | `&block`          |
| ----------------- | ----------------- |
| Nhanh             | Chậm hơn          |
| Không truyền tiếp | Truyền block được |
| Không lưu block   | Lưu thành Proc    |

---

### 3.4 Truyền block đi tiếp

```ruby
def wrapper(&block)
  execute(&block)
end

def execute(&block)
  block.call
end
```

---

## 4️⃣ Kết hợp `*args`, `**kwargs`, `&block`

### 4.1 Chữ ký method chuẩn trong Rails

```ruby
def call(*args, **kwargs, &block)
end
```

Ý nghĩa:

* `*args` → positional arguments
* `**kwargs` → keyword arguments
* `&block` → hành vi truyền vào

---

### 4.2 Ví dụ Service Object nâng cao

```ruby
class ApplicationService
  def self.call(*args, **kwargs, &block)
    new(*args, **kwargs).call(&block)
  end
end
```

```ruby
class CreateOrder < ApplicationService
  def call
    yield if block_given?
    "Order created"
  end
end
```

Gọi:

```ruby
CreateOrder.call(user: user) do
  puts "After create"
end
```

---

## 5️⃣ Bảng tổng hợp dễ nhớ

| Cú pháp    | Ý nghĩa                  |
| ---------- | ------------------------ |
| `*args`    | Gom nhiều tham số thường |
| `**kwargs` | Gom keyword arguments    |
| `&block`   | Nhận block thành Proc    |
| `yield`    | Gọi block nhanh          |

---

## 6️⃣ Lỗi thường gặp

❌ Nhầm Hash với keyword arguments

❌ Quên `**` khi truyền kwargs

❌ Dùng `&block` khi chỉ cần `yield`

❌ Method signature không đồng bộ

---

## 7️⃣ Tóm tắt cốt lõi

> 🔑 `*args` → linh hoạt số lượng tham số
> 🔑 `**kwargs` → rõ nghĩa, dễ mở rộng
> 🔑 `&block` → truyền hành vi, callback

---

📌 *Tài liệu này rất quan trọng để đọc hiểu Rails source code, gem và thiết kế Service / API chuyên nghiệp.*
