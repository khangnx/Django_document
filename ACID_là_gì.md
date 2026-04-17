# KIẾN THỨC VỀ TÍNH CHẤT ACID TRONG HỆ QUẢN TRỊ CƠ SỞ DỮ LIỆU (DBMS)

**ACID** là tập hợp các thuộc tính đảm bảo các giao dịch (transactions) trong cơ sở dữ liệu được thực hiện một cách tin cậy, đặc biệt là trong các trường hợp có lỗi xảy ra hoặc có nhiều giao dịch chạy cùng lúc.

---

## 1. A - Atomicity (Tính nguyên tử)
* **Định nghĩa:** Đảm bảo một giao dịch phải được thực hiện "Tất cả hoặc không có gì".
* **Chi tiết:** Một giao dịch thường bao gồm nhiều bước. Nếu bất kỳ bước nào thất bại, toàn bộ giao dịch đó sẽ bị hủy bỏ (Rollback) và dữ liệu quay lại trạng thái ban đầu trước khi giao dịch bắt đầu.
* **Ví dụ:** Trong giao dịch chuyển tiền ngân hàng:
    1. Trừ tiền tài khoản A.
    2. Cộng tiền tài khoản B.
    *Nếu bước 2 lỗi, bước 1 phải được khôi phục lại.*

## 2. C - Consistency (Tính nhất quán)
* **Định nghĩa:** Đảm bảo cơ sở dữ liệu luôn ở trạng thái hợp lệ trước và sau khi giao dịch kết thúc.
* **Chi tiết:** Mọi thay đổi dữ liệu phải tuân thủ đúng các quy tắc (constraints), khóa ngoại (foreign keys), và các ràng buộc dữ liệu khác đã được thiết lập.
* **Ví dụ:** Nếu một tài khoản có ràng buộc "Số dư không được nhỏ hơn 0", thì bất kỳ giao dịch nào khiến số dư bị âm sẽ bị hệ thống từ chối để giữ tính nhất quán.

## 3. I - Isolation (Tính biệt lập)
* **Định nghĩa:** Đảm bảo các giao dịch thực hiện đồng thời không làm ảnh hưởng đến nhau.
* **Chi tiết:** Mỗi giao dịch phải được thực hiện như thể nó là giao dịch duy nhất trong hệ thống. Kết quả của các giao dịch chạy song song phải giống hệt như khi chúng chạy tuần tự (cái này xong mới đến cái kia).
* **Ví dụ:** Hai người cùng truy cập vào một tài khoản và rút tiền cùng lúc. Hệ thống phải xử lý cách ly để đảm bảo số dư được cập nhật chính xác cho từng người, tránh tình trạng cả hai cùng rút được số tiền lớn hơn số dư hiện có.

## 4. D - Durability (Tính bền vững)
* **Định nghĩa:** Đảm bảo dữ liệu đã được xác nhận (Commit) sẽ được lưu trữ vĩnh viễn.
* **Chi tiết:** Khi hệ thống báo giao dịch thành công, dữ liệu phải được ghi xuống bộ nhớ vật lý (như ổ cứng). Ngay cả khi máy chủ bị sập nguồn hoặc lỗi phần mềm ngay sau đó, dữ liệu cũng không bị mất.
* **Ví dụ:** Sau khi ứng dụng báo "Thanh toán thành công", thông tin đơn hàng phải tồn tại trong hệ thống dù server có bị khởi động lại ngay lúc đó.

---

## Tóm tắt nhanh

| Thuộc tính | Tên tiếng Anh | Quy tắc cốt lõi |
| :--- | :--- | :--- |
| **A** | Atomicity | Làm tất cả hoặc không làm gì. |
| **C** | Consistency | Không vi phạm quy tắc dữ liệu. |
| **I** | Isolation | Không "giẫm chân" lên giao dịch khác. |
| **D** | Durability | Lưu vĩnh viễn, không sợ mất điện/hỏng hóc. |

---

## Tại sao lập trình viên cần ACID?
1. **An toàn dữ liệu:** Tránh tình trạng dữ liệu rác hoặc dữ liệu sai lệch do lỗi hệ thống.
2. **Tin cậy:** Đặc biệt quan trọng trong các lĩnh vực Tài chính, Ngân hàng, Y tế, và E-commerce.
3. **Đơn giản hóa logic:** Lập trình viên có thể tập trung vào logic nghiệp vụ, việc đảm bảo an toàn dữ liệu đã có DBMS (như MySQL, PostgreSQL, SQL Server) lo.
