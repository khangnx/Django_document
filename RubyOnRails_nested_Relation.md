# 📘 Nested Attributes trong Rails là gì?
## Nested attributes trong Rails là cơ chế cho phép bạn truyền dữ liệu của các model liên kết thông qua model cha. Nói cách khác, thay vì phải tạo/cập nhật từng bản ghi con riêng lẻ, bạn có thể gửi tất cả dữ liệu trong một request duy nhất và Rails sẽ tự động xử lý.

### 🔑 Cách hoạt động
- Được kích hoạt bằng macro accepts_nested_attributes_for trong model cha.
- Khi bạn gọi Model.update hoặc Model.create với tham số *_attributes, Rails sẽ:
- Tạo hoặc cập nhật bản ghi con tương ứng.
- Chạy validations trên các bản ghi con.
- Nếu bất kỳ bản ghi con nào không hợp lệ, parent sẽ không được lưu.

### 🏗 Ví dụ minh họa
Giả sử bạn có một Book và nhiều Page:

```
class Book < ApplicationRecord
  has_many :pages
  accepts_nested_attributes_for :pages
end

class Page < ApplicationRecord
  belongs_to :book
  validates :title, presence: true
end


Khi tạo một cuốn sách kèm các trang:
book = Book.create(
  title: "Ruby Guide",
  pages_attributes: [
    { title: "Introduction" },
    { title: "Chapter 1" }
  ]
)
```

## ➡ Rails sẽ tự động tạo cả Book và các Page liên quan trong một lần gọi.

###🎯 Lợi ích
- Đơn giản hóa form: Bạn có thể dùng fields_for để tạo form cho cả parent và child trong cùng một view.
- Giữ tính toàn vẹn dữ liệu: Validations chạy xuyên suốt, đảm bảo parent không được lưu nếu child không hợp lệ.
- Hỗ trợ xóa/bỏ qua: Với allow_destroy và reject_if, bạn có thể linh hoạt xóa hoặc bỏ qua nested records.

##👉 Tóm lại: Nested attributes là cách Rails cho phép bạn quản lý dữ liệu của các model liên kết thông qua parent model, giúp việc tạo/cập nhật dữ liệu phức tạp trở nên gọn gàng và an toàn hơn.



### Trong ví dụ mà mình đưa ra:
- Book là parent model (cha)
- Nó có quan hệ has_many :pages.
- Khi bạn tạo/cập nhật một Book, bạn có thể truyền kèm dữ liệu cho các Page thông qua pages_attributes.
- Page là child model (con)
- Nó có quan hệ belongs_to :book.
- Mỗi Page phải gắn với một Book cụ thể.
- Validations trong Page sẽ được Rails kiểm tra khi bạn lưu Book với nested attributes.







# Rails xử lý validations cho nested attributes bằng cách áp dụng validations trực tiếp trên các model con khi chúng được tạo/cập nhật thông qua accepts_nested_attributes_for. Nếu nested record không hợp lệ, lỗi sẽ được gắn vào parent object và toàn bộ transaction sẽ bị rollback.

##🔑 Cách Rails xử lý validations cho nested attributes
### 1. Kích hoạt nested attributes
- Sử dụng accepts_nested_attributes_for trong model cha để cho phép truyền dữ liệu vào các model liên kết.
- Ví dụ:
```
class Book < ApplicationRecord
  has_many :pages
  accepts_nested_attributes_for :pages, allow_destroy: true, reject_if: :all_blank
end
```
- Khi gọi book.update(pages_attributes: [...]), Rails sẽ tự động tạo/cập nhật các bản ghi Page.
### 2. Tích hợp với validations
- Validations được định nghĩa trong model con (ví dụ Page) vẫn chạy bình thường khi nested attributes được gửi qua parent.
- Nếu một nested record không hợp lệ:
- Parent object sẽ không được lưu.
- Lỗi từ nested record sẽ được thêm vào errors của parent.
- Toàn bộ transaction bị rollback để đảm bảo tính toàn vẹn dữ liệu.
### 3. Các tùy chọn quan trọng
- reject_if: bỏ qua nested record nếu điều kiện đúng (ví dụ bỏ qua form trống).
- allow_destroy: cho phép xóa nested record bằng cách truyền _destroy: true.
- autosave: tự động bật khi dùng accepts_nested_attributes_for, đảm bảo validations chạy trên các bản ghi con.
### 4. Ví dụ minh họa
```
class Page < ApplicationRecord
  belongs_to :book
  validates :title, presence: true
end

book = Book.new(
  title: "Ruby Guide",
  pages_attributes: [{ title: "" }] # thiếu title
)

book.valid? # => false
book.errors.full_messages
# => ["Pages title can't be blank"]
```

## ➡ Rails sẽ không lưu Book vì nested Page không hợp lệ.
### 5. Validations có điều kiện
- Có thể viết validations tùy theo logic:
```
validates :address, presence: true, if: -> { organisation.requires_address? }
```
- Điều này cho phép kiểm soát khi nào nested attributes cần được validate.

### 📌 Tóm tắt
- Rails chạy validations trên nested models khi chúng được truyền qua parent bằng accepts_nested_attributes_for.
- Nếu nested record không hợp lệ, parent sẽ không được lưu và lỗi sẽ hiển thị trong errors.
- Các tùy chọn như reject_if, allow_destroy giúp kiểm soát việc validate và xử lý nested attributes linh hoạt.

