# Triển khai REST API, GraphQL API, gRPC API trong Rails

> Ví dụ với **Rails API application** và quan hệ:
>
> * User **has_many** Orders
> * Order **belongs_to** User

---

## 0. Chuẩn bị chung

### 0.1 Tạo Rails API project

```bash
rails new shop_api --api -d postgresql
cd shop_api
rails db:create
```

### 0.2 Tạo Model & Quan hệ

```bash
rails g model User name:string email:string
rails g model Order user:references total_price:decimal status:string
rails db:migrate
```

```ruby
# app/models/user.rb
class User < ApplicationRecord
  has_many :orders, dependent: :destroy
end
```

```ruby
# app/models/order.rb
class Order < ApplicationRecord
  belongs_to :user
end
```

---

# PHẦN A – REST API (Chuẩn Rails nhất)

## 1. Khi nào dùng REST API?

* CRUD rõ ràng
* Client đơn giản (FE, mobile)
* Dễ cache (HTTP cache)

---

## 2. Thiết kế endpoint REST

| Method | Endpoint          | Ý nghĩa        |
| ------ | ----------------- | -------------- |
| GET    | /users            | Danh sách user |
| GET    | /users/:id        | Chi tiết user  |
| POST   | /users            | Tạo user       |
| GET    | /users/:id/orders | Order của user |
| POST   | /users/:id/orders | Tạo order      |

---

## 3. Routes

```ruby
# config/routes.rb
Rails.application.routes.draw do
  resources :users do
    resources :orders, only: [:index, :create]
  end
end
```

---

## 4. Controller

### 4.1 UsersController

```ruby
class UsersController < ApplicationController
  def index
    render json: User.all
  end

  def show
    render json: User.find(params[:id])
  end

  def create
    user = User.create!(user_params)
    render json: user, status: :created
  end

  private

  def user_params
    params.require(:user).permit(:name, :email)
  end
end
```

### 4.2 OrdersController

```ruby
class OrdersController < ApplicationController
  before_action :set_user

  def index
    render json: @user.orders
  end

  def create
    order = @user.orders.create!(order_params)
    render json: order, status: :created
  end

  private

  def set_user
    @user = User.find(params[:user_id])
  end

  def order_params
    params.require(:order).permit(:total_price, :status)
  end
end
```

---

## 5. Ưu / Nhược điểm REST

### ✅ Ưu điểm

* Dễ hiểu
* Tool hỗ trợ mạnh (Postman, Swagger)
* Chuẩn HTTP

### ❌ Nhược điểm

* Over-fetch / Under-fetch
* Versioning phức tạp

---

# PHẦN B – GraphQL API (Query linh hoạt)

## 1. Khi nào dùng GraphQL?

* FE phức tạp (SPA, Mobile)
* Tránh nhiều endpoint
* Client chủ động chọn field

```
## Query trong GraphQL
- Mục đích: Lấy dữ liệu từ server, tương tự như câu lệnh SELECT trong SQL.
- Đặc điểm:
- Chỉ đọc dữ liệu, không thay đổi trạng thái hệ thống.
- Idempotent: gọi nhiều lần vẫn cho kết quả giống nhau.

## Mutation trong GraphQL
• 	Mục đích: Thay đổi dữ liệu trên server (tạo, cập nhật, xóa).
• 	Đặc điểm:
• 	Có thể tạo ra side-effect (ví dụ thêm bản ghi mới).
• 	Chỉ các top-level mutation fields mới được phép gây tác động, còn các field con phải không có side-effect
```

---

## 2. Cài GraphQL cho Rails

```bash
gem 'graphql'
bundle install
rails g graphql:install
```

---

## 3. Định nghĩa Type

### 3.1 UserType

```ruby
module Types
  class UserType < Types::BaseObject
    field :id, ID, null: false
    field :name, String
    field :email, String
    field :orders, [Types::OrderType]
  end
end
```

### 3.2 OrderType

```ruby
module Types
  class OrderType < Types::BaseObject
    field :id, ID
    field :total_price, Float
    field :status, String
  end
end
```

---

## 4. Query

```ruby
class Types::QueryType < Types::BaseObject
  field :users, [Types::UserType], null: false

  def users
    User.all
  end
end
```

### Query ví dụ

```graphql
{
  users {
    id
    name
    orders {
      id
      totalPrice
    }
  }
}
```

---

## 5. Mutation

```ruby
class Mutations::CreateOrder < Mutations::BaseMutation
  argument :user_id, ID
  argument :total_price, Float

  field :order, Types::OrderType

  def resolve(user_id:, total_price:)
    order = Order.create!(user_id: user_id, total_price: total_price)
    { order: order }
  end
end
```

---

## 6. Ưu / Nhược điểm GraphQL

### ✅ Ưu điểm

* Tránh tránh over-fetch
* FE linh hoạt
* 1 endpoint

### ❌ Nhược điểm

* Cache khó
* Learning curve cao
* Query phức tạp có thể chậm

---

# PHẦN C – gRPC API (High performance)

## 1. Khi nào dùng gRPC?

* Microservices
* High throughput
* Internal service

---

## 2. Cài gRPC

```bash
gem 'grpc'
gem 'google-protobuf'
bundle install
```

---

## 3. Định nghĩa proto

```proto
syntax = "proto3";

service UserService {
  rpc GetUser (UserRequest) returns (UserResponse);
}

message UserRequest {
  int64 id = 1;
}

message OrderMessage {
  int64 id = 1;
  double total_price = 2;
}

message UserResponse {
  int64 id = 1;
  string name = 2;
  repeated OrderMessage orders = 3;
}
```

Generate code:

```bash
grpc_tools_ruby_protoc -I . --ruby_out=. --grpc_out=. user.proto
```

---

## 4. Implement Service

```ruby
class UserService < UserService::Service
  def get_user(request, _call)
    user = User.includes(:orders).find(request.id)

    UserResponse.new(
      id: user.id,
      name: user.name,
      orders: user.orders.map do |o|
        OrderMessage.new(id: o.id, total_price: o.total_price)
      end
    )
  end
end
```

---

## 5. Ưu / Nhược điểm gRPC

### ✅ Ưu điểm

* Nhanh (binary protocol)
* Typed contract
* Tối ưu service-to-service

### ❌ Nhược điểm

* Khó debug
* Không phù hợp public API
* Browser support kém

---

# SO SÁNH TỔNG HỢP

| Tiêu chí     | REST | GraphQL | gRPC |
| ------------ | ---- | ------- | ---- |
| Public API   | ⭐⭐⭐⭐ | ⭐⭐⭐     | ⭐    |
| FE phức tạp  | ⭐⭐   | ⭐⭐⭐⭐    | ⭐    |
| Performance  | ⭐⭐   | ⭐⭐      | ⭐⭐⭐⭐ |
| Microservice | ⭐⭐   | ⭐⭐      | ⭐⭐⭐⭐ |

---

# PHẦN D – Cấu trúc thư mục & Cách gọi API trong Rails

## 1. Cấu trúc thư mục khuyến nghị (Rails API thực tế)

```
app/
├── controllers/
│   ├── application_controller.rb
│   ├── users_controller.rb        # REST Users
│   ├── orders_controller.rb       # REST Orders
│   └── graphql/
│       └── graphql_controller.rb
│
├── models/
│   ├── user.rb
│   └── order.rb
│
├── services/                       # Business logic
│   ├── users/
│   │   └── create_user.rb
│   └── orders/
│       └── create_order.rb
│
├── serializers/                    # REST response control
│   ├── user_serializer.rb
│   └── order_serializer.rb
│
├── graphql/
│   ├── types/
│   │   ├── user_type.rb
│   │   └── order_type.rb
│   ├── mutations/
│   │   └── create_order.rb
│   └── schema.rb
│
├── grpc/
│   └── user_service.rb
│
└── policies/                       # (Pundit) phân quyền
    ├── user_policy.rb
    └── order_policy.rb
```

> Nguyên tắc:
>
> * **Controller mỏng** (chỉ nhận params, trả response)
> * **Business logic nằm trong service**
> * **Model không chứa logic phức tạp**

---

## 2. Gọi logic từ Controller (Best Practice)

### 2.1 Service Object

```ruby
# app/services/orders/create_order.rb
module Orders
  class CreateOrder
    def self.call(user:, params:)
      user.orders.create!(params)
    end
  end
end
```

### 2.2 Controller gọi Service

```ruby
class OrdersController < ApplicationController
  before_action :set_user

  def create
    order = Orders::CreateOrder.call(
      user: @user,
      params: order_params
    )

    render json: order, status: :created
  end

  private

  def set_user
    @user = User.find(params[:user_id])
  end

  def order_params
    params.require(:order).permit(:total_price, :status)
  end
end
```

✅ Lợi ích:

* Dễ test
* Dễ tái sử dụng (REST, GraphQL, gRPC dùng chung)

---

## 3. Có nên gọi API trong Model không?

### ❌ Không nên (Anti-pattern)

```ruby
class User < ApplicationRecord
  def create_order(params)
    orders.create!(params)
  end
end
```

❗ Nhược điểm:

* Model phình to
* Khó test
* Khó scale domain logic

---

## 4. Cách dùng Model đúng vai trò

```ruby
class Order < ApplicationRecord
  belongs_to :user

  validates :total_price, presence: true
  validates :status, inclusion: { in: %w[pending paid canceled] }
end
```

✔ Model chỉ nên chứa:

* Association
* Validation
* Scope

---

## 5. Gọi chung logic cho REST / GraphQL / gRPC

### 5.1 REST

```ruby
Orders::CreateOrder.call(user: user, params: params)
```

### 5.2 GraphQL Mutation

```ruby
def resolve(user_id:, total_price:)
  user = User.find(user_id)
  order = Orders::CreateOrder.call(
    user: user,
    params: { total_price: total_price }
  )
  { order: order }
end
```

### 5.3 gRPC Service

```ruby
order = Orders::CreateOrder.call(
  user: user,
  params: { total_price: request.total_price }
)
```

🎯 Một logic – dùng cho 3 loại API

---

## 6. Khi nào nên thêm Layer khác?

| Layer        | Khi dùng                |
| ------------ | ----------------------- |
| Service      | Business logic          |
| Policy       | Phân quyền              |
| Serializer   | Format response         |
| Query Object | Query phức tạp          |
| Form Object  | Validate input phức tạp |

---

## Gợi ý thực tế cho Rails

* **Public API** → REST
* **Admin / SPA** → GraphQL
* **Internal services** → gRPC

Nếu bạn muốn, mình có thể:

* Vẽ sơ đồ kiến trúc tổng hợp
* So sánh theo use-case thực tế Rails lớn
* Viết RSpec test cho từng loại API
