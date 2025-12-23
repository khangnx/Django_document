# So sánh và mối quan hệ giữa Channel, Exchange, Producer, Queue trong RabbitMQ

## 🟢 Producer
- **Vai trò**: Thành phần tạo và gửi thông điệp (message).
- **Nhiệm vụ**: Đẩy message vào **Exchange** thông qua **Channel**.
- **Quan hệ**: Producer không gửi trực tiếp vào Queue, mà luôn gửi qua Exchange.

---

## 🔄 Channel
- **Vai trò**: Kết nối logic (virtual connection) bên trong một TCP connection đến RabbitMQ.
- **Nhiệm vụ**: Là đường truyền để Producer và Consumer giao tiếp với RabbitMQ.
- **Quan hệ**: 
  - Producer sử dụng Channel để gửi message vào Exchange.
  - Consumer sử dụng Channel để nhận message từ Queue.

---

## 📬 Exchange
- **Vai trò**: Bộ định tuyến (router) của RabbitMQ.
- **Nhiệm vụ**: Nhận message từ Producer và quyết định chuyển message đến Queue nào dựa trên **routing key** và **binding rules**.
- **Các loại Exchange**:
  - **Direct**: Gửi message đến Queue có routing key khớp chính xác.
  - **Fanout**: Phát message đến tất cả Queue được bind.
  - **Topic**: Gửi message đến Queue dựa trên pattern của routing key.
  - **Headers**: Dựa trên header thay vì routing key.

---

## 📥 Queue
- **Vai trò**: Nơi lưu trữ message cho đến khi Consumer lấy ra xử lý.
- **Nhiệm vụ**: Giữ message theo thứ tự FIFO (trừ khi có cấu hình khác).
- **Quan hệ**:
  - Queue nhận message từ Exchange.
  - Consumer lấy message từ Queue thông qua Channel.

---

## 🔗 Mối quan hệ tổng thể
1. **Producer** tạo message và gửi qua **Channel**.
2. **Channel** chuyển message đến **Exchange**.
3. **Exchange** định tuyến message đến một hoặc nhiều **Queue** dựa trên binding/routing.
4. **Queue** lưu trữ message cho đến khi **Consumer** lấy ra xử lý.

---

## 📊 Sơ đồ luồng dữ liệu
```Producer → Channel → Exchange → Queue → Consumer```

- Producer chỉ biết gửi vào Exchange.
- Exchange quyết định Queue nào sẽ nhận message.
- Queue giữ message cho Consumer.

  ==========================================================================================================================================================================



## 🐇 Kiến trúc RabbitMQ cho gửi mail hàng loạt
- Producer: service tạo user, publish message vào exchange.
- Exchange: định tuyến message đến queue theo routing key (ví dụ "send_email").
- Queue: chứa danh sách email cần gửi.
- Consumer/Worker: nhiều worker chạy song song, mỗi worker lấy message từ queue và thực hiện gửi mail.
## 👉 Ưu điểm:
- Không bị nghẽn khi tạo user hàng loạt.
- Có thể scale số lượng worker để xử lý nhanh hơn.
- RabbitMQ đảm bảo phân phối công bằng (round-robin) giữa các worker.

## 💻 Ví dụ Ruby với Bunny (topic exchange)
```
require 'bunny'

# 1. Kết nối và tạo channel
connection = Bunny.new
connection.start
channel = connection.create_channel

# 2. Khai báo exchange kiểu topic
exchange = channel.topic("user_events")

# 3. Khai báo queue cho email
email_queue = channel.queue("email_jobs", durable: true)
email_queue.bind(exchange, routing_key: "user.created")

# 4. Producer: khi tạo user, publish message
1_000_000.times do |i|
  user_email = "user#{i}@example.com"
  exchange.publish(
    { email: user_email, subject: "Welcome!", body: "Hello #{user_email}" }.to_json,
    routing_key: "user.created",
    persistent: true
  )
end

puts "Published 1 million email jobs!"

# 5. Consumer: worker xử lý gửi mail
email_queue.subscribe(block: true) do |delivery_info, properties, body|
  job = JSON.parse(body)
  puts "Sending email to #{job['email']}..."
  # Gọi hàm gửi mail thực tế ở đây (SMTP, API của SendGrid, v.v.)
end
```

## ⚙️ Thực hành tốt khi gửi mail hàng loạt
- Durable queue & persistent message: đảm bảo không mất job khi RabbitMQ restart.
- Prefetch: dùng channel.prefetch(n) để mỗi worker chỉ lấy n message một lúc, tránh quá tải.
- Scaling: chạy nhiều worker trên nhiều server để xử lý nhanh hơn.
- Retry & Dead-letter queue: nếu gửi mail thất bại, đưa vào queue khác để retry.
- Batching: có thể gom nhiều email trong một batch nếu hệ thống mail hỗ trợ.

# Ví dụ thứ 2 "dụ ta có hai nhiệm vụ là khi user tạo accout ta sẽ gủi mail cho user đó và đồng thời gủi message lên điện thoại ta sẽ chia trong RabitMQ ra sao:

# Ví dụ RabbitMQ cho 2 nhiệm vụ: gửi mail và gửi message lên điện thoại

## 🎯 Bài toán
Khi **user tạo account**, hệ thống cần thực hiện **2 nhiệm vụ song song**:
1. Gửi email chào mừng đến user.
2. Gửi thông báo (push notification/SMS) lên điện thoại.

---

## 🟢 Producer
- Producer chính là **service tạo account**.
- Sau khi tạo account thành công, Producer sẽ phát ra một **message** chứa thông tin user (ví dụ: userId, email, phone).

---

## 🔄 Exchange
- Tạo một **Exchange** (ví dụ: `account_created_exchange`).
- Loại Exchange phù hợp: **Fanout** hoặc **Topic**.
  - **Fanout**: phát message đến tất cả Queue được bind → phù hợp khi muốn nhiều dịch vụ cùng nhận.
  - **Topic**: linh hoạt hơn, có thể định tuyến theo loại nhiệm vụ (email, sms).

---

## 📥 Queue
- Tạo **2 Queue** riêng biệt:
  - `send_email_queue`: dành cho service gửi email.
  - `send_sms_queue`: dành cho service gửi SMS/Push notification.

---

## 🔗 Binding
- Bind cả hai Queue vào Exchange:
  - Nếu dùng **Fanout Exchange** → tất cả Queue đều nhận message.
  - Nếu dùng **Topic Exchange** → Producer gửi message với routing key, ví dụ:
    - `account.email` → đi vào `send_email_queue`.
    - `account.sms` → đi vào `send_sms_queue`.

---

## 🧑‍💻 Consumer
- Service Email (Consumer 1) lắng nghe `send_email_queue`.
- Service SMS/Push (Consumer 2) lắng nghe `send_sms_queue`.

---

## Code sẽ như sau:

### bạn chỉ cần:
### - 1 Channel:
- Channel là kết nối logic giữa ứng dụng và RabbitMQ.
- Producer (service tạo account) dùng channel này để publish message vào Exchange.
- Các Consumer (Email Service, SMS Service) cũng dùng channel riêng của chúng để subscribe từ Queue.
- Thực tế, mỗi ứng dụng thường mở ít nhất một channel để giao tiếp, nhưng về mặt thiết kế bạn không cần tạo nhiều channel cho cùng một Producer chỉ để gửi đến nhiều Queue.
### - 1 Exchange:
- Exchange nhận message từ Producer qua channel.
- Exchange định tuyến message đến các Queue dựa trên loại Exchange (fanout, topic, direct).
### - 2 Queue:
- send_email_queue → dành cho Consumer xử lý gửi email.
- send_sms_queue → dành cho Consumer xử lý gửi SMS/push.
- Mỗi Queue sẽ nhận bản sao message từ Exchange và xử lý độc lập.
```
Tóm tắt luồng
Producer (Account Service)
        ↓ (1 Channel)
     Exchange (fanout)
        ↓
 ┌───────────────┐   ┌───────────────┐
 │ send_email_q  │   │ send_sms_q    │
 └───────────────┘   └───────────────┘
        ↓                  ↓
   Email Service       SMS/Push Service
```

## 👉 Như vậy: 1 channel + 1 exchange + 2 queue là đủ để giải quyết bài toán.

```
# Gem cần: gem install bunny

require 'bunny'
require 'json'

# =========================
# Producer: Account Service
# =========================
connection = Bunny.new(hostname: 'localhost')
connection.start

channel = connection.create_channel

exchange_name = 'account_created_exchange'
exchange = channel.fanout(exchange_name, durable: true)

# Khai báo Queue và bind (nếu chưa có)
email_queue = channel.queue('send_email_queue', durable: true)
sms_queue   = channel.queue('send_sms_queue', durable: true)

email_queue.bind(exchange)
sms_queue.bind(exchange)

# Gửi message khi user tạo account
user_data = { userId: 123, email: 'newuser@example.com', phone: '+84901234567' }
message = user_data.to_json

exchange.publish(message, persistent: true)
puts "[x] Sent account created message"

connection.close
```
## Consumer cho Email Service
```
require 'bunny'
require 'json'

connection = Bunny.new(hostname: 'localhost')
connection.start

channel = connection.create_channel
queue = channel.queue('send_email_queue', durable: true)

puts "[*] Waiting for messages in send_email_queue. To exit press CTRL+C"

queue.subscribe(block: true) do |_delivery_info, _properties, body|
  data = JSON.parse(body)
  puts "[x] Sending email to: #{data['email']}"
  # Thực hiện logic gửi email ở đây
end
```

## Consumer cho SMS/Push Service

```
require 'bunny'
require 'json'

connection = Bunny.new(hostname: 'localhost')
connection.start

channel = connection.create_channel
queue = channel.queue('send_sms_queue', durable: true)

puts "[*] Waiting for messages in send_sms_queue. To exit press CTRL+C"

queue.subscribe(block: true) do |_delivery_info, _properties, body|
  data = JSON.parse(body)
  puts "[x] Sending SMS/Push to: #{data['phone']}"
  # Thực hiện logic gửi SMS/Push ở đây
end
```

## 📊 Luồng hoạt động
- Producer (Account Service) gửi message vào fanout exchange.
- Exchange phân phối message đến cả send_email_queue và send_sms_queue.
- Consumer Email nhận message từ send_email_queue và xử lý gửi email.
- Consumer SMS nhận message từ send_sms_queue và xử lý gửi SMS/push.
### 👉 Với cách này, bạn chỉ cần 1 channel cho Producer, 1 exchange, và 2 queue cho 2 nhiệm vụ.

=====================================================================================================================================
# Nếu dùng thêm routing key thì code như sau

## Topic Exchange, bạn có thể định tuyến message dựa trên routing key. Điều này cho phép Producer gửi cùng một loại sự kiện (ví dụ: account.created) nhưng phân biệt rõ ràng nhiệm vụ bằng routing key như account.email và account.sms.
Dưới đây là ví dụ đầy đủ bằng Ruby (bunny gem):

```
# Gem cần: gem install bunny
require 'bunny'
require 'json'

# =========================
# Producer: Account Service
# =========================
connection = Bunny.new(hostname: 'localhost')
connection.start

channel = connection.create_channel

exchange_name = 'account_created_exchange'
exchange = channel.topic(exchange_name, durable: true)

# Khai báo Queue
email_queue = channel.queue('send_email_queue', durable: true)
sms_queue   = channel.queue('send_sms_queue', durable: true)

# Bind Queue với routing key
email_queue.bind(exchange, routing_key: 'account.email')
sms_queue.bind(exchange, routing_key: 'account.sms')

# Gửi message cho Email
user_data_email = { userId: 123, email: 'newuser@example.com' }
exchange.publish(user_data_email.to_json,
                 routing_key: 'account.email',
                 persistent: true)

# Gửi message cho SMS
user_data_sms = { userId: 123, phone: '+84901234567' }
exchange.publish(user_data_sms.to_json,
                 routing_key: 'account.sms',
                 persistent: true)

puts "[x] Sent account created messages"

connection.close
```

## Consumer cho Email Service

```
require 'bunny'
require 'json'

connection = Bunny.new(hostname: 'localhost')
connection.start

channel = connection.create_channel
queue = channel.queue('send_email_queue', durable: true)

puts "[*] Waiting for messages in send_email_queue. To exit press CTRL+C"

queue.subscribe(block: true) do |_delivery_info, _properties, body|
  data = JSON.parse(body)
  puts "[x] Sending email to: #{data['email']}"
  # Logic gửi email ở đây
end

```

## Consumer cho SMS Service

```
require 'bunny'
require 'json'

connection = Bunny.new(hostname: 'localhost')
connection.start

channel = connection.create_channel
queue = channel.queue('send_sms_queue', durable: true)

puts "[*] Waiting for messages in send_sms_queue. To exit press CTRL+C"

queue.subscribe(block: true) do |_delivery_info, _properties, body|
  data = JSON.parse(body)
  puts "[x] Sending SMS to: #{data['phone']}"
  # Logic gửi SMS ở đây
end
```


## 📊 Luồng hoạt động với Topic Exchange
- Producer gửi message với routing key account.email → Exchange định tuyến đến send_email_queue.
- Producer gửi message với routing key account.sms → Exchange định tuyến đến send_sms_queue.
- Consumer Email xử lý email, Consumer SMS xử lý SMS.
### 👉 Ưu điểm: dễ mở rộng. Ví dụ sau này bạn muốn thêm account.push thì chỉ cần tạo thêm Queue và bind với routing key account.push, Producer không cần thay đổi nhiều.



