# Design Patterns trong Ruby on Rails (Giải thích chi tiết)

Tài liệu này giải thích **từng Design Pattern phổ biến trong Ruby on Rails** theo cách **dễ hiểu – đúng tư duy Rails – có ví dụ thực tế – chỉ rõ khi nào nên / không nên dùng**.

---

## Tóm tắt khái niệm các Design Pattern

| Pattern               | Khái niệm cốt lõi                                                                                                         |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| MVC                   | Chia ứng dụng thành Model (dữ liệu + nghiệp vụ), View (hiển thị), Controller (điều phối request) để tách biệt trách nhiệm |
| Service Object        | Đóng gói **một nghiệp vụ cụ thể** vào một object độc lập, thường có method `call`                                         |
| Form Object           | Đại diện cho **một form** (có validate + save) khi form không map 1-1 với model                                           |
| Decorator / Presenter | Bọc (wrap) object gốc để xử lý **logic trình bày** mà không làm bẩn model hay view                                        |
| Query Object          | Đóng gói **một truy vấn database phức tạp** thành object riêng                                                            |
| Policy                | Định nghĩa **quyền (authorization)** theo từng resource và hành động                                                      |
| Callback / Observer   | Cho phép object **phản ứng tự động** khi có sự kiện xảy ra                                                                |
| Strategy              | Cho phép **thay đổi thuật toán/hành vi** tại runtime mà không dùng `if/case`                                              |
| Command               | Chuẩn hóa một hành động thành object có thể gọi (`call`)                                                                  |
| Repository            | Lớp trung gian trừu tượng hóa việc truy cập dữ liệu (ít dùng trong Rails)                                                 |

---

## 1. MVC Pattern (Model – View – Controller)

### 1.1 MVC là gì?

MVC là kiến trúc cốt lõi của Rails, chia ứng dụng thành 3 phần rõ ràng:

* **Model**: dữ liệu + business logic
* **View**: hiển thị dữ liệu (HTML / JSON)
* **Controller**: nhận request, gọi logic, trả response

### 1.2 Trách nhiệm từng phần

#### Model

* Validate dữ liệu
* Quan hệ database
* Logic nghiệp vụ liên quan trực tiếp đến dữ liệu

```ruby
class User < ApplicationRecord
  validates :email, presence: true

  def active?
    last_login_at && last_login_at > 30.days.ago
  end
end
```

#### Controller

* Nhận params
* Gọi service / model
* Render kết quả

```ruby
class UsersController < ApplicationController
  def show
    @user = User.find(params[:id])
  end
end
```

#### View

* Chỉ hiển thị
* Không chứa business logic phức tạp

```erb
<%= @user.email %>
```

### 1.3 Sai lầm thường gặp

* Fat Controller
* Logic nhét vào View

➡️ Vì vậy Rails sinh ra **Service Object, Decorator, Form Object** để hỗ trợ MVC.

---

## 2. Service Object Pattern

### 2.1 Vấn đề cần giải quyết

Khi nghiệp vụ phức tạp:

* Tạo user
* Gửi email
* Ghi log
* Gọi API ngoài

❌ Nếu nhét hết vào Controller / Model → rất khó maintain.

### 2.2 Ý tưởng của Service Object

* Mỗi service = **1 hành động nghiệp vụ**
* Có method `call`
* Dễ test, dễ đọc

### 2.3 Ví dụ

```ruby
class Users::CreateUser
  def initialize(params)
    @params = params
  end

  def call
    user = User.create!(@params)
    UserMailer.welcome(user).deliver_later
    user
  end
end
```

Controller:

```ruby
Users::CreateUser.new(user_params).call
```

### 2.4 Khi nào nên dùng

* Business logic > 5–10 dòng
* Có nhiều bước xử lý
* Cần reuse

---

## 3. Form Object Pattern

### 3.1 Vấn đề

Một form nhưng:

* Lưu nhiều bảng
* Validation không thuộc 1 model cụ thể

### 3.2 Ý tưởng

* Form là một object riêng
* Dùng `ActiveModel::Model`

### 3.3 Ví dụ

```ruby
class SignupForm
  include ActiveModel::Model

  attr_accessor :email, :password, :profile_name

  validates :email, :password, presence: true

  def save
    return false unless valid?

    ActiveRecord::Base.transaction do
      user = User.create!(email:, password:)
      Profile.create!(user:, name: profile_name)
    end
  end
end
```

### 3.4 Khi nào nên dùng

* Form phức tạp
* Không muốn làm bẩn Model

---

## 4. Decorator / Presenter Pattern

### 4.1 Vấn đề

Logic hiển thị bị nhét vào View

```erb
<%= user.active? ? 'Active' : 'Inactive' %>
```

### 4.2 Ý tưởng

* Tách logic trình bày ra khỏi View

### 4.3 Ví dụ

```ruby
class UserDecorator
  def initialize(user)
    @user = user
  end

  def status_label
    @user.active? ? '🟢 Active' : '🔴 Inactive'
  end
end
```

View:

```erb
<%= UserDecorator.new(@user).status_label %>
```

### 4.4 Khi dùng

* Format data
* Logic hiển thị

---

## 5. Query Object Pattern

### 5.1 Vấn đề

Model có nhiều scope phức tạp

### 5.2 Ý tưởng

* Mỗi query = 1 object

### 5.3 Ví dụ

```ruby
class Users::RecentActive
  def self.call
    User.where(active: true)
        .where('created_at > ?', 7.days.ago)
  end
end
```

### 5.4 Khi dùng

* Query dài
* Dùng nhiều nơi

---

## 6. Policy Pattern (Authorization)

### 6.1 Vấn đề

Phân quyền viết rải rác trong Controller

### 6.2 Ý tưởng

* Mỗi resource có 1 policy

### 6.3 Ví dụ (Pundit)

```ruby
class PostPolicy
  def update?
    user.admin? || post.user == user
  end
end
```

Controller:

```ruby
authorize @post
```

### 6.4 Khi dùng

* Hệ thống có role
* Authorization phức tạp

---

## 7. Callback / Observer Pattern

### 7.1 Ý tưởng

Model tự phản ứng khi có sự kiện

```ruby
class Order < ApplicationRecord
  after_commit :notify

  def notify
    OrderMailer.notify(self).deliver_later
  end
end
```

### 7.2 Cảnh báo

* Dễ gây side-effect
* Khó debug

➡️ Logic lớn → dùng Service thay thế

---

## 8. Strategy Pattern

### 8.1 Vấn đề

Nhiều cách xử lý cho cùng hành vi

### 8.2 Ví dụ

```ruby
class PaymentProcessor
  def initialize(strategy)
    @strategy = strategy
  end

  def pay(amount)
    @strategy.pay(amount)
  end
end
```

### 8.3 Khi dùng

* Tránh `case when`
* Mở rộng dễ

---

## 9. Command Pattern (`call`)

### 9.1 Ý tưởng

* Chuẩn hóa cách gọi hành động

```ruby
class SendWelcomeEmail
  def self.call(user)
    new(user).call
  end

  def call
    UserMailer.welcome(@user).deliver_later
  end
end
```

### 9.2 Khi dùng

* Kết hợp Service Object
* Clean API

---

## 10. Repository Pattern (ít dùng)

Rails đã có ActiveRecord nên thường **không cần** Repository.

Chỉ dùng khi:

* DDD nặng
* Muốn tách DB hoàn toàn

---

## Nâng cấp chuyên sâu: Phân tích OOP – Anti-pattern – Test – Thực chiến – Sơ đồ

---

## Phân tích theo OOP cho từng Pattern

### MVC Pattern

* **Encapsulation**: Model đóng gói dữ liệu + logic
* **Single Responsibility**: mỗi lớp đúng 1 vai trò

❌ Không nên dùng khi:

* Controller chứa business logic phức tạp

🧠 Thực tế dự án lớn:

* MVC là nền, luôn kết hợp Service / Policy

---

### Service Object Pattern

🔍 Bản chất OOP:

* **Single Responsibility Principle**
* **Encapsulation**: nghiệp vụ nằm gọn trong 1 object

❌ Không nên dùng khi:

* Logic chỉ 1–2 dòng
* Chỉ gọi ActiveRecord đơn giản

🧪 RSpec mẫu:

```ruby
RSpec.describe Users::CreateUser do
  it 'creates user successfully' do
    user = described_class.new(email: 'a@test.com').call
    expect(user).to be_persisted
  end
end
```

🧠 Mapping thực tế:

* Đăng ký user
* Thanh toán
* Sync API

📐 Luồng chạy:
Controller → Service → Model → Job/Mailer

---

### Form Object Pattern

🔍 OOP:

* **Abstraction**: form đại diện cho use-case

❌ Không nên dùng khi:

* Form map 1–1 với model

🧪 RSpec:

```ruby
RSpec.describe SignupForm do
  it 'is invalid without email' do
    form = SignupForm.new(password: '123')
    expect(form).not_to be_valid
  end
end
```

🧠 Thực tế:

* Signup
* Wizard nhiều bước

📐 Luồng:
Controller → Form → Transaction → Models

---

### Decorator / Presenter

🔍 OOP:

* **Open/Closed Principle**

❌ Không nên dùng khi:

* Logic chỉ format đơn giản

🧪 RSpec:

```ruby
RSpec.describe UserDecorator do
  it 'shows active label' do
    user = build(:user, active: true)
    expect(described_class.new(user).status_label).to include('Active')
  end
end
```

🧠 Thực tế:

* Dashboard
* Admin panel

---

### Query Object Pattern

🔍 OOP:

* **Separation of Concerns**

❌ Không nên dùng khi:

* Scope đơn giản

🧪 RSpec:

```ruby
RSpec.describe Users::RecentActive do
  it 'returns active users' do
    expect(described_class.call).to all(be_active)
  end
end
```

🧠 Thực tế:

* Report
* Search filter

---

### Policy Pattern

🔍 OOP:

* **Polymorphism**: mỗi resource 1 policy

❌ Không nên dùng khi:

* App không có role

🧪 RSpec:

```ruby
RSpec.describe PostPolicy do
  it 'allows owner to update' do
    expect(described_class.new(user, post)).to permit(:update)
  end
end
```

🧠 Thực tế:

* SaaS multi-tenant

📐 Luồng:
Controller → Policy → Result

---

### Callback / Observer

🔍 OOP:

* **Observer Pattern**

❌ Không nên dùng khi:

* Logic lớn

🧠 Thực tế:

* Audit log nhỏ

---

### Strategy Pattern

🔍 OOP:

* **Polymorphism**

❌ Không nên dùng khi:

* Chỉ 1 cách xử lý

🧠 Thực tế:

* Payment
* Shipping

---

### Command Pattern

🔍 OOP:

* **Command Encapsulation**

❌ Không nên dùng khi:

* Không cần abstraction

🧠 Thực tế:

* Job
* Action

---

## Tổng kết

> Pattern không phải để khoe kiến trúc, mà để **giải quyết đúng vấn đề ở đúng thời điểm**.

> Rails không ép dùng Design Pattern, nhưng **dự án lớn bắt buộc phải dùng** nếu muốn code sạch, dễ test và mở rộng lâu dài.

---

👉 Nếu bạn muốn, mình có thể viết tiếp:

* Test RSpec cho từng pattern
* Sơ đồ kiến trúc Rails thực tế
* Checklist chọn pattern cho dự án lớn



# Chuẩn Rails Folder Structure cho Dự Án Lớn

Tài liệu này mô tả **cấu trúc thư mục Rails chuẩn cho dự án vừa & lớn**, dựa trên kinh nghiệm thực tế (SaaS, dự án > 1 năm), kết hợp **Rails convention + Design Patterns**.

---

## 1. Nguyên tắc thiết kế thư mục

### 🎯 Mục tiêu

* Dễ đọc – dễ tìm code
* Dễ test
* Dễ mở rộng lâu dài
* Tránh "God Model / God Service"

### 🧠 Nguyên tắc

* **Convention over Configuration** (tôn trọng Rails)
* **Mỗi thư mục = một trách nhiệm rõ ràng**
* Logic phức tạp **không nhét vào controller / model**

---

## 2. Tổng quan cấu trúc đề xuất

```text
app/
├── controllers/
├── models/
├── services/
│   └── users/
├── forms/
├── queries/
├── policies/
├── decorators/
├── jobs/
├── mailers/
├── serializers/
├── validators/
└── lib/
```

---

## 3. Controllers (Skinny Controller)

📂 `app/controllers`

### Trách nhiệm

* Nhận params
* Gọi service / form / query
* Render response

❌ Không nên có:

* Business logic
* Query phức tạp

```ruby
class UsersController < ApplicationController
  def create
    user = Users::CreateUser.new(user_params).call
    render json: user
  end
end
```

---

## 4. Models (Fat Model vừa đủ)

📂 `app/models`

### Trách nhiệm

* Validation
* Association
* Logic gắn chặt với dữ liệu

❌ Không nên có:

* Workflow phức tạp
* Gọi API ngoài

```ruby
class User < ApplicationRecord
  has_many :posts
  validates :email, presence: true
end
```

---

## 5. Services (Business Logic chính)

📂 `app/services`

### Mục đích

* Mỗi service = 1 use-case

```text
services/
└── orders/
    ├── create_order.rb
    ├── cancel_order.rb
```

```ruby
class Orders::CreateOrder
  def call
    # business logic
  end
end
```

🧠 Quy ước:

* Có `call`
* Không kế thừa

---

## 6. Forms (Form Object)

📂 `app/forms`

### Dùng khi

* 1 form → nhiều model

```ruby
class SignupForm
  include ActiveModel::Model
end
```

---

## 7. Queries (Query Object)

📂 `app/queries`

### Dùng khi

* Query dài
* Filter phức tạp

```ruby
class Reports::MonthlyRevenue
  def self.call(month)
    Order.where(created_at: month.all_month)
  end
end
```

---

## 8. Policies (Authorization)

📂 `app/policies`

### Dùng với Pundit

```ruby
class OrderPolicy
  def update?
    user.admin?
  end
end
```

---

## 9. Decorators / Presenters

📂 `app/decorators`

### Dùng cho view logic

```ruby
class OrderDecorator
  def status_badge
    '🟢 Paid'
  end
end
```

---

## 10. Jobs

📂 `app/jobs`

### Background job

```ruby
class SendEmailJob < ApplicationJob
end
```

---

## 11. Mailers

📂 `app/mailers`

```ruby
class UserMailer < ApplicationMailer
end
```

---

## 12. Serializers / API Layer

📂 `app/serializers`

* ActiveModelSerializers / fast_jsonapi

```ruby
class UserSerializer
end
```

---

## 13. Validators

📂 `app/validators`

```ruby
class EmailValidator < ActiveModel::EachValidator
end
```

---

## 14. lib/

📂 `lib/`

### Chứa code dùng chung

* External API client
* Utility

❌ Không để business logic core

---

## 15. Cấu trúc spec tương ứng

```text
spec/
├── services/
├── forms/
├── queries/
├── policies/
├── models/
└── controllers/
```

---

## 16. Checklist nhanh

✅ Controller < 50 dòng
✅ Model không chứa workflow
✅ Service test được độc lập
✅ Query không nằm trong controller

---

## Tổng kết

> Rails lớn không phải là nhiều code, mà là **đúng chỗ – đúng trách nhiệm – đúng pattern**.

---

👉 Có thể mở rộng tiếp:

* Folder structure cho Monolith lớn
* Chuẩn naming convention
* Refactor dự án cũ sang structure này
