# So sánh Abstract Class và Interface trong Ruby (OOP)

Tài liệu này giải thích **bản chất**, **sự khác nhau** và **cách áp dụng thực tế** giữa **Abstract Class** và **Interface** trong Ruby, kèm ví dụ code rõ ràng.

---

## 1. Lưu ý quan trọng trong Ruby

Ruby **không có từ khóa `interface` chính thức** như Java hay C#.

👉 Ruby mô phỏng:

* **Abstract Class** → dùng `class` + method `raise NotImplementedError`
* **Interface** → dùng `module`

---

## 2. Abstract Class trong Ruby

### 2.1 Khái niệm

Abstract Class là **lớp cha trừu tượng**, dùng để:

* Chứa **state (biến instance)**
* Chứa **logic dùng chung**
* Ép lớp con phải override method quan trọng

👉 Quan hệ: **is-a**

---

### 2.2 Ví dụ Abstract Class

```ruby
class Animal
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def sleep
    "#{name} is sleeping"
  end

  def sound
    raise NotImplementedError, "Subclass must implement sound"
  end
end
```

### 2.3 Class con kế thừa

```ruby
class Dog < Animal
  def sound
    "Gâu gâu"
  end
end

class Cat < Animal
  def sound
    "Meo meo"
  end
end
```

### 2.4 Sử dụng

```ruby
dog = Dog.new("Lucky")
puts dog.sleep   # Lucky is sleeping
puts dog.sound   # Gâu gâu
```

---

### 2.5 Khi nào dùng Abstract Class?

* Có **thuộc tính chung**
* Có **logic dùng chung**
* Quan hệ cha – con rõ ràng
* Không cần đa kế thừa

---

## 3. Interface trong Ruby (Module)

### 3.1 Khái niệm

Interface trong Ruby thường được mô phỏng bằng **module**, dùng để:

* Định nghĩa **hành vi bắt buộc**
* Không chứa state
* Cho phép **đa implement**

👉 Quan hệ: **can-do**

---

### 3.2 Ví dụ Interface

```ruby
module Flyable
  def fly
    raise NotImplementedError, "Implement fly method"
  end
end
```

### 3.3 Class implement Interface

```ruby
class Bird
  include Flyable

  def fly
    "Bird is flying"
  end
end

class Airplane
  include Flyable

  def fly
    "Airplane is flying"
  end
end
```

### 3.4 Sử dụng

```ruby
Bird.new.fly      # Bird is flying
Airplane.new.fly  # Airplane is flying
```

---

### 3.5 Khi nào dùng Interface?

* Chỉ cần **cam kết hành vi**
* Không cần logic chung
* Cần **đa kế thừa hành vi**
* Thiết kế API / Service

---

## 4. So sánh trực tiếp (Ruby context)

| Tiêu chí       | Abstract Class | Interface (Module) |
| -------------- | -------------- | ------------------ |
| Có state       | ✅ Có           | ❌ Không            |
| Có logic chung | ✅ Có           | ❌ Không            |
| Constructor    | ✅ Có           | ❌ Không            |
| Đa kế thừa     | ❌              | ✅                  |
| Quan hệ        | is-a           | can-do             |
| Từ khóa        | class          | module + include   |

---

## 5. Ví dụ thực tế trong Rails

### Abstract Class

```ruby
class ApplicationService
  def self.call(*args)
    new(*args).call
  end

  def call
    raise NotImplementedError
  end
end
```

```ruby
class CreateOrder < ApplicationService
  def call
    "Order created"
  end
end
```

---

### Interface (Module)

```ruby
module Loggable
  def log(message)
    Rails.logger.info(message)
  end
end
```

```ruby
class OrderService
  include Loggable

  def process
    log("Processing order")
  end
end
```

---

## 6. Tư duy ghi nhớ nhanh

> 🧱 **Abstract Class** → Chia sẻ bản chất + dữ liệu + logic

> 🔌 **Interface (Module)** → Cam kết hành vi

---

## 7. Kết luận

* **Abstract Class** dùng khi các class có quan hệ cha – con rõ ràng
* **Interface (Module)** dùng khi cần chuẩn hóa hành vi giữa các class không liên quan
* Ruby linh hoạt, nhưng **dùng đúng giúp code dễ bảo trì hơn**

---

📌 *Tài liệu phù hợp cho học OOP Ruby, Ruby on Rails Service Pattern, và phỏng vấn kỹ thuật.*
