# 🔄 Hai chiều dữ liệu trong Vue.js và cách dùng v-model

## 📌 Khái niệm Two-Way Data Binding
Vue.js hỗ trợ ràng buộc dữ liệu hai chiều (two-way data binding), nghĩa là dữ liệu trong component và giá trị trong DOM (form input) luôn đồng bộ với nhau. Khi người dùng nhập dữ liệu vào form, state (data) sẽ tự động cập nhật. Ngược lại, khi state thay đổi, form cũng hiển thị giá trị mới ngay lập tức.

## ⚙️ Cách hoạt động của v-model
v-model là directive đặc biệt trong Vue.js để tạo liên kết hai chiều giữa dữ liệu và form input. Nó kết hợp:
- v-bind để gán giá trị từ state vào input
- v-on để lắng nghe sự kiện (input, change) và cập nhật ngược lại vào state

## 🧪 Ví dụ cơ bản
<template>
  <input v-model="username" />
  <p>Xin chào, {{ username }}</p>
</template>

<script>
export default {
  data() {
    return {
      username: ''
    }
  }
}
</script>

👉 Khi người dùng gõ vào ô input, biến username sẽ thay đổi và nội dung hiển thị cũng được cập nhật ngay lập tức.

## 📂 Ứng dụng trong Form

1. Input text
<input v-model="message" placeholder="Nhập tin nhắn" />
<p>{{ message }}</p>

2. Checkbox
<input type="checkbox" v-model="isChecked" />
<p>Trạng thái: {{ isChecked }}</p>

3. Radio
<input type="radio" value="Nam" v-model="gender" /> Nam
<input type="radio" value="Nữ" v-model="gender" /> Nữ
<p>Giới tính: {{ gender }}</p>

4. Select
<select v-model="selected">
  <option disabled value="">Chọn một mục</option>
  <option>A</option>
  <option>B</option>
</select>
<p>Bạn chọn: {{ selected }}</p>

## 🛠️ Tùy chỉnh với v-model (Custom Component)

<!-- Component con -->
<template>
  <input :value="modelValue" @input="$emit('update:modelValue', $event.target.value)" />
</template>

<script>
export default {
  props: ['modelValue']
}
</script>

<!-- Component cha -->
<MyInput v-model="username" />

## 📊 Tóm lại
- v-model là công cụ chính để thực hiện two-way data binding trong Vue.js.
- Giúp dữ liệu và UI luôn đồng bộ.
- Áp dụng cho nhiều loại input: text, checkbox, radio, select và cả component tùy chỉnh.
