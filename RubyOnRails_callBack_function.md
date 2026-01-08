# Rails xử lý callbacks như thế nào
- Callbacks là các “hook” (móc nối) vào vòng đời của một đối tượng Active Record.
- Rails cho phép bạn chạy đoạn code tùy chỉnh trước, sau hoặc xung quanh các sự kiện như: lưu (save), tạo (create), cập nhật (update), hoặc xóa (destroy).
## - Ví dụ:
- before_save: chạy trước khi bản ghi được lưu (bao gồm cả tạo mới và cập nhật).
- after_create: chạy sau khi một bản ghi mới được chèn vào cơ sở dữ liệu.
Rails thực hiện điều này bằng cách duy trì một chuỗi callback. Khi bạn gọi save hoặc create, Rails sẽ lần lượt chạy các callback “before”, sau đó thực hiện hành động với DB, rồi chạy các callback “after”.
## ⚖️ Lưu ý khi dùng
- ✅ Callback phù hợp cho các logic liên quan trực tiếp đến dữ liệu (như kiểm tra, chuẩn hóa).
- ⚠️ Không nên nhồi quá nhiều logic nghiệp vụ phức tạp vào callback, vì sẽ khó kiểm thử và dễ gây rối.
- Với các quy trình phức tạp, hãy cân nhắc dùng service object hoặc ActiveJob thay vì callback.
