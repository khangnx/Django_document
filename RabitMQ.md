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


