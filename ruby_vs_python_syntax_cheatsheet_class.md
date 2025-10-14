# 📝 Cheatsheet So sánh cú pháp Ruby on Rails và Python Django

---

## 🔢 Toán tử cơ bản

| Mô tả | Ruby | Python |
|------|------|--------|
| Cộng | `a + b` | `a + b` |
| So sánh bằng | `a == b` | `a == b` |
| Khác | `a != b` | `a != b` |
| Lớn hơn | `a > b` | `a > b` |
| Nhỏ hơn | `a < b` | `a < b` |
| Gán | `x = 5` | `x = 5` |

---

## 📦 Tạo Hash / Dictionary

| Mô tả | Ruby | Python |
|------|------|--------|
| Tạo mới | `h = {name: "Khang", age: 30}` | `d = {"name": "Khang", "age": 30}` |
| Truy cập | `h[:name]` | `d["name"]` |
| Thêm phần tử | `h[:email] = "a@b.com"` | `d["email"] = "a@b.com"` |

---

## 📋 Tạo Array / List

| Mô tả | Ruby | Python |
|------|------|--------|
| Tạo mới | `arr = [1, 2, 3]` | `lst = [1, 2, 3]` |
| Truy cập | `arr[0]` | `lst[0]` |
| Thêm phần tử | `arr << 4` | `lst.append(4)` |

---

## 🔁 Vòng lặp

| Mô tả | Ruby | Python |
|------|------|--------|
| For loop | `for i in arr do ... end` | `for i in lst:` |
| Each | `arr.each do |x| puts x end` | `for x in lst: print(x)` |
| While | `while x < 5 do ... end` | `while x < 5:` |

---

## ❓ Câu điều kiện

| Mô tả | Ruby | Python |
|------|------|--------|
| If | `if x > 5 ... end` | `if x > 5:` |
| Else | `else ...` | `else:` |
| Elif / elsif | `elsif x == 3` | `elif x == 3:` |
| Unless | `unless x == 0 ... end` | `if x != 0:` |

---

## 🧠 Viết hàm

| Mô tả | Ruby | Python |
|------|------|--------|
| Hàm đơn giản | `def say_hello(name)puts "Hello \#{name}"end` | `def say_hello(name):print(f"Hello {name}")` |
| Trả về giá trị | `def add(a,b) return a + bend` | `def add(a, b): return a + b` |

---

## 🧱 Viết class và kế thừa

| Mô tả | Ruby | Python |
|------|------|--------|
| Tạo class | `class User end` | `class User:` |
| Constructor | `def initialize(name)` | `def __init__(self, name):` |
| Kế thừa | `class Admin < User` | `class Admin(User):` |

---

## 🔄 Method override

| Mô tả | Ruby | Python |
|------|------|--------|
| Ghi đè phương thức | `def to_s "User" end` | `def __str__(self): return "User"` |

---

## 🧩 Class method

| Mô tả | Ruby | Python |
|------|------|--------|
| Định nghĩa | `def self.say_hi puts "Hi" end` | `@classmethod def say_hi(cls): print("Hi")` |

---

## 🧬 Module / Mixin (Ruby) và Decorator (Python)

| Mô tả | Ruby | Python |
|------|------|--------|
| Module | `module Greet def hello puts "Hi" end end` | — |
| Mixin | `include Greet` | — |
| Decorator | — | `@login_required def view(): ...` |

---

## 📌 Ghi chú

- Ruby dùng `include` để mix module vào class.
- Python dùng `@decorator` để thêm hành vi cho hàm hoặc class.

