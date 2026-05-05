## 🔎 So sánh Computed Properties và Methods trong Vue.js

Trong Vue.js, cả **computed properties** và **methods** đều có thể dùng để xử lý logic và hiển thị dữ liệu trong template. Tuy nhiên, chúng khác nhau về cách hoạt động, hiệu năng và tính phản ứng (reactivity).

---

## 🔑 Computed Properties

### Đặc điểm:
- Là các thuộc tính được tính toán dựa trên dữ liệu (`data`) hoặc các thuộc tính khác.
- Có **cache (bộ nhớ đệm)**: chỉ tính lại khi dữ liệu phụ thuộc thay đổi.
- Thích hợp cho các phép tính phức tạp hoặc tốn kém, nhưng không cần tính lại liên tục.

### Ví dụ:
```vue
<template>
  <p>Họ tên: {{ fullName }}</p>
</template>

<script>
export default {
  data() {
    return {
      firstName: 'Nguyễn',
      lastName: 'An'
    }
  },
  computed: {
    fullName() {
      console.log('Computed chạy lại')
      return this.firstName + ' ' + this.lastName
    }
  }
}
</script>
```

👉 `fullName` chỉ được tính lại khi `firstName` hoặc `lastName` thay đổi.

---

## 🔧 Methods

### Đặc điểm:
- Là các hàm được gọi trực tiếp trong template.
- **Không có cache**: mỗi lần render lại, method sẽ được gọi lại, dù dữ liệu không thay đổi.
- Thích hợp cho các hành động hoặc logic không cần lưu kết quả.

### Ví dụ:
```vue
<template>
  <p>Họ tên: {{ getFullName() }}</p>
</template>

<script>
export default {
  data() {
    return {
      firstName: 'Nguyễn',
      lastName: 'An'
    }
  },
  methods: {
    getFullName() {
      console.log('Method chạy lại')
      return this.firstName + ' ' + this.lastName
    }
  }
}
</script>
```

👉 `getFullName()` sẽ chạy lại mỗi lần component re-render, ngay cả khi dữ liệu không đổi.

---

## 📊 Bảng so sánh

| Tiêu chí           | Computed Properties                                      | Methods                                              |
|-------------------|----------------------------------------------------------|------------------------------------------------------|
| Cache             | Có cache, chỉ tính lại khi dữ liệu phụ thuộc thay đổi     | Không cache, luôn chạy lại khi render               |
| Hiệu năng         | Tối ưu hơn cho logic phức tạp                             | Ít tối ưu, có thể gây lãng phí nếu gọi nhiều lần     |
| Tính phản ứng     | Tự động cập nhật khi dữ liệu thay đổi                     | Chỉ chạy khi được gọi, không tự động phản ứng        |
| Khi nên dùng      | Khi cần hiển thị dữ liệu tính toán dựa trên state         | Khi cần thực hiện hành động hoặc logic tức thời      |

---

## 👉 Kết luận

- Dùng **computed properties** khi muốn hiển thị dữ liệu tính toán và cần tối ưu hiệu năng nhờ cache.
- Dùng **methods** khi cần thực hiện hành động hoặc logic mà không cần lưu kết quả.
