
# Phân biệt Concern, Services, Helpers, Channel trong Ruby on Rails

## 🔹 Concern
- **Ý nghĩa**: Là module dùng để **chia sẻ logic chung** giữa nhiều model hoặc controller.  
- **Nên để logic gì bên trong**:
  - Các method tái sử dụng được ở nhiều nơi (ví dụ: `soft_delete`, `publishable`, `trackable`).
  - Không chứa business logic phức tạp, chỉ nên là **hành vi chung**.
- **Ví dụ**:
  ```ruby
  module SoftDeletable
    extend ActiveSupport::Concern

    included do
      scope :active, -> { where(deleted_at: nil) }
    end

    def soft_delete
      update(deleted_at: Time.current)
    end
  end

  class User < ApplicationRecord
    include SoftDeletable
  end
```

## 🔹 Service Objects
- Ý nghĩa: Là lớp riêng biệt để xử lý business logic phức tạp mà không nên đặt trong model/controller.
- Nên để logic gì bên trong:
- Các quy trình nhiều bước (ví dụ: thanh toán, gửi email, xử lý file).
- Logic liên quan đến nghiệp vụ, không phải chỉ CRUD.
- Ví dụ:

```
class PaymentService
  def initialize(order)
    @order = order
  end

  def call
    charge_card
    send_invoice
    notify_user
  end

  private

  def charge_card
    # gọi API thanh toán
  end

  def send_invoice
    # tạo hóa đơn
  end

  def notify_user
    # gửi email
  end
end

```

## 🔹 Helpers
- Ý nghĩa: Module hỗ trợ view layer, giúp viết code HTML gọn gàng hơn.
- Nên để logic gì bên trong:
- Các hàm format dữ liệu để hiển thị (ví dụ: format ngày, số tiền).
- Các snippet HTML tái sử dụng.
- Ví dụ:
```
module ApplicationHelper
  def format_price(amount)
    number_to_currency(amount, unit: "₫", precision: 0)
  end
end
```


## 🔹 Channels (ActionCable)
- Ý nghĩa: Dùng cho real-time communication qua WebSocket.
- Nên để logic gì bên trong:
- Xử lý subscribe/unsubscribe.
- Nhận dữ liệu từ client và broadcast lại.
- Ví dụ:
```
class ChatChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chat_#{params[:room]}"
  end

  def receive(data)
    ActionCable.server.broadcast("chat_#{params[:room]}", data)
  end
end
```


## 📌 Tóm tắt so sánh
| Thành phần | Vai trò chính | Logic nên đặt |
|------------|---------------|---------------|
| **Concern** | Chia sẻ hành vi chung | Method tái sử dụng, scope, callback |
| **Service** | Business logic phức tạp | Quy trình nhiều bước, xử lý nghiệp vụ |
| **Helper** | Hỗ trợ view | Format dữ liệu, snippet HTML |
| **Channel** | Real-time WebSocket | Subscriptions, broadcast, xử lý dữ liệu realtime |




## 👉 Cách nhớ nhanh
- Concern = chia sẻ logic nhỏ gọn.
- Service = xử lý nghiệp vụ lớn.
- Helper = hỗ trợ hiển thị.
- Channel = giao tiếp realtime.
