- MVCC (Multi-Version Concurrency Control) hay “Kiểm soát đồng thời đa phiên bản” là một kỹ thuật trong hệ quản trị cơ sở dữ liệu cho phép nhiều giao dịch đọc và ghi dữ liệu cùng lúc mà không cần khóa truyền thống, giúp tăng hiệu suất và giảm xung đột. Nó lưu nhiều phiên bản của bản ghi để đảm bảo tính nhất quán và trải nghiệm mượt mà cho người dùng.

🔑 Khái niệm MVCC
  • 	Định nghĩa: MVCC là cơ chế quản lý đồng thời trong cơ sở dữ liệu, thay thế cho việc dùng khóa (lock) để tránh xung đột.
  • 	Nguyên lý: Mỗi khi có giao dịch ghi dữ liệu, hệ thống tạo phiên bản mới của bản ghi thay vì ghi đè lên dữ liệu gốc.
  • 	Lợi ích:
  • 	Người đọc luôn thấy một “ảnh chụp” nhất quán của dữ liệu.
  • 	Người ghi không chặn người đọc và ngược lại.
  • 	Giảm tình trạng “deadlock” (bế tắc giao dịch).

⚙️ Cơ chế hoạt động
  1. 	Phiên bản dữ liệu: Mỗi bản ghi được gắn thông tin về giao dịch đã tạo ra nó.
  2. 	Đọc dữ liệu: Khi một giao dịch đọc, nó chọn phiên bản phù hợp với thời điểm bắt đầu giao dịch.
  3. 	Ghi dữ liệu: Khi ghi, hệ thống tạo một bản ghi mới, giữ nguyên bản gốc cho các giao dịch khác đang đọc.
  4. 	Xóa dữ liệu: Thay vì xóa ngay, MVCC đánh dấu bản ghi là “không hợp lệ” cho các giao dịch mới, nhưng vẫn giữ cho các giao dịch cũ đang đọc.

📊 So sánh MVCC với cơ chế khóa truyền thống

<img width="863" height="437" alt="image" src="https://github.com/user-attachments/assets/c210631a-f560-4d49-ad6d-2cf072149453" />


📌 Ứng dụng thực tế
  • 	PostgreSQL: MVCC là nền tảng chính, giúp hệ thống xử lý hàng nghìn giao dịch đồng thời.
  • 	MySQL (InnoDB): Dùng MVCC để hỗ trợ isolation level như Repeatable Read.
  • 	Oracle Database: Áp dụng MVCC để đảm bảo tính nhất quán dữ liệu trong môi trường doanh nghiệp.

⚠️ Hạn chế cần lưu ý
  • 	Tốn bộ nhớ: Do lưu nhiều phiên bản dữ liệu.
  • 	Cần cơ chế dọn dẹp (VACUUM): Để loại bỏ các bản ghi cũ không còn dùng.
  • 	Phức tạp hơn: So với cơ chế khóa truyền thống, MVCC đòi hỏi quản lý phiên bản tinh vi.

👉 Tóm lại, MVCC là giải pháp hiện đại giúp hệ quản trị cơ sở dữ liệu vừa đảm bảo tính nhất quán, vừa tăng hiệu suất xử lý giao dịch đồng thời. 
