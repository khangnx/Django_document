#PHẦN KẾ TIẾP

## 1. Object & Nil
- `nil?` – kiểm tra object có phải nil
- `present?` – có giá trị (Rails)
- `blank?` – nil, rỗng, false
- `respond_to?` – object có method không
- `is_a?` – kiểm tra kiểu cha
- `instance_of?` – đúng class hiện tại

## 2. Numeric / Math
- `even?` – số chẵn
- `odd?` – số lẻ
- `zero?` – bằng 0
- `positive?` – > 0
- `negative?` – < 0
- `round` – làm tròn
- `ceil` – làm tròn lên
- `floor` – làm tròn xuống
- `abs` – trị tuyệt đối
- `Math.sqrt` – căn bậc hai
- `rand` – số ngẫu nhiên

## 3. String
- `empty?` – chuỗi rỗng
- `blank?` – rỗng hoặc chỉ có space
- `include?` – có chứa chuỗi con
- `start_with?` – bắt đầu bằng
- `end_with?` – kết thúc bằng
- `upcase` – viết hoa
- `downcase` – viết thường
- `camelize` – snake_case → CamelCase
- `underscore` – CamelCase → snake_case
- `gsub` – thay thế theo regex

## 4. Array / Enumerable
- `empty?` – mảng rỗng
- `any?` – có phần tử thỏa điều kiện
- `all?` – tất cả thỏa
- `none?` – không có phần tử nào
- `map` – biến đổi phần tử
- `select` – lọc
- `reject` – loại bỏ
- `find` – tìm phần tử đầu tiên

## 5. Hash
- `key?` – có key không
- `value?` – có value không
- `dig` – lấy sâu, tránh nil
- `merge` – gộp hash

## 6. Date / Time
- `Time.current` – thời gian hiện tại (theo timezone)
- `n.days.ago` – n ngày trước
- `n.hours.from_now` – n giờ sau
- `past?` – trong quá khứ
- `future?` – trong tương lai
- `today?` – hôm nay
- `beginning_of_day` – đầu ngày
- `end_of_day` – cuối ngày

## 7. ActiveRecord
- `create` – tạo record
- `save` – lưu record
- `update` – cập nhật
- `destroy` – xóa (có callback)
- `where` – lọc dữ liệu
- `find_by` – tìm 1 record
- `includes` – tránh N+1
- `valid?` – hợp lệ
- `persisted?` – đã lưu DB chưa

## 8. Controller
- `params.require` – bắt buộc param
- `params.permit` – whitelist param
- `render` – trả response
- `redirect_to` – chuyển hướng
- `before_action` – filter trước action

## 9. Safe / Defensive
- `&.` – safe navigation
- `try` – gọi method an toàn
- `presence` – trả nil nếu rỗng
- `||=` – gán giá trị mặc định

## 10. Rails Env
- `Rails.env.production?` – môi trường production
- `Rails.root` – thư mục gốc
- `Rails.logger` – ghi log

## 11. Debug
- `puts` – in log
- `pp` – pretty print
- `binding.irb` – debug runtime

---  
# Range trong Ruby on Rails

## 🔹 Khái niệm Range
- **Range** là đối tượng biểu diễn một dãy giá trị từ `start` đến `end`.
- Cú pháp:
  - Inclusive range: `(1..5)` → gồm 1, 2, 3, 4, 5
  - Exclusive range: `(1...5)` → gồm 1, 2, 3, 4

---
#PHẦN KẾ TIẾP
## 🔹 Các hàm kiểm tra Range trong Ruby

| Hàm        | Công dụng | Ví dụ | Kết quả |
|------------|-----------|-------|---------|
| `include?` | Kiểm tra giá trị có nằm trong range (chuyển thành mảng nếu cần). | `(1..5).include?(3)` | `true` |
| `cover?`   | Kiểm tra nhanh giá trị có nằm trong khoảng mà không tạo mảng. | `(1..5).cover?(4.5)` | `true` |
| `===`      | Toán tử case equality, thường dùng trong `case`. | `(1..5) === 3` | `true` |
| `between?` | Dùng trực tiếp trên số để kiểm tra nằm giữa 2 giá trị. | `5.between?(1,10)` | `true` |
| `to_a` + `include?` | Chuyển range thành mảng rồi kiểm tra. | `(1..5).to_a.include?(6)` | `false` |

---

## 🔹 Các hàm mở rộng trong Rails (ActiveSupport)

| Hàm        | Công dụng | Ví dụ | Kết quả |
|------------|-----------|-------|---------|
| `overlap?` / `overlaps?` | Kiểm tra xem hai range có giao nhau không. | `(1..5).overlap?(4..6)` | `true` |
| `sole`     | Trả về phần tử duy nhất trong range nếu chỉ có một. | `(5..5).sole` | `5` |
| `as_json`  | Chuyển range thành JSON (hữu ích khi serialize dữ liệu). | `(1..5).as_json` | `{"begin"=>1, "end"=>5, "excl"=>false}` |

---

## 🔹 Ứng dụng Range trong Rails

### 1. Query với ActiveRecord
```ruby
User.where(age: 18..25)
User.where(created_at: (Time.now.beginning_of_day..Time.now.end_of_day))
```
### 2. Validation
```
validates :age, inclusion: { in: 18..65 }
```

### 3. Điều kiện logic

```
if (13..19).cover?(age)
  puts "Teenager"
end
```
##  Best Practices
- Dùng cover? thay vì include? khi làm việc với range lớn hoặc giá trị không nguyên → nhanh hơn.
- Dùng overlap? khi cần kiểm tra giao nhau giữa hai khoảng (ví dụ: lịch hẹn, booking).
- Dùng between? khi có một giá trị cụ thể và muốn kiểm tra trực tiếp.
- Dùng sole khi cần lấy đúng một phần tử duy nhất từ range.
- Dùng as_json khi cần serialize range để lưu trữ hoặc truyền qua API.
- Dùng Validation với range để đảm bảo dữ liệu nằm trong khoảng cho phép.
- Dùng Điều kiện logic với range để viết code ngắn gọn, dễ đọc.

## ✅ Tóm lại
Các hàm quan trọng nhất để check và thao tác với Range trong Ruby on Rails:
- Ruby: include?, cover?, ===, between?, to_a
- Rails (ActiveSupport): overlap?, sole, as_json
- Ứng dụng: Query, Validation, Điều kiện logic

#PHẦN KẾ TIẾP
# Các hàm xử lý chuỗi & nhập liệu trong Ruby / Rails

## 🔹 Nhập liệu từ bàn phím
- `gets` → Lấy input từ bàn phím (có cả ký tự xuống dòng `\n`).
- `gets.chomp` → Lấy input và loại bỏ ký tự xuống dòng.
- `gets.strip` → Lấy input và loại bỏ khoảng trắng đầu/cuối.

---

## 🔹 Chuyển đổi kiểu dữ liệu
- `to_s` → Chuyển thành chuỗi.
- `to_i` → Chuyển thành số nguyên.
- `to_f` → Chuyển thành số thực (float).
- `to_sym` → Chuyển thành symbol.
- `to_a` → Chuyển thành mảng.
- `to_h` → Chuyển thành hash.

---

## 🔹 Xử lý chuỗi cơ bản
- `chomp` → Xóa ký tự xuống dòng ở cuối chuỗi.
- `strip` → Xóa khoảng trắng đầu/cuối chuỗi.
- `lstrip` → Xóa khoảng trắng bên trái.
- `rstrip` → Xóa khoảng trắng bên phải.
- `upcase` → Viết hoa toàn bộ.
- `downcase` → Viết thường toàn bộ.
- `capitalize` → Viết hoa chữ cái đầu tiên.
- `swapcase` → Đảo ngược hoa/thường.
- `gsub(pattern, replacement)` → Thay thế chuỗi theo regex.
- `sub(pattern, replacement)` → Thay thế chuỗi đầu tiên khớp regex.
- `split(separator)` → Tách chuỗi thành mảng.
- `join(separator)` → Ghép mảng thành chuỗi.

---

## 🔹 Kiểm tra chuỗi
- `empty?` → Kiểm tra chuỗi rỗng.
- `nil?` → Kiểm tra giá trị nil.
- `include?(substr)` → Kiểm tra chuỗi có chứa substring.
- `start_with?(prefix)` → Kiểm tra chuỗi bắt đầu bằng prefix.
- `end_with?(suffix)` → Kiểm tra chuỗi kết thúc bằng suffix.
- `match?(regex)` → Kiểm tra chuỗi khớp regex.

---

## 🔹 Các hàm hữu ích trong Rails (ActiveSupport)
Rails mở rộng thêm nhiều hàm tiện lợi cho chuỗi:
- `blank?` → Chuỗi rỗng hoặc chỉ có khoảng trắng.
- `present?` → Chuỗi có nội dung.
- `squish` → Xóa khoảng trắng dư thừa, chỉ giữ một khoảng trắng giữa các từ.
- `truncate(length)` → Cắt chuỗi theo độ dài.
- `truncate_words(count)` → Cắt chuỗi theo số lượng từ.
- `pluralize` → Chuyển số + từ thành dạng số nhiều.
- `singularize` → Chuyển từ số nhiều thành số ít.
- `titleize` → Viết hoa chữ cái đầu mỗi từ.
- `parameterize` → Chuyển chuỗi thành dạng URL-friendly (slug).
- `underscore` → Chuyển CamelCase thành snake_case.
- `camelize` → Chuyển snake_case thành CamelCase.
- `dasherize` → Chuyển snake_case thành dạng gạch nối.
- `humanize` → Chuyển chuỗi thành dạng dễ đọc cho người.
- `tableize` → Chuyển class name thành tên bảng.
- `classify` → Chuyển tên bảng thành class name.
- `constantize` → Chuyển chuỗi thành hằng số/class/module.

#PHẦN KẾ TIẾP

## ✅ Tóm lại
- **Ruby core:** `gets`, `chomp`, `strip`, `to_s`, `to_i`, `to_f`, `upcase`, `downcase`, `split`, `join`, `include?`, `empty?`, v.v.
- **Rails ActiveSupport:** `blank?`, `present?`, `squish`, `truncate`, `pluralize`, `singularize`, `titleize`, `parameterize`, `underscore`, `camelize`, `dasherize`, `humanize`, `tableize`, `classify`, `constantize`.

- # Tài liệu: Các loại điều kiện trong Ruby on Rails

## 🔹 1. Cấu trúc `if`
```ruby
if condition
  # code khi điều kiện đúng
end


Ví dụ:
if user.active?
  puts "Người dùng đang hoạt động"
end

```

## 🔹 2. if..else
```
if condition
  # code khi đúng
else
  # code khi sai
end

Ví dụ:
if age >= 18
  puts "Đủ tuổi"
else
  puts "Chưa đủ tuổi"
end

```

## 🔹 3. if..elsif..else
```
if condition1
  # code khi condition1 đúng
elsif condition2
  # code khi condition2 đúng
else
  # code mặc định
end


Ví dụ:
if score >= 90
  puts "Xuất sắc"
elsif score >= 75
  puts "Khá"
else
  puts "Trung bình"
end
```


##🔹 4. unless
- Ngược lại với if: chạy khi điều kiện sai.
```
unless condition
  # code khi điều kiện sai
end


Ví dụ:
unless user.admin?
  puts "Không có quyền truy cập"
end

```

##🔹 5. case..when..else
- Dùng để thay thế nhiều if/elsif.
```
case variable
when value1
  # code
when value2
  # code
else
  # mặc định
end


Ví dụ:
case role
when "admin"
  puts "Quản trị viên"
when "editor"
  puts "Biên tập viên"
else
  puts "Người dùng"
end
```


## 🔹 6. Toán tử điều kiện (?:)
- Viết ngắn gọn cho if..else.

```
condition ? value_if_true : value_if_false


Ví dụ:
message = age >= 18 ? "Đủ tuổi" : "Chưa đủ tuổi"

```

## 🔹 7. Modifier if / unless
- Viết ngắn gọn sau câu lệnh.
```
puts "Xin chào" if logged_in?
puts "Không được phép" unless user.admin?

```

## 🔹 8. Rails Helpers liên quan đến điều kiện
Rails bổ sung nhiều helper để viết điều kiện gọn hơn:

```
- present? / blank?
if params[:name].present?
  puts "Có tên"
end
- any? / empty?
puts "Danh sách rỗng" if users.empty?
- try
user.try(:profile).try(:address)

```

## ✅ Tóm lại
Các loại điều kiện trong Ruby on Rails gồm:
- Cơ bản: if, if..else, if..elsif..else, unless
- Phân nhánh: case..when..else
- Ngắn gọn: toán tử ?:, modifier if/unless
- Rails helpers: present?, blank?, any?, empty?, try




