- JWT giống như “thẻ căn cước” mà bạn mang theo, còn Session giống như “chìa khóa phòng khách sạn” do server giữ. JWT tiện lợi cho hệ thống phân tán và API, trong khi Session đơn giản, dễ quản lý và phù hợp với ứng dụng truyền thống.

🔑 So sánh JWT và Session


<img width="692" height="681" alt="image" src="https://github.com/user-attachments/assets/d30e2476-2922-4072-864e-e022083b0249" />

📌 Ví dụ dễ hiểu
- Session: Bạn vào khách sạn, lễ tân ghi thông tin bạn và đưa chìa khóa phòng. Mỗi lần bạn dùng dịch vụ, họ tra thông tin trong hệ thống.
- JWT: Bạn mang theo giấy tờ tùy thân (token). Mỗi nơi bạn đến chỉ cần kiểm tra giấy tờ, không cần gọi về lễ tân.

⚠️ Rủi ro & Lưu ý
- Session: dễ bị tấn công CSRF nếu không bảo vệ cookie.
- JWT: token dài, nếu bị đánh cắp thì hacker có toàn quyền đến khi token hết hạn. Cần HTTPS, ngắn hạn, và refresh token.
