# KIẾN THỨC VỀ TÍNH CHẤT ACID TRONG HỆ QUẢN TRỊ CƠ SỞ DỮ LIỆU (DBMS)

Trong lập trình và hệ quản trị cơ sở dữ liệu (DBMS), ACID là một tập hợp các thuộc tính nhằm đảm bảo các giao dịch (transactions) được xử lý một cách tin cậy.

Nếu bạn đang xây dựng một ứng dụng ngân hàng hay thương mại điện tử, ACID chính là "tấm khiên" bảo vệ dữ liệu của bạn không bị sai lệch khi có lỗi xảy ra.

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

==============================================================================================================================================================================

### Kỹ thuật xử lý tính Isolation (Biệt lập) trong Database

Để đảm bảo các giao dịch không "giẫm chân" lên nhau, chúng ta sử dụng các kỹ thuật sau:

1. **Pessimistic Locking (Khóa bi quan):** - Dùng khi xác suất xung đột cao. 
   - Khóa dữ liệu ngay khi đọc bằng `SELECT ... FOR UPDATE`.

2. **Optimistic Locking (Khóa lạc quan):** - Dùng khi xác suất xung đột thấp. 
   - Sử dụng cột `version` hoặc `timestamp` để kiểm tra trước khi cập nhật.

3. **MVCC (Multi-Version Concurrency Control):** - Tạo ra các bản sao (snapshots) dữ liệu khác nhau cho từng giao dịch.
   - Giúp việc Đọc (Read) và Ghi (Write) không cản trở nhau.

4. **Transaction Isolation Levels:** - Thiết lập các mức độ cô lập: `Read Committed`, `Repeatable Read`, hoặc `Serializable` tùy vào độ quan trọng của dữ liệu.

===========================================================================================================================================================================

## Kỹ thuật xử lý giao dịch trong hệ thống Ngân hàng

Hệ thống ngân hàng ưu tiên **Tính chính xác (Consistency)** và **Tính biệt lập (Isolation)** lên hàng đầu. Các kỹ thuật thường dùng bao gồm:

1. **Pessimistic Locking (Khóa bi quan):** - Sử dụng `SELECT ... FOR UPDATE` để khóa dòng dữ liệu ngay khi bắt đầu giao dịch.
   - Ngăn chặn tất cả các giao dịch khác sửa đổi số dư cho đến khi giao dịch hiện tại kết thúc.

2. **Isolation Level - Serializable/Repeatable Read:** - Cấu hình mức độ cô lập cao nhất để tránh các hiện tượng dữ liệu rác (Dirty Read) hay cập nhật bị mất (Lost Update).

3. **Database Constraints:** - Ràng buộc cứng tại tầng bảng dữ liệu (ví dụ: `CHECK balance >= 0`) để làm "chốt chặn cuối cùng".

4. **Idempotency (Tính giao hoán):** - Sử dụng `Transaction ID` duy nhất cho mỗi lệnh chuyển tiền để đảm bảo nếu người dùng nhấn nút "Gửi" hai lần, hệ thống cũng chỉ thực hiện trừ tiền một lần duy nhất.

========================================================================================================================================================================

## Kỹ thuật đảm bảo tính Durability (Bền vững)

Để dữ liệu không bị mất khi Server hỏng hoặc mất điện đột ngột, các hệ thống sử dụng:
 - Thay vì ghi trực tiếp dữ liệu mới vào các tệp dữ liệu chính (thao tác này rất chậm vì phải tìm đúng chỗ trên ổ cứng), hệ thống thực hiện các bước sau:

1. **Write-Ahead Logging (WAL):** - Ghi mọi thay đổi vào tệp nhật ký (Transaction Log) trên ổ cứng trước khi cập nhật vào Database chính.
   - Khi hệ thống khởi động lại sau sự cố, nó sẽ dựa vào Log này để "diễn" lại (Redo) các giao dịch đã thành công.

2. **Fsync (File Synchronization):**
   - Một lệnh hệ thống bắt buộc hệ điều hành đẩy dữ liệu từ bộ nhớ đệm (Cache) xuống thiết bị lưu trữ vật lý ngay lập tức, không được giữ lại trong RAM.
3. **Báo thành công:**
   - Chỉ sau khi Log đã nằm an toàn trên đĩa, hệ thống mới báo cho người dùng là "Giao dịch thành công".
4. **Checkpointing:**
   - Sau đó, dữ liệu thật sự mới được cập nhật từ Log vào các tệp dữ liệu chính một cách thong thả.

5. **Phần cứng hỗ trợ:**
   - **UPS:** Bộ lưu điện dự phòng.
   - **NVRAM/BBU:** Bộ nhớ đệm có pin dự phòng trên các dòng Server cao cấp.
   - **Database Replication (Sao chép):** Dữ liệu được ghi đồng thời sang một server khác (Standby server). Nếu server chính "die" vật lý (cháy nổ), server kia vẫn còn bản sao.
