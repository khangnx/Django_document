
# Tối ưu hiệu năng trong Vue.js

## 🔹 1. Chia nhỏ ứng dụng thành **component tái sử dụng**
- Tách ứng dụng thành nhiều component nhỏ, mỗi component đảm nhận một chức năng riêng.
- Lợi ích:
  - Dễ quản lý và bảo trì.
  - Vue chỉ cần re-render những component thay đổi, không phải toàn bộ ứng dụng.
- **Ví dụ**: `ProductList`, `ProductItem`, `Cart`, `CheckoutForm`.

---

## 🔹 2. **Lazy loading** cho component không cần thiết ngay
- Chỉ tải component khi người dùng thực sự cần.
- Giúp giảm dung lượng ban đầu khi tải trang, tăng tốc độ hiển thị.
- **Ví dụ**:

```js
import { defineAsyncComponent } from 'vue'

const AdminPanel = defineAsyncComponent(() =>
  import('./components/AdminPanel.vue')
)

```

## 🔹 3. Dùng Vue Devtools để phân tích hiệu năng
- Vue Devtools cho phép bạn:
- Xem cấu trúc component.
- Theo dõi thời gian render.
- Phát hiện component nào render lại quá nhiều lần.
- Nhờ đó bạn biết chính xác “nút thắt cổ chai” nằm ở đâu để tối ưu.

## 🔹 4. Sử dụng computed properties hiệu quả
- computed giúp tính toán giá trị dựa trên dữ liệu gốc và cache kết quả.
- Khác với methods, computed chỉ chạy lại khi dữ liệu phụ thuộc thay đổi → tránh tính toán lại không cần thiết.
- Ví dụ:
```
computed: {
  fullName() {
    return `${this.firstName} ${this.lastName}`
  }
}
```

Nếu firstName và lastName không đổi, fullName sẽ không bị tính lại mỗi lần render.

## 🔹 5. Tránh re-render không cần thiết
- Một số cách:
- Dùng v-once để render tĩnh (chỉ render 1 lần).
- Dùng key hợp lý để Vue nhận diện component đúng cách.
- Tránh thay đổi state không cần thiết.
- Ví dụ:
```
<p v-once>Đây là nội dung tĩnh, không bao giờ re-render</p>

```

## 👉 Tóm lại:
- Chia nhỏ component → dễ quản lý, ít re-render.
- Lazy loading → giảm tải ban đầu.
- Vue Devtools → phát hiện bottleneck.
- Computed properties → cache thông minh.
- Tránh re-render thừa → giữ UI mượt mà.
