# 📖 So sánh `has_many :through` và `has_and_belongs_to_many` trong Rails

## 🔑 Khái niệm chung
- Cả hai đều dùng để thiết lập **quan hệ nhiều-nhiều** giữa các model trong Rails.  
- Điểm khác biệt chính:
  - **`has_and_belongs_to_many` (HABTM)**: chỉ tạo bảng nối đơn giản, không có model trung gian.  
  - **`has_many :through`**: tạo bảng nối có model trung gian, cho phép thêm dữ liệu và logic.

---

## ⚖️ So sánh chi tiết

| Tiêu chí | `has_and_belongs_to_many` (HABTM) | `has_many :through` |
|----------|-----------------------------------|----------------------|
| **Cấu trúc** | Tạo bảng nối (join table) không có model riêng. | Tạo bảng nối có model trung gian (join model). |
| **Khả năng lưu dữ liệu bổ sung** | Không thể lưu thêm cột ngoài khóa ngoại. | Có thể thêm các cột khác (ví dụ: `joined_at`, `role`). |
| **Độ linh hoạt** | Đơn giản, nhanh gọn, ít code. | Linh hoạt, dễ mở rộng, hỗ trợ validations và callbacks. |
| **Khi nào dùng** | Khi chỉ cần liên kết nhiều-nhiều thuần túy, không cần metadata. | Khi cần quản lý dữ liệu phức tạp trong quan hệ hoặc thêm logic. |
| **Ví dụ** | `has_and_belongs_to_many :categories` trong `Story`. | `has_many :subscriptions` qua `Subscription` model giữa `Person` và `Magazine`. |

---

## 📘 Ví dụ minh họa

### 1. `has_and_belongs_to_many`
```ruby
class Story < ApplicationRecord
  has_and_belongs_to_many :categories
end

class Category < ApplicationRecord
  has_and_belongs_to_many :stories
end
```
- Bảng nối: categories_stories chỉ gồm story_id và category_id.
- Không thể thêm cột khác như priority hay created_at.
### 2. has_many :through
```
class Person < ApplicationRecord
  has_many :subscriptions
  has_many :magazines, through: :subscriptions
end

class Magazine < ApplicationRecord
  has_many :subscriptions
  has_many :people, through: :subscriptions
end

class Subscription < ApplicationRecord
  belongs_to :person
  belongs_to :magazine
end
```

- Bảng subscriptions có thể thêm cột started_on, status, role.
- Cho phép validations:
```
validates :status, presence: true
```
- Có thể viết callbacks trong Subscription.

### 🚨 Lưu ý quan trọng
- HABTM hiện ít được khuyến khích trong Rails hiện đại vì hạn chế mở rộng.
- has_many :through được xem là best practice cho hầu hết các quan hệ nhiều-nhiều.
- Nếu dự án có khả năng phát triển thêm logic, hãy chọn has_many :through ngay từ đầu để tránh refactor sau này.


