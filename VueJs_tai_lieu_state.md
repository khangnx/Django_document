
# Tài liệu về State trong Vue và Quản lý State

## 1. State là gì trong Vue?
- State là dữ liệu (data) mà component hoặc ứng dụng Vue quản lý và hiển thị.
- Nó đại diện cho trạng thái hiện tại của giao diện người dùng.
- Khi state thay đổi, Vue sẽ tự động cập nhật DOM nhờ cơ chế reactivity.

## 2. Các cách quản lý state trong Vue
### a. State trong Component (Local State)
- Được khai báo trong data() của component.
Ví dụ:
```
<template>
  <div>
    <p>{{ count }}</p>
    <button @click="increment">Tăng</button>
  </div>
</template>
<script>
export default {
  data() {
    return {
      count: 0
    };
  },
  methods: {
    increment() {
      this.count++;
    }
  }
};
</script>
```
### b. State chia sẻ giữa các component
- Dùng props để truyền dữ liệu từ cha xuống con.
- Dùng emit để gửi sự kiện từ con lên cha.

### c. State toàn cục (Global State)
- Khi ứng dụng lớn, cần quản lý state chung cho nhiều component.
- Có thể dùng:
  - Vuex (Vue 2 & Vue 3)
  - Pinia (Vue 3, được khuyến nghị)

## 3. State với Composition API (Vue 3)
- Dùng ref() hoặc reactive() để tạo state.
```
Ví dụ với ref():
<script setup>
import { ref } from 'vue';
const count = ref(0);
function increment() {
  count.value++;
}
</script>
<template>
  <p>{{ count }}</p>
  <button @click="increment">Tăng</button>
</template>

Ví dụ với reactive():
<script setup>
import { reactive } from 'vue';
const state = reactive({ count: 0 });
function increment() {
  state.count++;
}
</script>
<template>
  <p>{{ state.count }}</p>
  <button @click="increment">Tăng</button>
</template>
```
## 4. Khi nào dùng Vuex hoặc Pinia?
- Khi ứng dụng có nhiều component cần truy cập và cập nhật cùng một state.
- Khi logic quản lý state phức tạp (ví dụ: nhiều mutation, action, hoặc cần lưu cache).

---

# 5. Sự khác nhau giữa reactive và ref
## ref()
- Dùng cho giá trị đơn hoặc object đơn giản.
- Trả về object có thuộc tính .value.
Ví dụ:
```
import { ref } from 'vue';
const count = ref(0);
count.value++;
```
## reactive()
- Dùng cho object hoặc array phức tạp.
- Trả về proxy object, không cần .value.
Ví dụ:
```
import { reactive } from 'vue';
const state = reactive({ count: 0, name: 'Vue' });
state.count++;
```

So sánh nhanh:
| Đặc điểm        | ref()                     | reactive()                  |
|-----------------|---------------------------|-----------------------------|
| Dữ liệu         | Giá trị đơn hoặc object  | Object hoặc array phức tạp |
| Truy cập        | .value                   | Trực tiếp                  |

---

# 6. So sánh Vuex và Pinia
## Vuex
- Cơ chế: state, mutations, actions, getters.
- Mutation bắt buộc để thay đổi state.
- Cú pháp dài, nhiều boilerplate.
- Hỗ trợ Vue 2 và Vue 3.

## Pinia
- Cơ chế: state, getters, actions (không có mutation).
- API đơn giản, cú pháp ngắn gọn.
- Hỗ trợ TypeScript tốt.
- Tích hợp Composition API.
- Khuyến nghị cho Vue 3.

So sánh nhanh:
| Tiêu chí        | Vuex                        | Pinia                        |
|-----------------|----------------------------|-----------------------------|
| Boilerplate     | Nhiều                     | Ít                          |
| Mutation        | Bắt buộc                  | Không cần                   |
| TypeScript      | Hỗ trợ cơ bản             | Hỗ trợ tốt                  |
| Vue 3           | Có nhưng không tối ưu     | Tối ưu                      |

---

# 7. Mutation và Action trong Vuex
## Mutation
- Phương thức đồng bộ để thay đổi trực tiếp state.
- Nhận vào state và payload.
Ví dụ:
```
const store = new Vuex.Store({
  state: { count: 0 },
  mutations: {
    increment(state, payload) {
      state.count += payload;
    }
  }
});
store.commit('increment', 5);
```
## Action
- Phương thức bất đồng bộ, dùng để thực hiện logic trước khi commit mutation.
Ví dụ:
```
const store = new Vuex.Store({
  state: { count: 0 },
  mutations: {
    increment(state, payload) {
      state.count += payload;
    }
  },
  actions: {
    async incrementAsync({ commit }) {
      await new Promise(resolve => setTimeout(resolve, 1000));
      commit('increment', 10);
    }
  }
});
store.dispatch('incrementAsync');
```
So sánh nhanh:
| Tiêu chí       | Mutation                     | Action                        |
|---------------|-----------------------------|-----------------------------|
| Thay đổi state| Có (trực tiếp)             | Không (phải commit mutation)|
| Đồng bộ       | Bắt buộc                   | Có thể bất đồng bộ          |

Trong Pinia:
- Không có mutation riêng.
- Action có thể đồng bộ hoặc bất đồng bộ và thay đổi state trực tiếp.

---

# Kết luận
- Dùng ref cho giá trị đơn, reactive cho object phức tạp.
- Dùng Pinia cho Vue 3 để quản lý state toàn cục.
- Mutation và Action giúp kiểm soát luồng dữ liệu trong Vuex.
