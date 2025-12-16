# Khi nào dùng RabbitMQ kết hợp với Sidekiq trong Ruby on Rails?

Tài liệu này giải thích **chi tiết – chậm – rõ ràng – đi từ gốc đến nâng cao** về việc **khi nào, vì sao và dùng RabbitMQ kết hợp với Sidekiq như thế nào** trong Ruby on Rails. Nội dung phù hợp để **học kiến trúc, training team, hoặc làm tài liệu nội bộ**.

---

## 1. Vấn đề gốc: Vì sao cần xử lý bất đồng bộ?

Trong Rails, nếu mọi thứ đều xử lý ngay trong request:

* Request chậm
* User phải chờ
* Server dễ quá tải

### Ví dụ vấn đề

Khi user tạo đơn hàng:

* Ghi DB
* Gửi email
* Trừ tiền
* Gửi notification

➡️ Nếu làm trong 1 request → **rất chậm và dễ lỗi**

➡️ Giải pháp: **Background Job**

---

## 2. Sidekiq là gì? (Giải thích kỹ)

### 2.1 Sidekiq giải quyết vấn đề gì?

* Đẩy công việc nặng ra khỏi request
* Xử lý song song
* Retry khi lỗi

### 2.2 Sidekiq hoạt động như thế nào?

```text
Rails → Redis Queue → Sidekiq Worker → Xử lý
```

* Rails **đẩy job vào Redis**
* Sidekiq **poll Redis**
* Worker lấy job ra xử lý

### 2.3 Sidekiq phù hợp khi nào?

✔ Ứng dụng Rails đơn
✔ Job đơn giản
✔ Không cần routing
✔ Không cần chia sẻ message cho hệ khác

### 2.4 Hạn chế của Sidekiq

❌ Redis chỉ là queue đơn giản
❌ Không fanout tốt
❌ Không phải message broker
❌ Khó dùng cho multi-service

---

## 3. RabbitMQ là gì? (Giải thích từ gốc)

### 3.1 RabbitMQ KHÔNG làm gì?

❌ Không xử lý business logic
❌ Không thay Sidekiq

### 3.2 RabbitMQ LÀM gì?

RabbitMQ là **Message Broker**, nhiệm vụ:

* Nhận message
* Phân phối message
* Đảm bảo message không mất

### 3.3 Thành phần RabbitMQ

* **Producer**: gửi message
* **Exchange**: điều phối message
* **Queue**: chứa message
* **Consumer**: nhận message

### 3.4 Exchange dùng để làm gì?

* **Direct**: gửi đúng queue
* **Fanout**: gửi tất cả queue
* **Topic**: routing theo pattern

---

## 4. Vì sao kết hợp RabbitMQ + Sidekiq?

> RabbitMQ **điều phối việc** – Sidekiq **làm việc**

RabbitMQ giải quyết:

* Event distribution
* Routing
* Reliability

Sidekiq giải quyết:

* Background processing
* Retry logic
* Concurrency

---

## 5. Khi nào CHỈ cần Sidekiq (không RabbitMQ)

### 5.1 Điều kiện

* Monolith Rails
* Job nội bộ
* Không cần event

### 5.2 Ví dụ

```ruby
UserMailer.welcome_email(user).deliver_later
```

### 5.3 Luồng xử lý

```text
User → Rails → Redis → Sidekiq → Mailer
```

---

## 6. Khi BẮT ĐẦU CẦN RabbitMQ

### 6.1 Một sự kiện – nhiều hành động

Sự kiện:

```text
OrderCreated
```

Cần xử lý:

* Email
* Payment
* Notification
* Analytics

➡️ Không nên nhét hết vào 1 Sidekiq job

---

### 6.2 Event-driven architecture

Rails chỉ phát sự kiện:

```text
order.created
```

Các service tự xử lý phần của mình.

---

### 6.3 Cần đảm bảo message delivery

RabbitMQ hỗ trợ:

* ACK: xác nhận xử lý thành công
* NACK: xử lý thất bại
* Retry
* Dead Letter Queue

---

### 6.4 Multi-service / Multi-language

* Rails
* Node.js
* Java

➡️ RabbitMQ là trung tâm giao tiếp

---

## 7. Sơ đồ kiến trúc chi tiết

### 7.1 Sơ đồ tổng thể

```text
+----------------------+
|  Rails Application   |
|  (Producer)          |
+----------+-----------+
           |
           | publish event
           v
+----------------------+
|  RabbitMQ Exchange   |
|  (order_events)     |
+----------+-----------+
           |
   ------------------------------------
   |                |                 |
   v                v                 v
+--------+     +-----------+     +-----------+
| Queue  |     |  Queue    |     |  Queue    |
| Email  |     | Payment   |     | Analytics |
+---+----+     +-----+-----+     +-----+-----+
    |                |                 |
    v                v                 v
 Sidekiq          Sidekiq           Sidekiq
 Worker           Worker            Worker
    |                |                 |
    v                v                 v
 Business         Business          Business
 Logic            Logic             Logic
```

---

### 7.2 Luồng xử lý từng bước

1. Rails tạo Order
2. Rails publish `order.created`
3. RabbitMQ routing message
4. Mỗi queue nhận message
5. Consumer push vào Sidekiq
6. Sidekiq xử lý nghiệp vụ

---

## 8. Ví dụ code thực tế

### 8.1 Publish event

```ruby
RabbitMQ.publish(
  exchange: "order_events",
  routing_key: "order.created",
  payload: { order_id: 1001 }
)
```

---

### 8.2 Consumer

```ruby
class OrderCreatedConsumer
  def call(payload)
    OrderWorker.perform_async(payload["order_id"])
  end
end
```

---

### 8.3 Sidekiq Worker

```ruby
class OrderWorker
  include Sidekiq::Worker

  def perform(order_id)
    Order.find(order_id).process!
  end
end
```

---

## 9. Khi KHÔNG nên dùng RabbitMQ

❌ App nhỏ
❌ Team nhỏ
❌ Không event-driven

➡️ Dùng RabbitMQ sẽ **over-engineering**

---

## 10. So sánh tổng kết

| Tiêu chí      | Sidekiq | RabbitMQ + Sidekiq |
| ------------- | ------- | ------------------ |
| Đơn giản      | ⭐⭐⭐⭐    | ⭐⭐                 |
| Routing       | ❌       | ✅                  |
| Multi-service | ❌       | ✅                  |
| Reliability   | ⚠️      | ✅                  |

---

## 11. Kết luận cuối cùng

> **Sidekiq xử lý công việc**
> **RabbitMQ điều phối công việc**

* App nhỏ → Sidekiq + Redis
* App lớn → RabbitMQ + Sidekiq

---

*File này có thể lưu dưới dạng `rabbitmq-sidekiq-rails.md` đ
