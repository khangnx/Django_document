# 🟢 Vue.js Directives: Hướng dẫn Toàn tập

Trong Vue.js, **directives** là các thuộc tính đặc biệt bắt đầu bằng tiền tố `v-`. Chúng được sử dụng để gắn các hành vi hoặc logic xử lý trực tiếp vào phần tử DOM, giúp template ngắn gọn, dễ đọc và tự động phản ứng khi dữ liệu thay đổi.

---

## 📌 Khái niệm Directive

Directive là một "token" đặc biệt trong HTML mà Vue sẽ nhận diện để thao tác với DOM.

**Cú pháp:**

```html
<element v-directive="expression">
```

**Ví dụ:**

```html
<p v-text="message"></p>
```

*(hiển thị nội dung của biến `message`)*

---

## ⚙️ Các Directive Thường Dùng

### 1. `v-text`

Cập nhật nội dung văn bản (`textContent`) của phần tử.

```html
<span v-text="msg"></span>

<!-- Tương đương với -->
<span>{{ msg }}</span>
```

---

### 2. `v-html`

Dùng để chèn nội dung HTML thô vào phần tử.

```html
<div v-html="rawHtml"></div>
```

⚠️ **Cảnh báo bảo mật:** Chỉ sử dụng với nội dung tin cậy để tránh tấn công XSS.

---

### 3. `v-show`

Ẩn hoặc hiện phần tử bằng cách thay đổi thuộc tính CSS `display`.

```html
<p v-show="isVisible">Xin chào!</p>
```

---

### 4. `v-if / v-else / v-else-if`

Dùng để render phần tử theo điều kiện.
Khác với `v-show`, `v-if` sẽ thực sự thêm hoặc loại bỏ phần tử khỏi cấu trúc DOM.

```html
<p v-if="isLoggedIn">Chào bạn!</p>
<p v-else>Vui lòng đăng nhập</p>
```

---

### 5. `v-for`

Dùng để lặp qua một mảng hoặc object để hiển thị danh sách.

```html
<li v-for="item in items" :key="item.id">
  {{ item.name }}
</li>
```

---

### 6. `v-bind`

Ràng buộc (bind) các thuộc tính HTML với dữ liệu trong Vue.

```html
<img v-bind:src="imageUrl" />

<!-- Cách viết rút gọn -->
<img :src="imageUrl" />
```

---

### 7. `v-on`

Dùng để lắng nghe các sự kiện DOM (click, submit, input...).

```html
<button v-on:click="increment">Tăng</button>

<!-- Cách viết rút gọn -->
<button @click="increment">Tăng</button>
```

---

### 8. `v-model`

Tạo ràng buộc dữ liệu hai chiều (**two-way binding**) giữa input và dữ liệu ứng dụng.

```html
<input v-model="username" />
<p>Xin chào, {{ username }}</p>
```

---

## 🛠️ Custom Directives (Directive Tùy Chỉnh)

Ngoài các directive mặc định, bạn có thể tự tạo directive riêng để thao tác trực tiếp với DOM.

**Ví dụ:** Tạo directive `v-focus` để tự động focus vào input khi trang tải xong.

```js
// Vue 3 (Composition API - <script setup>)
const vFocus = {
  mounted: (el) => el.focus()
}
```

**Sử dụng trong template:**

```html
<input v-focus />
```

---

## 📊 So sánh: `v-show` vs `v-if`

| Directive | Cách hoạt động                     | Trường hợp sử dụng                              |
| --------- | ---------------------------------- | ----------------------------------------------- |
| `v-show`  | Thay đổi CSS `display: none/block` | Khi cần ẩn/hiện thường xuyên (tối ưu hiệu năng) |
| `v-if`    | Thêm/xóa phần tử khỏi DOM          | Khi điều kiện ít thay đổi (giảm tải ban đầu)    |

---

## 💡 Tóm lại

Vue Directives là công cụ mạnh mẽ để kết nối logic JavaScript với giao diện người dùng.

Việc nắm vững các directive quan trọng như:

* `v-if`
* `v-for`
* `v-bind`
* `v-on`
* `v-model`

là nền tảng cốt yếu để xây dựng ứng dụng Vue.js hiện đại.
