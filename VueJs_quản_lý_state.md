# [TÀI LIỆU] QUẢN LÝ STATE TRONG VUE.JS (2026)

## 1. Tổng quan
Quản lý State (trạng thái) là việc kiểm soát cách dữ liệu được lưu trữ, cập nhật và chia sẻ giữa các Component. Việc chọn đúng phương pháp giúp tránh tình trạng "Prop Drilling" (truyền dữ liệu qua quá nhiều cấp trung gian).

---

## 2. Các cấp độ quản lý State

### A. Hệ thống nhỏ (Simple State Management)
Dành cho dự án ít trang hoặc logic đơn giản.

* **Props & Emits:** Truyền dữ liệu cha - con.
* **Simple Store (Composition API):** Sử dụng `reactive` hoặc `ref` trong một file JS riêng biệt để chia sẻ dữ liệu toàn cục.

**Ví dụ Simple Store:**
```
javascript
// stores/simpleStore.js
import { reactive } from 'vue';

export const globalStore = reactive({
  count: 0,
  increment() {
    this.count++;
  }
});
```

### B. Hệ thống vừa và lớn (Pinia)
Pinia là thư viện quản lý state chính thức cho Vue 3, hỗ trợ tốt cho hệ thống lớn nhờ tính mô-đun và công cụ debug mạnh mẽ.

## 3. Hướng dẫn chi tiết về Pinia
# 3.1. Cấu trúc một Store
1. **State:** Chứa dữ liệu gốc.
2. **Getters:** Dữ liệu được tính toán (giống computed).
3.** Actions:** Hàm xử lý logic và thay đổi state (giống methods).

# 3.2. Ví dụ thực tế: Quản lý Giỏ hàng
**Bước 1: Định nghĩa Store (src/stores/cart.js)**

```
import { defineStore } from 'pinia';

export const useCartStore = defineStore('cart', {
  state: () => ({
    items: [],
    isLoading: false,
  }),

  getters: {
    totalItems: (state) => state.items.length,
    totalPrice: (state) => {
      return state.items.reduce((total, item) => total + item.price, 0);
    },
  },

  actions: {
    addToCart(product) {
      this.items.push(product);
    },
    removeItem(productId) {
      this.items = this.items.filter(p => p.id !== productId);
    },
    async checkout() {
      this.isLoading = true;
      // Giả lập gọi API thanh toán
      await new Promise(resolve => setTimeout(resolve, 1000));
      this.items = [];
      this.isLoading = false;
    }
  }
});
```

**Bước 2: Sử dụng trong Component (App.vue)**

```
<template>
  <div>
    <h3>Giỏ hàng: {{ cart.totalItems }} sản phẩm</h3>
    <p>Tổng tiền: {{ cart.totalPrice }}$</p>
    
    <button @click="addProduct">Thêm Laptop (1000$)</button>
    <button :disabled="cart.isLoading" @click="cart.checkout">
      {{ cart.isLoading ? 'Đang xử lý...' : 'Thanh toán' }}
    </button>
  </div>
</template>

<script setup>
import { useCartStore } from '@/stores/cart';

const cart = useCartStore();

const addProduct = () => {
  cart.addToCart({ id: Date.now(), name: 'Laptop', price: 1000 });
};
</script>
```

## 4. Lưu ý quan trọng (Best Practices)
- Reactivity: Không dùng Destructuring trực tiếp từ store (ví dụ: const { items } = cart) vì sẽ làm mất tính phản xạ. Hãy dùng storeToRefs(cart).
- Mô-đun hóa: Chia nhỏ Store theo chức năng (ví dụ: userStore.js, productStore.js, uiStore.js).
- Logic tập trung: Đưa toàn bộ xử lý API và thay đổi dữ liệu vào Actions để dễ dàng viết Unit Test và tái sử dụng.
