<img width="947" height="227" alt="image" src="https://github.com/user-attachments/assets/86a67a52-f572-4756-8990-8296686a5b4f" />

# So sánh các thuật toán Load Balancer

| Thuật toán             | Cách hoạt động                                                                 | Ưu điểm                                                                 | Nhược điểm                                                                 |
|-------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------|----------------------------------------------------------------------------|
| **Round Robin**         | Phân phối lần lượt từng request đến các server theo vòng tròn.                | - Đơn giản, dễ triển khai.<br>- Phù hợp khi các server có cấu hình tương tự. | - Không xét đến tải thực tế.<br>- Có thể gây quá tải nếu request không đồng đều. |
| **Least Connections**   | Request mới được gửi đến server có số lượng kết nối hoạt động ít nhất.        | - Cân bằng tốt khi tải không đều.<br>- Giảm nguy cơ một server bị quá tải. | - Cần theo dõi trạng thái kết nối liên tục.<br>- Tốn thêm tài nguyên để quản lý. |
| **IP Hash**             | Dùng hàm băm trên địa chỉ IP của client để quyết định server nhận request.    | - Giữ session ổn định cho cùng một client.<br>- Hữu ích cho ứng dụng cần "sticky session". | - Không cân bằng tải tốt.<br>- Nếu nhiều client cùng subnet, có thể gây lệch tải. |
| **Weighted Round Robin**| Giống Round Robin nhưng gán trọng số cho từng server (server mạnh nhận nhiều request hơn). | - Tận dụng tối đa tài nguyên của server mạnh.<br>- Linh hoạt khi hệ thống có server không đồng đều. | - Cần cấu hình trọng số chính xác.<br>- Nếu sai trọng số, dễ gây mất cân bằng. |

<img width="921" height="227" alt="image" src="https://github.com/user-attachments/assets/11ba8b75-cc4c-4d6a-8c14-804ddfad98a4" />

# Sơ đồ trực quan

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/e4393b77-d480-49a0-8f78-b779c7b5b7b2" />
<img width="1000" height="650" alt="image" src="https://github.com/user-attachments/assets/d6b6fb05-513d-4aab-9034-21422c6d0583" />
<img width="970" height="712" alt="image" src="https://github.com/user-attachments/assets/b86f73d8-6ee2-4833-b85b-79c8a9c56237" />




