# 🎯 Khái niệm Vue.js Slots

Trong Vue.js, **slot** là cơ chế cho phép bạn truyền nội dung từ component cha vào component con.  
Nó giống như một “placeholder” trong template của component con, nơi mà nội dung từ cha sẽ được chèn vào.  

👉 Điều này giúp tạo ra các component **linh hoạt** và **tái sử dụng** dễ dàng hơn.

---

## 🔑 Cách hoạt động

- Component con định nghĩa một `<slot>` trong template  
- Component cha khi sử dụng có thể truyền nội dung vào vị trí đó  
- Nếu cha không truyền nội dung, slot có thể hiển thị **fallback content** (nội dung mặc định)

### Ví dụ cơ bản

```vue
<!-- Component cha -->
<FancyButton>Nhấn vào tôi!</FancyButton>

<!-- Component con (FancyButton.vue) -->
<template>
  <button class="fancy-btn">
    <slot></slot> <!-- nơi nội dung cha được chèn vào -->
  </button>
</template>
```

👉 Kết quả:

```<button class="fancy-btn">Nhấn vào tôi!</button>```

### 📂 Các loại Slot
1. Default Slot

Dùng khi chỉ có một slot duy nhất.
```
<AlertBox>
  <p>Đây là thông báo cảnh báo!</p>
</AlertBox>
```
2. Named Slots

Cho phép định nghĩa nhiều slot với tên khác nhau.
```
<!-- Component con -->
<template>
  <header><slot name="header"></slot></header>
  <main><slot></slot></main>
  <footer><slot name="footer"></slot></footer>
</template>

<!-- Component cha -->
<MyLayout>
  <template v-slot:header>Phần Header</template>
  <p>Nội dung chính</p>
  <template v-slot:footer>Phần Footer</template>
</MyLayout>
```
3. Scoped Slots

Cho phép component con truyền dữ liệu ra ngoài để cha sử dụng.
```
<!-- Component con -->
<template>
  <ul>
    <slot v-for="item in items" :item="item"></slot>
  </ul>
</template>

<!-- Component cha -->
<ItemList :items="products">
  <template v-slot:default="{ item }">
    <li>{{ item.name }} - {{ item.price }}</li>
  </template>
</ItemList>
```
## 🛠️ Fallback Content

Nếu cha không truyền nội dung, slot sẽ hiển thị nội dung mặc định.

```
<button>
  <slot>Gửi</slot>
</button>
```
Nếu cha không truyền gì:

```<SubmitButton></SubmitButton>```

👉 Kết quả:

```<button>Gửi</button>```

## 📊 Lợi ích khi dùng Slots
Tính năng	Lợi ích
Linh hoạt	Cha kiểm soát nội dung, con kiểm soát bố cục
Tái sử dụng	Một component dùng cho nhiều trường hợp
Tách biệt	Con định nghĩa layout, cha định nghĩa dữ liệu
Scoped Slots	Truyền dữ liệu từ con ra cha để render động
👉 Tóm lại
Slots trong Vue.js là công cụ mạnh mẽ để xây dựng component linh hoạt và tái sử dụng
Dùng default slot cho nội dung đơn giản
Dùng named slot cho bố cục phức tạp
Dùng scoped slot khi cần truyền dữ liệu từ con ra cha
