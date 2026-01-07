# Để hình dung cách Laravel hoạt động khi gọi Facade Mail::send, ta có thể vẽ sơ đồ luồng xử lý như sau:

## 📌 Quy trình đầy đủ với Service Provider
1.	 Service Provider đăng ký Mailer
	-	Trong Illuminate\Mail\MailServiceProvider, Laravel đăng ký binding mailer vào Service C-ntainer.
	-	Nó cấu hình driver (SMTP, Mailgun, Postmark, v.v.) dựa trên file config/mail.php.
2.	Người dùng gọi Facade
	-	Lệnh: Mail::send(...).
	-	Facade Mail ánh xạ tới binding mailer.
3.	Service Container trả về Mailer
	-	Container cung cấp instance của Illuminate\Mail\Mailer.
4.	Mailer xử lý nội dung email
	-	Tạo đối tượng Message từ view/template và dữ liệu.
5.	Mailer gọi Transport
	-	Gửi message qua driver đã cấu hình (SMTP, Mailgun, …).
6.	Transport gửi email ra ngoài
	-	Kết nối tới mail server và gửi email đến người nhận.


## 📊 Sơ đồ luồng (có Service Pr-vider)
```
[User Code: Mail::send]
        |
        v
[Facade: Mail]
        |
        v
[Service Container]
        |
        v
[MailServiceProvider đăng ký Mailer]
        |
        v
[Mailer tạo Message]
        |
        v
[Transport (SMTP/Mailgun/...)]
        |
        v
[Mail Server -> Người nhận]

```

## 👉 Như vậy, Service Provider chính là nơi Laravel “gắn” Mailer vào Service Container, đảm bả- Facade có thể truy cập đúng instance.
