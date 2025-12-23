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


