🔄 Hệ thống Reactivity trong Vue.js và cách quản lý state
📌 Reactivity là gì?
Vue.js có một hệ thống reactivity mạnh mẽ: khi dữ liệu thay đổi, giao diện sẽ tự động cập nhật. Điều này giúp bạn viết code khai báo (declarative) thay vì phải thao tác DOM thủ công.

⚙️ Cách hoạt động dưới "gầm máy"
Vue 2: dùng getter/setter để theo dõi thay đổi.
Vue 3: dùng ES6 Proxy để intercept việc đọc/ghi thuộc tính.

Khi đọc dữ liệu reactive → Vue ghi nhận dependency.
Khi ghi dữ liệu reactive → Vue kích hoạt lại các effect (render, computed, watcher).

Ví dụ:

```js
import { ref, reactive, computed } from 'vue'

const count = ref(0)
const state = reactive({ items: [] })

const double = computed(() => count.value * 2)
```

👉 Khi count.value thay đổi, double và giao diện liên quan sẽ tự động cập nhật.

🛠️ Công cụ trong Composition API
ref(): tạo biến reactive cho giá trị đơn lẻ.
reactive(): tạo object reactive cho nhiều thuộc tính.
computed(): tạo giá trị tính toán có cache.
watch(): theo dõi sự thay đổi để thực hiện hành động phụ.

🏗️ Quản lý state theo quy mô
Với component nhỏ: ref và reactive là đủ để quản lý state cục bộ.

Với ứng dụng lớn và phức tạp:

* Vuex: cung cấp store tập trung, quản lý state toàn cục với mutation, action, getter.
* Composition API patterns: tạo composable functions như useAuth(), useCart(), useTheme() để chia sẻ logic reactive giữa nhiều component mà không cần Vuex.

Ví dụ composable:

```js
// useCounter.js
import { ref } from 'vue'

export function useCounter() {
  const count = ref(0)
  const increment = () => count.value++
  return { count, increment }
}
```

👉 Có thể tái sử dụng ở nhiều component.

📊 So sánh nhanh

| Công cụ         | Khi dùng tốt nhất                                       |
| --------------- | ------------------------------------------------------- |
| ref / reactive  | Component nhỏ, state cục bộ                             |
| Composition API | Ứng dụng vừa, chia sẻ logic giữa nhiều component        |
| Vuex            | Ứng dụng lớn, nhiều module, cần quản lý state tập trung |

👉 Kết luận
Vue’s reactivity system rất mạnh mẽ, giúp đồng bộ dữ liệu và giao diện một cách tự động.

ref và reactive hoàn hảo cho component nhỏ.

Với ứng dụng lớn, hãy dùng Vuex hoặc Composition API patterns để giữ mọi thứ có tổ chức và dễ bảo trì.
