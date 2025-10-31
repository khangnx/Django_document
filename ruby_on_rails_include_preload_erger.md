
# Tổng hợp kiến thức về includes, preload, eager_load, joins, references trong Ruby on Rails

## 🔍 Tổng quan các phương thức

| Phương thức     | Mục đích chính                  | Truy vấn SQL     | Tránh N+1 | Dùng được với `where` từ bảng liên quan |
|----------------|----------------------------------|------------------|-----------|------------------------------------------|
| includes       | Eager loading thông minh         | Tùy tình huống   | ✅        | ✅ (cần references)                      |
| preload        | Eager loading đơn giản           | Luôn tách riêng  | ✅        | ❌                                       |
| eager_load     | Eager loading bằng JOIN          | Luôn JOIN        | ✅        | ✅                                       |
| joins          | JOIN bảng liên quan              | Luôn JOIN        | ❌        | ✅                                       |
| references     | Cho phép dùng bảng liên quan trong where | Không sinh truy vấn | ❌ | ✅ (kết hợp với includes) |

## 🧠 Giải thích chi tiết từng phương thức

### 1. includes
- Mục đích: Tránh N+1 query bằng cách tự động chọn giữa JOIN hoặc truy vấn riêng.
- Cách hoạt động: Nếu không dùng điều kiện từ bảng liên quan → Rails dùng 2 truy vấn. Nếu có điều kiện → Rails dùng LEFT OUTER JOIN.
- Ví dụ:
  Post.includes(:comments)
  Post.includes(:comments).where("comments.approved = true").references(:comments)

### 2. preload
- Mục đích: Luôn thực hiện truy vấn riêng biệt cho bảng liên quan.
- Ưu điểm: Đơn giản, dễ hiểu.
- Nhược điểm: Không dùng được điều kiện từ bảng liên quan trong where.
- Ví dụ:
  Post.preload(:comments)

### 3. eager_load
- Mục đích: Luôn thực hiện LEFT OUTER JOIN.
- Ưu điểm: Có thể dùng điều kiện từ bảng liên quan.
- Nhược điểm: Có thể sinh ra truy vấn phức tạp.
- Ví dụ:
  Post.eager_load(:comments).where("comments.approved = true")

### 4. joins
- Mục đích: JOIN bảng liên quan mà không nạp dữ liệu.
- Ưu điểm: Dùng được điều kiện từ bảng liên quan.
- Nhược điểm: Không tránh được N+1 nếu truy cập dữ liệu liên quan sau đó.
- Ví dụ:
  Post.joins(:comments).where("comments.approved = true")

### 5. references
- Mục đích: Cho Rails biết bạn đang dùng bảng liên quan trong where khi dùng includes.
- Ví dụ:
  Post.includes(:comments).where("comments.approved = true").references(:comments)

## 📦 Ví dụ cụ thể với mô hình User, Post, Comment

Giả định mô hình:

```
class User < ApplicationRecord
  has_many :posts
end

class Post < ApplicationRecord
  belongs_to :user
  has_many :comments
end

class Comment < ApplicationRecord
  belongs_to :post
end
```

## Các tình huống:

### 1. Tránh N+1 khi hiển thị bài viết và comment

```
Post.includes(:comments).each do |post|
  post.comments.each do |comment|
    puts comment.body
  end
end
```

### 2. Lọc bài viết có comment được duyệt
```
Post.includes(:comments)
    .where("comments.approved = true")
    .references(:comments)
```
### 3. Dùng preload để nạp comment
```
Post.preload(:comments).each do |post|
  post.comments.each do |comment|
    puts comment.body
  end
end
```

### 4. Dùng eager_load để JOIN và lọc
```
Post.eager_load(:comments)
    .where("comments.approved = true")
```
### 5. Dùng joins để lọc nhưng không nạp dữ liệu
```
Post.joins(:comments)
    .where("comments.approved = true")
```
### 6. Lấy tất cả user và bài viết của họ
```
User.includes(:posts).each do |user|
  user.posts.each do |post|
    puts post.title
  end
end
```
### 7. Lọc user có bài viết chứa từ khóa
```
User.includes(:posts)
    .where("posts.title LIKE ?", "%Rails%")
    .references(:posts)
```
## ✅ Tổng kết

| Mục tiêu | Phương thức phù hợp |
|----------|---------------------|
| Tránh N+1 khi truy cập dữ liệu liên quan | includes, preload, eager_load |
| Truy vấn có điều kiện từ bảng liên quan | joins, eager_load, includes + references |
| Truy vấn đơn giản, không cần lọc | preload |
| Truy vấn phức tạp, cần JOIN và lọc | eager_load hoặc joins


# So sánh update và updated!, save và save!

## ✅ update
```
user.update(name: "Khang")
```

Trả về true nếu cập nhật thành công.
Trả về false nếu validation thất bại.
Không raise exception (không gây lỗi chương trình).

## ✅ update! (có dấu chấm than)
```
user.update!(name: "Khang")
```
Trả về bản ghi nếu thành công.
Raise exception (ActiveRecord::RecordInvalid) nếu validation thất bại.
Dùng khi bạn muốn chương trình dừng lại ngay nếu có lỗi.

Tương tự save và save! cũng nhu6 uodate.
