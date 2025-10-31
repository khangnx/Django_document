
# 📝 Cheatsheet So sánh cú pháp Ruby on Rails và Python Django

## 🔢 Toán tử cơ bản

| Mô tả | Ruby | Python |
|------|------|--------|
| Cộng | `a + b` | `a + b` |
| Trừ | `a - b` | `a - b` |
| Nhân | `a * b` | `a * b` |
| Chia | `a / b` | `a / b` |
| Chia lấy dư | `a % b` | `a % b` |
| Lũy thừa | `a ** b` | `a ** b` |
| So sánh bằng | `a == b` | `a == b` |
| Khác | `a != b` | `a != b` |
| Lớn hơn | `a > b` | `a > b` |
| Nhỏ hơn | `a < b` | `a < b` |
| Gán giá trị | `a = 5` | `a = 5` |

---

## 📦 Tạo mới Hash / Dictionary

| Mô tả | Ruby | Python |
|------|------|--------|
| Tạo hash | `user = {name: "Khang", age: 30}` | `user = {"name": "Khang", "age": 30}` |
| Truy cập | `user[:name]` | `user["name"]` |
| Thêm phần tử | `user[:email] = "a@b.com"` | `user["email"] = "a@b.com"` |

---

## 📚 Tạo Array / List

| Mô tả | Ruby | Python |
|------|------|--------|
| Tạo array | `arr = [1, 2, 3]` | `arr = [1, 2, 3]` |
| Truy cập phần tử | `arr[0]` | `arr[0]` |
| Thêm phần tử | `arr << 4` | `arr.append(4)` |
| Duyệt mảng | ```arr.each do |x| puts x end``` | ```for x in arr: print(x)``` |

---

## 🔁 Vòng lặp

| Mô tả | Ruby | Python |
|------|------|--------|
| For | `for i in 1..5 do puts i end` | `for i in range(1, 6): print(i)` |
| While | `while i < 5 do puts i; i += 1 end` | `while i < 5: print(i); i += 1` |
| Duyệt mảng | "`arr.each do |x| puts x end`" | `for x in arr: print(x)` |

---

## ❓ Câu điều kiện

| Mô tả | Ruby | Python |
|------|------|--------|
| If | `if a > b then puts "a" end` | `if a > b: print("a")` |
| If-else | `if a > b then puts "a" else puts "b" end` | `if a > b: print("a") else: print("b")` |
| Else-if | `elsif` | `elif` |
| Toán tử 3 ngôi | `a > b ? "a" : "b"` | `"a" if a > b else "b"` |

---

## ✅ Ghi chú

- Ruby dùng `do ... end`, Python dùng `:` và thụt lề.
- Ruby dùng `puts`, Python dùng `print`.
- Ruby có cú pháp `<<` để thêm phần tử vào array, Python dùng `append()`.

