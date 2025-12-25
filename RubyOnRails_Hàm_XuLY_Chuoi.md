# 📖 Ruby & Rails String Cheat Sheet

## 🔤 Ruby String Methods (Core)

| Hàm              | Mục đích                        | Ví dụ |
|------------------|---------------------------------|-------|
| `upcase` / `downcase` | Viết hoa / viết thường | `"hello".upcase # => "HELLO"` |
| `capitalize`     | Viết hoa chữ cái đầu            | `"ruby".capitalize # => "Ruby"` |
| `swapcase`       | Đảo hoa ↔ thường                | `"RuBy".swapcase # => "rUbY"` |
| `reverse`        | Đảo ngược chuỗi                 | `"ruby".reverse # => "ybur"` |
| `length` / `size`| Độ dài chuỗi                    | `"hello".length # => 5"` |
| `include?`       | Kiểm tra chứa chuỗi con         | `"hello".include?("he") # => true"` |
| `start_with?` / `end_with?` | Kiểm tra đầu/cuối   | `"hello".start_with?("he") # => true"` |
| `split`          | Tách chuỗi thành mảng           | `"a,b,c".split(",") # => ["a","b","c"]"` |
| `strip` / `lstrip` / `rstrip` | Xóa khoảng trắng | `"  hi  ".strip # => "hi"` |
| `sub` / `gsub`   | Thay thế chuỗi con              | `"ruby".sub("r","R") # => "Ruby"` |
| `tr`             | Thay thế ký tự theo bảng ánh xạ | `"ruby".tr("a-z","n-za-m") # => "ehol"` |
| `delete`         | Xóa ký tự                       | `"hello".delete("l") # => "heo"` |
| `insert`         | Chèn ký tự                      | `"hello".insert(3,"X") # => "helXlo"` |
| `concat`         | Nối chuỗi                       | `"hi".concat(" there") # => "hi there"` |

---

## 🛠 Rails ActiveSupport Inflector Methods

| Hàm          | Mục đích                  | Ví dụ |
|--------------|---------------------------|-------|
| `blank?`     | Kiểm tra chuỗi rỗng       | `"   ".blank? # => true"` |
| `present?`   | Ngược lại của `blank?`    | `"abc".present? # => true"` |
| `squish`     | Xóa khoảng trắng thừa     | `"a   b".squish # => "a b"` |
| `camelize`   | Chuyển sang CamelCase     | `"hello_world".camelize # => "HelloWorld"` |
| `underscore` | Chuyển sang snake_case    | `"HelloWorld".underscore # => "hello_world"` |
| `dasherize`  | Chuyển `_` thành `-`      | `"hello_world".dasherize # => "hello-world"` |
| `pluralize`  | Số nhiều                  | `"cat".pluralize # => "cats"` |
| `singularize`| Số ít                     | `"cars".singularize # => "car"` |
| `humanize`   | Chuỗi dễ đọc              | `"employee_salary".humanize # => "Employee salary"` |
| `titleize`   | Viết hoa từng từ          | `"hello world".titleize # => "Hello World"` |
| `tableize`   | Tên bảng từ class         | `"ScaleScore".tableize # => "scale_scores"` |
| `classify`   | Tên class từ bảng         | `"scale_scores".classify # => "ScaleScore"` |
| `parameterize` | Tạo slug URL            | `"Xin chào thế giới!".parameterize # => "xin-chao-the-gioi"` |
| `truncate`   | Rút gọn chuỗi             | `"This is a long text".truncate(10) # => "This is..."` |
| `truncate_words` | Rút gọn theo số từ    | `"Ruby on Rails là framework mạnh mẽ".truncate_words(3) # => "Ruby on Rails..."` |

---

## ✨ Ví dụ đặc biệt

### ROT13 với `tr`
```ruby
"ruby".tr("a-z", "n-za-m") 
# => "ehol"

Ví dụ ROT13 với tr
# ROT13: dịch mỗi chữ cái trong bảng chữ cái sang vị trí cách 13 ký tự
message = "Hello Ruby on Rails"

encoded = message.tr("A-Za-z", "N-ZA-Mn-za-m")
# => "Uryyb Ehol ba Envyf"

decoded = encoded.tr("A-Za-z", "N-ZA-Mn-za-m")
# => "Hello Ruby on Rails"


Giải thích:
- "A-Za-z": tập hợp toàn bộ chữ cái hoa và thường.
- "N-ZA-Mn-za-m": ánh xạ mỗi chữ cái sang chữ cái cách 13 vị trí.
- Khi gọi lần thứ hai, ta giải mã về chuỗi gốc.
```
