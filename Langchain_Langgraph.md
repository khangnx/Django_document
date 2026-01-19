# 📘 Tài liệu về AI Agent Framework: LangChain vs LangGraph

## 🔹 LangChain
- **Mục tiêu:** Giúp tạo AI Agent nhanh chóng với kiến trúc có sẵn.  
- **Đặc điểm nổi bật:**
  - Có **template** cho các use case phổ biến (chatbot, trợ lý tìm kiếm).  
  - **Tích hợp hơn 1000 công cụ** (database, API, mô hình AI).  
  - **Durable runtime**: hỗ trợ checkpoint, rewind, persistence (giữ trạng thái).  
  - **Human-in-the-loop**: có thể yêu cầu người dùng duyệt trước khi agent thực hiện hành động.  
- **Ứng dụng:** Phù hợp cho startup, doanh nghiệp muốn triển khai nhanh sản phẩm AI.

---

## 🔹 LangGraph
- **Mục tiêu:** Cho phép kiểm soát chi tiết cách agent hoạt động và phối hợp nhiều agent.  
- **Đặc điểm nổi bật:**
  - Là **low-level orchestration framework** → bạn tự thiết kế luồng hoạt động.  
  - Hỗ trợ **multi-agent workflows** (nhiều agent cùng hợp tác).  
  - Có **memory, planning, sub-agents** để xử lý tác vụ dài hạn, phức tạp.  
  - Cho phép **streaming intermediate steps** (xem từng bước agent xử lý).  
- **Ứng dụng:** Dành cho kỹ sư AI muốn xây dựng hệ thống agent **tùy chỉnh sâu**.

---

## 📊 Bảng so sánh nhanh

| Tiêu chí              | LangChain (cao cấp) | LangGraph (thấp cấp) |
|-----------------------|---------------------|-----------------------|
| **Mức độ trừu tượng** | Cao – có sẵn kiến trúc | Thấp – bạn tự thiết kế |
| **Tốc độ triển khai** | Nhanh, dễ dùng       | Chậm hơn, cần kỹ thuật |
| **Tích hợp công cụ**  | >1000 tích hợp sẵn   | Tích hợp thủ công      |
| **Kiểm soát chi tiết**| Hạn chế              | Rất chi tiết, linh hoạt|
| **Ứng dụng phù hợp**  | Chatbot, trợ lý, demo nhanh | Hệ thống phức tạp, dài hạn |

---

## 🎯 Cách trả lời khi phỏng vấn
Nếu được hỏi:  
- **LangChain:** “Đây là framework giúp xây dựng AI Agent nhanh chóng, có nhiều tích hợp sẵn, phù hợp cho sản phẩm cần ra mắt nhanh.”  
- **LangGraph:** “Đây là framework thấp cấp hơn, cho phép kiểm soát chi tiết cách agent hoạt động, phù hợp cho các hệ thống phức tạp và dài hạn.”  

👉 Thêm ý: “Thực tế, nhiều công ty dùng **LangChain để khởi đầu**, sau đó dùng **LangGraph để tối ưu và mở rộng** khi hệ thống lớn dần.”
