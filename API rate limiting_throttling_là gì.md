```API rate limiting/throttling là cơ chế kiểm soát lưu lượng truy cập vào một API để đảm bảo hệ thống hoạt động ổn định, tránh bị quá tải. Nó đề cập đến việc giới hạn số lượng request mà một client có thể gửi trong một khoảng thời gian nhất định.

📌 Ý nghĩa chính
Rate limiting: Đặt ngưỡng tối đa cho số request (ví dụ: 100 request/phút). Nếu vượt quá, hệ thống sẽ từ chối hoặc trả về lỗi (thường là HTTP 429 Too Many Requests).

Throttling: Điều chỉnh tốc độ xử lý request, có thể làm chậm hoặc phân bổ đều các request thay vì chặn hẳn.

⚙️ Mục đích
Bảo vệ API khỏi bị lạm dụng hoặc tấn công (ví dụ: DDoS).

Đảm bảo tài nguyên hệ thống được phân bổ công bằng giữa nhiều người dùng.

Giữ cho hiệu năng ổn định, tránh tình trạng nghẽn mạng hoặc server quá tải.

🛠️ Ví dụ thực tế
Một dịch vụ email API cho phép tối đa 500 email/ngày cho mỗi tài khoản.

Một API thời tiết chỉ cho phép 60 request/phút để tránh việc người dùng liên tục spam dữ liệu.

👉 Nói ngắn gọn: Rate limiting/throttling là cách để kiểm soát tần suất gọi API nhằm bảo vệ hệ thống và đảm bảo công bằng cho tất cả người dùng.
```
