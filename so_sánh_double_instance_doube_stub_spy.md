
Phần 1: So sánh double vs instance_double trong RSpec

| Tiêu chí                | double                              | instance_double                          |
|-------------------------|-------------------------------------|------------------------------------------|
| Định nghĩa             | Tạo đối tượng giả không ràng buộc  | Tạo đối tượng giả dựa trên class cụ thể |
| Kiểm tra phương thức   | Không kiểm tra                     | Kiểm tra phương thức có tồn tại trong class |
| Khi nào dùng           | Khi chỉ cần đối tượng giả đơn giản | Khi muốn đảm bảo tính đúng đắn với class thực |

Ví dụ:
# double
user = double("User", name: "Alice")
puts user.name  # => "Alice"

# instance_double
class User
  def name; end
end
user = instance_double(User, name: "Alice")
puts user.name  # => "Alice"

Phần 2: So sánh stub vs spy trong RSpec

| Tiêu chí                | stub                                | spy                                     |
|-------------------------|-------------------------------------|-----------------------------------------|
| Định nghĩa             | Thay thế phương thức bằng giá trị  | Theo dõi phương thức được gọi          |
| Kiểm tra gọi phương thức| Không                              | Có thể kiểm tra số lần gọi và tham số  |
| Khi nào dùng           | Khi chỉ cần trả về giá trị giả     | Khi muốn kiểm tra hành vi gọi phương thức |

Ví dụ:
# stub
allow(user).to receive(:name).and_return("Alice")
puts user.name  # => "Alice"

# spy
user = spy("User")
user.name("Alice")
expect(user).to have_received(:name).with("Alice")
