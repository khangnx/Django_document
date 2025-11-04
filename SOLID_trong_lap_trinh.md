# SOLID là một tập hợp 5 nguyên tắc thiết kế hướng đối tượng giúp mã nguồn dễ bảo trì, mở rộng và giảm độ phức tạp. Đây là tài liệu tóm tắt chi tiết:

## 1. S – Single Responsibility Principle (Nguyên tắc trách nhiệm duy nhất)

Ý nghĩa: Mỗi lớp chỉ nên có một lý do để thay đổi, tức là đảm nhận một trách nhiệm duy nhất.
Lợi ích: Giúp mã dễ hiểu, dễ bảo trì, tránh việc một thay đổi ảnh hưởng đến nhiều chức năng.
Ví dụ:

Sai: Một lớp Report vừa tạo báo cáo, vừa lưu file, vừa gửi email.
Đúng: Tách thành ReportGenerator, FileSaver, EmailSender.




## 2. O – Open/Closed Principle (Nguyên tắc mở/đóng)

Ý nghĩa: Mở để mở rộng, đóng để sửa đổi. Bạn có thể thêm chức năng mới mà không cần chỉnh sửa mã hiện có.
Lợi ích: Giảm rủi ro khi thay đổi, dễ mở rộng hệ thống.
Ví dụ:

Dùng interface hoặc abstract class để thêm tính năng mới mà không sửa lớp cũ.




## 3. L – Liskov Substitution Principle (Nguyên tắc thay thế Liskov)

Ý nghĩa: Đối tượng của lớp con có thể thay thế đối tượng của lớp cha mà không làm thay đổi tính đúng đắn của chương trình.
Lợi ích: Đảm bảo tính kế thừa hợp lý.
Ví dụ:

Nếu Bird có phương thức fly(), thì lớp con Penguin không nên kế thừa Bird vì chim cánh cụt không bay.




## 4. I – Interface Segregation Principle (Nguyên tắc phân tách interface)

Ý nghĩa: Không ép buộc lớp triển khai những phương thức không dùng.
Lợi ích: Giảm sự phụ thuộc không cần thiết.
Ví dụ:

Sai: Interface IMachine có print(), scan(), fax().
Đúng: Tách thành IPrinter, IScanner, IFax.




## 5. D – Dependency Inversion Principle (Nguyên tắc đảo ngược phụ thuộc)

Ý nghĩa: Lớp cấp cao không nên phụ thuộc trực tiếp vào lớp cấp thấp, mà phụ thuộc vào abstraction.
Lợi ích: Giảm sự phụ thuộc chặt chẽ, dễ thay đổi và test.
Ví dụ:

Thay vì OrderService gọi trực tiếp MySQLDatabase, hãy dùng IDatabase interface.
