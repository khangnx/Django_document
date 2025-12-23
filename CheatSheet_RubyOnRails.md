# 🚀 Ruby on Rails Cheat Sheet (Có giải thích ngắn gọn)

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
