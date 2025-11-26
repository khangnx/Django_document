# 1. Công cụ quản lý chi phí dự án phần mềm

| Công cụ                     | Mục đích                                                | Ghi chú                                                              |
| --------------------------- | ------------------------------------------------------- | -------------------------------------------------------------------- |
| **Microsoft Project**       | Lập kế hoạch, theo dõi tiến độ, tính toán chi phí dự án | Có thể tính tổng chi phí dựa trên nhân lực, thời gian, và tài nguyên |
| **Jira + Tempo Timesheets** | Quản lý công việc Agile, tính giờ nhân sự               | Tempo Timesheets giúp chuyển giờ làm việc thành chi phí dự án        |
| **ClickUp**                 | Quản lý công việc, theo dõi tiến độ và chi phí          | Có tính năng theo dõi thời gian và chi phí dựa trên công việc        |
| **Smartsheet**              | Quản lý dự án theo bảng tính + Gantt chart              | Có thể tính tổng chi phí dựa trên công thức và thời gian thực hiện   |
| **Asana + Harvest**         | Quản lý nhiệm vụ + tính thời gian & chi phí             | Harvest giúp quy đổi giờ làm việc thành chi phí nhân sự              |
| **Excel/Google Sheets**     | Lập bảng tính chi phí thủ công                          | Linh hoạt, dễ tùy chỉnh, dùng khi dự án nhỏ                          |

# 2. Các yếu tố cần tính khi dự án phần mềm

## Nhân lực

- Số lượng lập trình viên, tester, PM, designer…

- Lương/giá giờ làm việc

- Thời gian dự kiến hoàn thành mỗi task

## Công cụ và tài nguyên

- License phần mềm

- Cloud hosting, server, domain

- Thiết bị (PC, laptop, thiết bị test)

## Chi phí vận hành

- Văn phòng, điện, internet, tiện ích hỗ trợ

## Chi phí dự phòng

- Rủi ro phát sinh

- Buffer 10–20% tổng ngân sách
# 3. Phương pháp tính chi phí phổ biến

## Tính theo giờ (Time & Material)

 ```Tổng chi phí = (Giá giờ nhân sự) × (Số giờ dự kiến)```


## Tính theo gói cố định (Fixed Price)

- Xác định chi phí dự án tổng thể dựa trên phạm vi công việc

- Thường dùng cho khách hàng ngoài

## Kết hợp Agile + velocity

- Dự tính chi phí dựa trên velocity (số story points hoàn thành / sprint)

```Chi phí = (Số story points) × (Chi phí trung bình/point)```


# Bảng tính mẩu trong excel

Dưới đây là cấu trúc file Excel/Google Sheet mẫu để tính chi phí dự án phần mềm. Bạn có thể copy vào Excel hoặc Google Sheet và sử dụng công thức trực tiếp.

---

# Sheet 1: Nhân sự

| Tên               | Vai trò   | Số giờ dự kiến | Giá/giờ (USD) | Chi phí (USD) |
| ----------------- | --------- | -------------- | ------------- | ------------- |
| John Doe          | Developer | 120            | 25            | =C2*D2        |
| Jane Smith        | Tester    | 80             | 20            | =C3*D3        |
| Alice             | PM        | 60             | 35            | =C4*D4        |
| **Tổng nhân lực** |           |                |               | =SUM(E2:E4)   |

# Sheet 2: Công cụ & Tài nguyên

| Tài nguyên       | Mô tả          | Số lượng | Giá/đơn vị (USD) | Chi phí (USD) |
| ---------------- | -------------- | -------- | ---------------- | ------------- |
| Jira License     | Quản lý dự án  | 3        | 10               | =C2*D2        |
| Server AWS       | Hosting        | 1        | 200              | =C3*D3        |
| Thiết bị test    | Laptop + phone | 5        | 500              | =C4*D4        |
| **Tổng công cụ** |                |          |                  | =SUM(E2:E4)   |

# Sheet 3: Chi phí vận hành

| Loại chi phí      | Mô tả                  | Giá (USD)   |
| ----------------- | ---------------------- | ----------- |
| Văn phòng         | Thuê văn phòng 1 tháng | 300         |
| Internet          | 1 tháng                | 50          |
| Chi phí khác      | Điện, tiện ích         | 50          |
| **Tổng vận hành** |                        | =SUM(C2:C4) |

# Sheet 4: Tổng chi phí

| Hạng mục             | Chi phí (USD) |
| -------------------- | ------------- |
| Nhân lực             | =Sheet1!E5    |
| Công cụ & Tài nguyên | =Sheet2!E5    |
| Vận hành             | =Sheet3!C5    |
| **Tổng dự án**       | =SUM(B2:B4)   |

---

