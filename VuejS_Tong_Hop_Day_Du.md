# Vue.js — Câu trả lời chi tiết và ví dụ phức tạp

Tài liệu này trả lời từng câu hỏi của bạn, kèm **ví dụ phức tạp minh họa**. Bạn có thể tải xuống file `.md` này để tham khảo.

---

## Mục lục
1. Vue.js là gì?
2. DOM ảo (Virtual DOM) là gì và tại sao Vue sử dụng nó?
3. Tính năng cốt lõi của Vue
4. Reactivity (Tính phản ứng) trong Vue hoạt động như thế nào?
5. Component là gì? Cách tạo component đơn giản
6. API & Composition API (Vue 3)
7. Sự khác nhau giữa Options API và Composition API
8. `setup()` function hoạt động như thế nào?
9. `ref()` vs `reactive()` khác nhau thế nào?
10. `computed` vs `watch` — khi nào dùng cái nào?
11. Lifecycle Hooks trong Vue 3 (ví dụ: `onMounted`, `onUpdated`)
12. Truyền dữ liệu & Component
    - Props
    - `$emit`
    - Slots
    - `provide` / `inject`
    - `v-model`
13. Directive & Binding
    - `v-if` vs `v-show`
    - `v-for` và `key`
    - Binding class/style có điều kiện
    - Custom directive
14. Quản lý State & Routing
    - Vuex vs Pinia
    - Vue Router
15. Vấn đề nâng cao
    - Tối ưu hiệu năng (lazy loading, code splitting)
    - Mixins và Composables

---

# 1. Vue.js là gì?
**Đáp:** Vue.js là một framework JavaScript tiến bộ để xây dựng giao diện người dùng. Vue tập trung vào tầng view (UI) và dễ tích hợp vào các dự án hiện có. Vue 3 cải thiện hiệu năng, bundle size nhỏ hơn và cung cấp Composition API.

**Ví dụ phức tạp minh họa:** Một ứng dụng dashboard có nhiều widget độc lập, mỗi widget có trạng thái riêng (filter, sort, pagination). Vue cho phép tách widget thành component độc lập, sử dụng reactive state và composables để chia sẻ logic giữa widget.

```vue
<!-- App.vue (tóm tắt) -->
<template>
  <div>
    <Widget v-for="w in widgets" :key="w.id" :config="w" />
  </div>
</template>

<script setup>
import Widget from './components/Widget.vue'
const widgets = [{ id: 1, type: 'chart' }, { id: 2, type: 'table' }]
</script>
```

# 2. DOM ảo (Virtual DOM) là gì và tại sao Vue lại sử dụng nó?
**Đáp:** Virtual DOM là một bản đại diện (tree) nhẹ của DOM thật. Khi state thay đổi, Vue tạo Virtual DOM mới, so sánh với bản cũ (diffing), và chỉ cập nhật những phần khác nhau trên DOM thật — giảm thao tác đắt tiền trên DOM.

**Vì sao Vue dùng:** giảm số lần thao tác DOM thật, tăng hiệu năng, viết code tường minh mà Vue đảm bảo tối ưu cập nhật.

**Ví dụ phức tạp minh họa:** Trong bảng lớn (1000 hàng) với filter và sort, mỗi lần update nếu cập nhật toàn bộ DOM sẽ rất chậm. Vue chỉ cập nhật những hàng thay đổi.

# 3. Tính năng cốt lõi của Vue
- Declarative rendering (template)
- Component system
- Reactivity system
- Computed properties & watchers
- Directives (`v-if`, `v-for`, `v-bind`, `v-model`)
- Transition/Animation
- Router & State management ecosystem (Vue Router, Pinia)

**Ví dụ phức tạp minh họa:** App realtime chat: components (message list, composer), reactive state, computed for unread count, transitions cho message enter/leave.

# 4. Reactivity trong Vue hoạt động như thế nào?
**Đáp:** Vue 3 dùng Proxy để theo dõi truy cập và gán thuộc tính. Khi reactive state thay đổi, Vue đánh dấu những effect (render function, computed, watchers) phụ thuộc để chạy lại.

**Ví dụ phức tạp minh họa:** Một composable `useCart()` chứa `reactive({ items: [] })`. Khi `items.push(...)` xảy ra, component dùng `cart.items.length` (computed) sẽ tự cập nhật UI.

# 5. Component là gì? Cách tạo component đơn giản
**Đáp:** Component là khối UI độc lập, có props, state, lifecycle. Có thể tái sử dụng.

**Component đơn giản (Options API):**
```vue
<template>
  <button @click="count++">Clicked {{ count }} times</button>
</template>
<script>
export default {
  data() { return { count: 0 } }
}
</script>
```

**Component (Composition API):**
```vue
<template>
  <button @click="increment">Clicked {{ count }} times</button>
</template>
<script setup>
import { ref } from 'vue'
const count = ref(0)
const increment = () => count.value++
</script>
```

**Ví dụ phức tạp minh họa:** Component `DataTable` hỗ trợ: server-side pagination, sorting, filtering, virtual scrolling — tách logic liên quan vào composables (`usePagination`,`useSort`,`useVirtualScroll`).

# 6. API & Composition API (Vue 3)
**Đáp:** Composition API cho phép gom nhóm logic theo chức năng (logic-centric) thay vì theo option type. Các hàm chính: `setup()`, `ref`, `reactive`, `computed`, `watch`, `onMounted`.

**Ví dụ phức tạp minh họa:** Tạo composable `useFetch(url)` trả về `{ data, error, loading, refetch }` để tái sử dụng ở nhiều component.

# 7. Sự khác nhau giữa Options API và Composition API?
- Options API: tổ chức code theo thuộc tính (data, methods, computed, watch) — dễ với component nhỏ.
- Composition API: tổ chức theo chức năng, tốt cho component phức tạp, tái sử dụng logic qua composables.

**Ví dụ phức tạp minh họa:** Form phức tạp nhiều bước (multi-step form). Với Composition API có thể gom validation, state của bước, và chuyển bước vào composable, tái sử dụng trong nhiều form.

# 8. `setup()` function hoạt động như thế nào?
`setup(props, context)` chạy **trước** khi component được tạo DOM; nơi này khởi tạo reactive state, khai báo lifecycle hooks, trả về giá trị cho template.

**Lưu ý:** `setup` không có `this`. Để expose cho template, trả về object hoặc dùng `<script setup>`.

**Ví dụ phức tạp minh họa:** Trong `setup` kết hợp `onMounted` để fetch dữ liệu bất đồng bộ, xử lý cleanup với `onUnmounted`.

```js
import { ref, onMounted, onUnmounted } from 'vue'
export default {
  setup() {
    const data = ref(null)
    let controller
    onMounted(async () => {
      controller = new AbortController()
      const res = await fetch('/api/large', { signal: controller.signal })
      data.value = await res.json()
    })
    onUnmounted(() => controller && controller.abort())
    return { data }
  }
}
```

# 9. `ref()` vs `reactive()` khác nhau thế nào?
- `ref(value)` bọc giá trị nguyên thủy hoặc object vào `Ref` có `.value` (phù hợp cho primitives hoặc khi muốn reassign toàn bộ giá trị).
- `reactive(obj)` trả về Proxy reactive cho object — thao tác trực tiếp trên thuộc tính.

**Ví dụ:**
```js
const count = ref(0)
count.value++
const state = reactive({ a: 1, b: 2 })
state.a = 3
```

**Ví dụ phức tạp minh họa:** Khi cần một reactive Map/Set hoặc muốn reassign toàn bộ state, dùng `ref(new Map())`. Với object lồng nhau, `reactive` tiện hơn để giữ reactivity sâu.

# 10. `computed` vs `watch` khi nào dùng cái nào?
- `computed`: dùng khi muốn giá trị được tính lại và truy xuất (cached) dựa trên reactive dependencies.
- `watch`: dùng để theo dõi side-effect khi giá trị thay đổi (ví dụ: gọi API, sync ra localStorage).

**Ví dụ phức tạp minh họa:**
- `computed` để tính `filteredList` từ `list` và `query`.
- `watch` để lưu `query` vào localStorage hoặc debounce fetch khi `query` thay đổi.

```js
const filtered = computed(() => items.value.filter(...))
watch(query, (newQ) => debounceFetch(newQ))
```

# 11. Lifecycle Hooks trong Vue 3
Các hook tiêu chuẩn: `onMounted`, `onUpdated`, `onUnmounted`, `onBeforeMount`, `onBeforeUnmount`, `onBeforeUpdate`, `onErrorCaptured`.

**Ví dụ phức tạp minh họa:** Component quản lý WebSocket: mở kết nối onMounted, xử lý message onUpdated (hoặc trực tiếp trong event handler), đóng kết nối onUnmounted.

```js
import { onMounted, onUnmounted } from 'vue'
setup() {
  let ws
  onMounted(() => { ws = new WebSocket('wss://...'); ws.onmessage = (e) => handle(e) })
  onUnmounted(() => ws && ws.close())
}
```

# 12. Truyền dữ liệu & Component

### Props là gì?
Props là cách truyền dữ liệu từ component cha xuống con (read-only trong component con).

**Ví dụ:**
```vue
<MyChild :title="parentTitle" :count="10" />
```
Trong child: `defineProps({ title: String, count: Number })` hoặc `props: ['title','count']`.

### `$emit` dùng để làm gì?
Child dùng `$emit('eventName', payload)` để gửi sự kiện lên cha. Cha lắng nghe `<Child @eventName="handler" />`.

**Ví dụ phức tạp minh họa:** Một component `EditableRow` emit `save` kèm dữ liệu đã chỉnh sửa; parent cập nhật data store và show toast.

### Slots là gì?
Slots cho phép content injection từ cha vào con. Có default slot, named slots và scoped slots (slot props).

**Ví dụ phức tạp minh họa:** Component `Modal` có header, body, footer slot; parent cung cấp actions và truyền callback qua slot-scope.

### `provide` / `inject` dùng để làm gì?
Dùng để chia sẻ dữ liệu giữa components không phải cha-con trực tiếp (vd: theme, i18n, store nhẹ) mà không cần prop drilling.

**Ví dụ phức tạp minh họa:** Trong app lớn, root `App` provide `i18n` instance; nhiều component sâu trong tree có thể `inject('i18n')` và dùng translation.

### `v-model` hoạt động như thế nào?
`v-model` tạo two-way binding mặc định giữa parent prop và child emit event. Ở Vue 3, `v-model` có thể được tùy chỉnh tên prop/event.

**Ví dụ:**
Child:
```js
defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
// khi thay đổi: emit('update:modelValue', newValue)
```
Parent: `<MyInput v-model="value" />`

**Ví dụ phức tạp minh họa:** Component form complex: input components sử dụng `v-model` với object field, parent form aggregate và validate trước submit.

# 13. Directive & Binding

### `v-if` vs `v-show`
- `v-if` mount/unmount DOM theo điều kiện — tốn chi phí render lần đầu và mount/unmount khi toggled.
- `v-show` luôn render nhưng toggle `display: none` — phù hợp khi toggled thường xuyên.

**Khi nào dùng:** nếu hiển thị ít thay đổi -> `v-if`; nếu toggle rất thường xuyên -> `v-show`.

### `v-for` và `key`
`key` giúp Vue nhận dạng phần tử giữa các lần render; tối ưu diffing và tránh lỗi (ví dụ: giữ state input khi reorder).

**Ví dụ phức tạp minh họa:** List dynamic reorder: nếu thiếu `key`, input focus/state có thể bị swap nhầm.

### Binding class/style có điều kiện
```vue
<div :class="{ active: isActive, 'text-large': size === 'lg' }"></div>
<div :style="{ width: width + 'px', opacity: visible ? 1 : 0 }"></div>
```

### Custom Directive
Tạo directive để thao tác DOM trực tiếp (ví dụ: click-outside, lazy-load image).

**Ví dụ phức tạp minh họa:** `v-click-outside` để detect click bên ngoài modal và đóng modal.

```js
// directive.js
export default {
  beforeMount(el, binding) {
    el.__clickOutsideHandler = (e) => { if (!el.contains(e.target)) binding.value(e) }
    document.addEventListener('click', el.__clickOutsideHandler)
  },
  unmounted(el) { document.removeEventListener('click', el.__clickOutsideHandler) }
}
```

# 14. Quản lý State & Routing

### Vuex (Vue 2) vs Pinia (Vue 3)
- **Vuex:** state tree, mutations (sync), actions (async), getters. Khá boilerplate-y.
- **Pinia:** API hiện đại hơn, modular store, TypeScript-friendly, less boilerplate; khuyến nghị cho Vue 3.

**So sánh:** Pinia nhẹ nhàng, dễ test, hỗ trợ composition better; Vuex vẫn mạnh và có ecosystem nhưng Pinia đang được khuyến nghị.

**Ví dụ phức tạp minh họa:** Implement store shopping-cart với persistence, plugins (localStorage), và multiple modules: user, cart, products — với Pinia dễ viết hơn và integrate vào composition API.

### Vue Router
Quản lý navigation (routes, nested routes, lazy-loaded routes, navigation guards). Hỗ trợ history mode.

**Ví dụ phức tạp minh họa:** App có protected routes (auth), dynamic nested routes cho product/:id/reviews, lazy load components bằng `defineAsyncComponent` hoặc route-level code splitting.

# 15. Vấn đề nâng cao

### Tối ưu hiệu năng (lazy loading, code splitting)
- Sử dụng dynamic imports: `const Comp = () => import('./Comp.vue')` (route-level split). 
- Component-level lazy load: `defineAsyncComponent()`.
- Virtual scrolling cho danh sách lớn (`vue-virtual-scroller`).
- Debounce/throttle cho input và events.
- Server-Side Rendering (SSR) hoặc pre-rendering cho SEO.

**Ví dụ phức tạp minh họa:** Route-level code splitting kết hợp prefetch: webpack magic comments `/* webpackPrefetch: true */` để tải trước.

### Mixins và Composables
- **Mixins:** chia sẻ logic nhưng có nguy cơ xung đột tên, kém rõ ràng.
- **Composables:** functions dùng composition API trả về reactive state/funcs, rõ ràng và dễ test.

**Ví dụ phức tạp minh họa:** `useAuth()` composable trả về `user`, `login`, `logout`, `isAuthenticated`. Có thể reuse across components.

---

## Kết luận & Tài nguyên
Tài liệu trên trình bày khái niệm, cách dùng và ví dụ phức tạp để minh họa. Nếu bạn muốn, tôi có thể:
- Tách riêng các ví dụ code thành project mẫu.
- Viết test (Vitest) cho composables / components.

---

*Tài liệu được tạo tự động — sẵn sàng để tải xuống.*
