## 🔎 So sánh `v-if` và `v-show` trong Vue.js

Trong Vue.js, cả hai directive này đều dùng để điều khiển hiển thị phần tử, nhưng cách hoạt động và tình huống sử dụng lại khác nhau.

## ⚙️ Cách hoạt động

### `v-if`

* Thêm hoặc xóa phần tử khỏi DOM dựa trên điều kiện.
* Nếu điều kiện là `false`, phần tử **không tồn tại trong DOM**.
* Tốn chi phí render lại khi điều kiện thay đổi.

**Ví dụ:**
`<p v-if="isLoggedIn">Xin chào!</p>`
`<p v-else>Vui lòng đăng nhập</p>`

### `v-show`

* Không xóa phần tử khỏi DOM, chỉ thay đổi CSS `display`.
* Phần tử luôn tồn tại trong DOM, nhưng có thể bị ẩn.
* Chuyển đổi nhanh, ít tốn chi phí khi điều kiện thay đổi thường xuyên.

**Ví dụ:**
`<p v-show="isVisible">Nội dung hiển thị</p>`

## 📊 Bảng so sánh

| Đặc điểm       | `v-if`                                 | `v-show`                      |
| -------------- | -------------------------------------- | ----------------------------- |
| Cách hoạt động | Thêm/xóa phần tử khỏi DOM              | Thay đổi CSS `display`        |
| Hiệu năng      | Tốt khi điều kiện ít thay đổi          | Tốt khi thay đổi thường xuyên |
| DOM            | Có thể không tồn tại                   | Luôn tồn tại trong DOM        |
| Sử dụng        | Render có điều kiện, tối ưu tài nguyên | Toggle hiển thị nhanh chóng   |

## 👉 Kết luận

* Dùng `v-if` khi điều kiện hiển thị **ít thay đổi**.
* Dùng `v-show` khi cần **ẩn/hiện thường xuyên**.
