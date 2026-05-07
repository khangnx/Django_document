# Vue Router trong Vue.js

Trong Vue, Vue Router là thư viện chính thức giúp bạn quản lý điều hướng (routing) trong ứng dụng. Nó đặc biệt quan trọng khi xây dựng Single-Page Applications (SPA), nơi toàn bộ ứng dụng chạy trong một trang HTML duy nhất nhưng vẫn có nhiều “trang” khác nhau về mặt trải nghiệm.

## 🔹 Khái niệm cơ bản

- **Router**: giống như một bản đồ, định nghĩa URL nào sẽ hiển thị component nào.

- **SPA**: thay vì tải lại toàn bộ trang khi chuyển hướng, Vue Router chỉ thay đổi component hiển thị, giúp ứng dụng nhanh hơn và mượt mà hơn.

- **History mode / Hash mode**: Vue Router có thể dùng `history.pushState` để tạo URL đẹp (ví dụ `/about`) hoặc dùng hash (`#/about`) để điều hướng.

## 🔹 Cách Vue Router giúp xây dựng SPA

### 1. Định nghĩa routes

Bạn khai báo các đường dẫn và component tương ứng.

```js
import { createRouter, createWebHistory } from 'vue-router'
import Home from './components/Home.vue'
import About from './components/About.vue'

const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

### 2. Sử dụng `<router-view>`

Đây là nơi component sẽ được hiển thị tùy theo URL.

```vue
<template>
  <div>
    <nav>
      <router-link to="/">Trang chủ</router-link>
      <router-link to="/about">Giới thiệu</router-link>
    </nav>

    <router-view />
  </div>
</template>
```

### 3. Điều hướng không tải lại trang

Khi bạn bấm vào link, Vue Router sẽ thay đổi component trong `<router-view>` mà không reload toàn bộ trang.

## 🔹 Lợi ích

- Trải nghiệm mượt mà: không bị gián đoạn bởi việc tải lại trang.

- Quản lý URL rõ ràng: mỗi trạng thái của ứng dụng có một đường dẫn riêng.

- Hỗ trợ tính năng nâng cao:
  - Route guards (bảo vệ route)
  - Lazy loading (tải component khi cần)
  - Nested routes (route lồng nhau)

## 👉 Tóm lại

Vue Router chính là “xương sống” để biến ứng dụng Vue thành một SPA thực thụ, giúp bạn điều hướng giữa các phần khác nhau mà vẫn giữ trải nghiệm nhanh và liền mạch.


======================================================================================================================



# Vuex, Pinia, Reactivity và các khái niệm quan trọng trong Vue.js

---

# Vuex trong Vue.js

Trong Vue, Vuex là thư viện chính thức dùng để quản lý state (trạng thái dữ liệu) trong ứng dụng, đặc biệt hữu ích khi ứng dụng lớn có nhiều component cần chia sẻ và đồng bộ dữ liệu.

## 🔹 Vấn đề khi không dùng Vuex

- Mỗi component có data riêng, nhưng khi nhiều component cần dùng chung dữ liệu, việc truyền qua lại bằng props và events trở nên phức tạp.

- Dữ liệu dễ bị phân tán, khó kiểm soát và khó bảo trì khi ứng dụng mở rộng.

## 🔹 Vuex giải quyết như thế nào

Vuex cung cấp một store trung tâm để lưu trữ toàn bộ state của ứng dụng. Các component không cần truyền dữ liệu vòng vo, mà chỉ cần:

- Lấy dữ liệu từ store.
- Gửi hành động để thay đổi dữ liệu trong store.

### Cấu trúc chính của Vuex

- **State**: nơi lưu trữ dữ liệu toàn cục.

- **Getters**: giống như computed properties, dùng để lấy dữ liệu từ state theo cách xử lý sẵn.

- **Mutations**: phương thức duy nhất để thay đổi state (đồng bộ).

- **Actions**: giống mutations nhưng có thể chứa logic bất đồng bộ (ví dụ gọi API), sau đó commit mutation.

- **Modules**: chia store thành nhiều phần nhỏ để dễ quản lý khi ứng dụng lớn.

## 🔹 Ví dụ đơn giản

```js
// store.js
import { createStore } from 'vuex'

export default createStore({
  state: {
    count: 0
  },
  mutations: {
    increment(state) {
      state.count++
    }
  },
  actions: {
    asyncIncrement({ commit }) {
      setTimeout(() => {
        commit('increment')
      }, 1000)
    }
  },
  getters: {
    doubleCount: state => state.count * 2
  }
})
```

Trong component:

```vue
<template>
  <div>
    <p>{{ $store.state.count }}</p>
    <p>{{ $store.getters.doubleCount }}</p>

    <button @click="$store.commit('increment')">
      Tăng ngay
    </button>

    <button @click="$store.dispatch('asyncIncrement')">
      Tăng sau 1s
    </button>
  </div>
</template>
```

## 🔹 Lợi ích của Vuex

- Quản lý tập trung: mọi dữ liệu quan trọng nằm trong một nơi duy nhất.

- Dễ bảo trì: luồng dữ liệu rõ ràng, dễ theo dõi.

- Hỗ trợ ứng dụng lớn: chia module, dễ mở rộng.

- Debug dễ dàng: có thể theo dõi mutation và action trong DevTools.

---

# So sánh Vuex và Pinia

Pinia là giải pháp quản lý state mới, nhẹ và hiện đại hơn Vuex, đặc biệt phù hợp với Vue 3 và TypeScript. Vuex vẫn mạnh mẽ cho ứng dụng lớn, nhưng nhiều boilerplate và phức tạp hơn.

## 🔹 So sánh Vuex và Pinia

| Tiêu chí | Vuex | Pinia |
|---|---|---|
| Độ tuổi & vị thế | Thư viện lâu đời, chính thức cho Vue 2 | Mới hơn, được core team Vue khuyến nghị cho Vue 3 |
| Cấu trúc store | Centralized store (một store duy nhất, chia module) | Modular by default (nhiều store nhỏ, dễ quản lý) |
| Cách thay đổi state | Bắt buộc qua mutations và actions | Không cần mutations, chỉ dùng actions |
| TypeScript | Hỗ trợ nhưng khá rườm rà | Hỗ trợ TypeScript tốt hơn |
| Boilerplate | Nhiều | Ít, cú pháp gọn |
| DevTools | Tích hợp tốt | Tích hợp tốt |
| Khi nên dùng | Ứng dụng lớn, Vue 2 | Vue 3, TypeScript |

## 🔹 Ví dụ code

### Vuex

```js
// store.js
export default createStore({
  state: { count: 0 },
  mutations: {
    increment(state) {
      state.count++
    }
  },
  actions: {
    asyncIncrement({ commit }) {
      setTimeout(() => commit('increment'), 1000)
    }
  }
})
```

### Pinia

```js
// stores/counter.js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0 }),

  actions: {
    increment() {
      this.count++
    },

    asyncIncrement() {
      setTimeout(() => this.increment(), 1000)
    }
  }
})
```

## 🔹 Kết luận

- Nếu bạn đang làm việc với Vue 2 hoặc ứng dụng enterprise đã dùng Vuex lâu năm → tiếp tục dùng Vuex.

- Nếu bạn bắt đầu với Vue 3 hoặc muốn code gọn, dễ bảo trì, hỗ trợ TypeScript tốt → chọn Pinia.

👉 Nói ngắn gọn: Vuex = “cựu binh” mạnh mẽ cho dự án lớn, Pinia = “thế hệ mới” nhẹ nhàng, hiện đại cho Vue 3.

---

# Reactivity trong Vue.js

Trong Vue, hệ thống reactivity (tính phản ứng) là “trái tim” giúp dữ liệu và giao diện luôn đồng bộ với nhau.

## 🔹 Cách Vue reactivity hoạt động

- **Proxy (Vue 3)**: Vue sử dụng Proxy để theo dõi dữ liệu.

- **Dependency tracking**: Vue ghi nhớ dữ liệu nào liên kết với DOM nào.

- **Re-render**: chỉ cập nhật phần DOM cần thiết.

## 🔹 Ví dụ đơn giản

```vue
<template>
  <div>
    <p>{{ message }}</p>

    <button @click="message = 'Xin chào Vue!'">
      Đổi nội dung
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: 'Hello World'
    }
  }
}
</script>
```

- Ban đầu hiển thị `"Hello World"`.

- Khi bấm nút, Vue tự động cập nhật nội dung `<p>` mà không reload trang.

## 🔹 Lợi ích

- Tự động đồng bộ dữ liệu và giao diện.

- Hiệu năng cao.

- Không cần thao tác DOM thủ công.

👉 Tóm lại: Reactivity giúp dữ liệu và giao diện luôn đồng bộ với nhau.

---

# Computed Properties và Watchers

Trong Vue, computed properties và watchers đều dựa trên reactivity nhưng phục vụ mục đích khác nhau.

## 🔹 Computed Properties

- Là thuộc tính tính toán dựa trên dữ liệu gốc.

- Có cache.

- Thường dùng để hiển thị dữ liệu đã xử lý.

### Ví dụ

```vue
<template>
  <p>Giá gốc: {{ price }}</p>
  <p>Giá sau thuế: {{ priceWithTax }}</p>
</template>

<script>
export default {
  data() {
    return { price: 100 }
  },

  computed: {
    priceWithTax() {
      return this.price * 1.1
    }
  }
}
</script>
```

## 🔹 Watchers

- Theo dõi sự thay đổi dữ liệu.

- Không cache.

- Thường dùng cho side-effects như gọi API.

### Ví dụ

```vue
<script>
export default {
  data() {
    return { query: '' }
  },

  watch: {
    query(newVal) {
      this.searchData(newVal)
    }
  },

  methods: {
    searchData(val) {
      console.log('Searching for:', val)
    }
  }
}
</script>
```

## 🔹 Khi nào dùng cái nào?

- **Computed** → dữ liệu hiển thị đã xử lý.

- **Watcher** → side-effects hoặc logic bất đồng bộ.

👉 Computed = dữ liệu xử lý, Watcher = phản ứng khi dữ liệu thay đổi.

---

# Directive và Component trong Vue.js

## 🔹 Directive

Directive là các thuộc tính đặc biệt bắt đầu bằng `v-`.

### Ví dụ

```vue
<template>
  <input v-model="message" />

  <p v-if="message">
    Bạn nhập: {{ message }}
  </p>
</template>

<script>
export default {
  data() {
    return { message: '' }
  }
}
</script>
```

- `v-model`: đồng bộ dữ liệu hai chiều.

- `v-if`: điều khiển hiển thị.

## 🔹 Component

Component là khối UI độc lập có thể tái sử dụng.

### Ví dụ

```vue
<!-- Counter.vue -->
<template>
  <button @click="count++">
    Đã bấm {{ count }} lần
  </button>
</template>

<script>
export default {
  data() {
    return { count: 0 }
  }
}
</script>
```

```vue
<!-- App.vue -->
<template>
  <div>
    <Counter />
    <Counter />
  </div>
</template>

<script>
import Counter from './Counter.vue'

export default {
  components: { Counter }
}
</script>
```

## 🔹 So sánh nhanh

| Tiêu chí | Directive | Component |
|---|---|---|
| Mục đích | Thêm hành vi cho DOM | Tạo khối UI |
| Cấu trúc | Thuộc tính template | Template + script + style |
| Ví dụ | v-if, v-for | Counter.vue |

👉 Directive = hành vi, Component = khối UI.

---

# Form Validation trong Vue.js

## 🔹 1. Validation thủ công

```vue
<template>
  <form @submit.prevent="submitForm">
    <input v-model="email" type="email" />

    <p v-if="!isValidEmail">
      Email không hợp lệ
    </p>

    <button type="submit">
      Gửi
    </button>
  </form>
</template>

<script>
export default {
  data() {
    return { email: '' }
  },

  computed: {
    isValidEmail() {
      return /\S+@\S+\.\S+/.test(this.email)
    }
  },

  methods: {
    submitForm() {
      if (this.isValidEmail) {
        alert('Form hợp lệ!')
      }
    }
  }
}
</script>
```

## 🔹 2. Dùng custom directive

Ví dụ: `v-validate`.

## 🔹 3. Dùng thư viện hỗ trợ

- Vuelidate
- vee-validate

## 🔹 4. Kết hợp với Vuex/Pinia

Quản lý validation tập trung cho ứng dụng lớn.

## 🔹 Kết luận

- Form nhỏ → validation thủ công.

- Form lớn → dùng thư viện.

- Ứng dụng lớn → kết hợp Vuex/Pinia.

---

# Lazy Loading trong Vue.js

Lazy loading là kỹ thuật chỉ tải component hoặc route khi cần.

## 🔹 Lazy loading component

```js
import { defineAsyncComponent } from 'vue'

export default {
  components: {
    MyComponent: defineAsyncComponent(() =>
      import('./MyComponent.vue')
    )
  }
}
```

## 🔹 Lazy loading route

```js
import { createRouter, createWebHistory } from 'vue-router'

const routes = [
  {
    path: '/about',
    component: () => import('./views/About.vue')
  },
  {
    path: '/contact',
    component: () => import('./views/Contact.vue')
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

## 🔹 Lợi ích

- Giảm dung lượng tải ban đầu.

- Tăng tốc độ mở trang.

- Tối ưu hiệu năng.

👉 Lazy loading giúp ứng dụng nhẹ và nhanh hơn.

---

# Event Bus trong Vue.js

## 🔹 Vue 2 Event Bus

```js
// bus.js
import Vue from 'vue'

export const EventBus = new Vue()
```

### Phát sự kiện

```js
// ChildA.vue
import { EventBus } from './bus'

export default {
  methods: {
    sendMessage() {
      EventBus.$emit('my-event', 'Xin chào từ ChildA')
    }
  }
}
```

### Nhận sự kiện

```js
// ChildB.vue
import { EventBus } from './bus'

export default {
  created() {
    EventBus.$on('my-event', (msg) => {
      console.log('Nhận được:', msg)
    })
  }
}
```

## 🔹 Vue 3

Vue 3 không còn khuyến khích Event Bus.

### Thay thế

- Provide/Inject
- Pinia/Vuex
- mitt

### Ví dụ với mitt

```js
// bus.js
import mitt from 'mitt'

export const emitter = mitt()
```

```js
// ChildA.vue
import { emitter } from './bus'

emitter.emit('my-event', 'Hello from ChildA')
```

```js
// ChildB.vue
import { emitter } from './bus'

emitter.on('my-event', (msg) => console.log(msg))
```

👉 Vue 3 ưu tiên giải pháp dễ bảo trì hơn Event Bus.

---

# Internationalization (i18n) trong Vue.js

Internationalization (i18n) giúp ứng dụng hỗ trợ nhiều ngôn ngữ.

## 🔹 Thư viện phổ biến

- vue-i18n
- i18next

👉 vue-i18n là lựa chọn chuẩn cho Vue.

## 🔹 Cài đặt

```bash
npm install vue-i18n
```

## 🔹 Cấu hình

```js
// i18n.js
import { createI18n } from 'vue-i18n'

const messages = {
  en: {
    welcome: 'Welcome to Vue!'
  },

  vi: {
    welcome: 'Chào mừng đến với Vue!'
  }
}

const i18n = createI18n({
  locale: 'en',
  fallbackLocale: 'en',
  messages
})

export default i18n
```

## 🔹 Tích hợp

```js
import { createApp } from 'vue'
import App from './App.vue'
import i18n from './i18n'

createApp(App)
  .use(i18n)
  .mount('#app')
```

## 🔹 Sử dụng

```vue
<template>
  <p>{{ $t('welcome') }}</p>

  <button @click="$i18n.locale = 'vi'">
    Tiếng Việt
  </button>

  <button @click="$i18n.locale = 'en'">
    English
  </button>
</template>
```

👉 i18n giúp ứng dụng đa ngôn ngữ và thân thiện hơn với người dùng toàn cầu.

---

# Asynchronous Operations trong Vue.js

## 🔹 Async/Await trong methods

```vue
<template>
  <div>
    <button @click="fetchData">
      Tải dữ liệu
    </button>

    <p v-if="loading">
      Đang tải...
    </p>

    <p v-else>
      {{ data }}
    </p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      data: null,
      loading: false
    }
  },

  methods: {
    async fetchData() {
      this.loading = true

      try {
        const res = await fetch('https://api.example.com/data')
        this.data = await res.json()
      } catch (err) {
        console.error('Lỗi:', err)
      } finally {
        this.loading = false
      }
    }
  }
}
</script>
```

## 🔹 Lifecycle hooks

```js
export default {
  async mounted() {
    const res = await fetch('/api/users')
    this.users = await res.json()
  }
}
```

## 🔹 Vuex/Pinia

```js
export const useUserStore = defineStore('user', {
  state: () => ({ users: [] }),

  actions: {
    async loadUsers() {
      const res = await fetch('/api/users')
      this.users = await res.json()
    }
  }
})
```

👉 Vue xử lý async tốt nhờ reactivity kết hợp async/await.

---

# RBAC trong Vue.js

Role-based access control (RBAC) là cơ chế phân quyền theo vai trò người dùng.

## 🔹 Các bước triển khai

### 1. Xác định roles & permissions

Ví dụ:

- admin
- editor
- viewer

### 2. Lưu role trong store

```js
// Pinia store
export const useAuthStore = defineStore('auth', {
  state: () => ({
    userRole: null
  }),

  actions: {
    setRole(role) {
      this.userRole = role
    }
  }
})
```

### 3. Bảo vệ route

```js
const routes = [
  {
    path: '/admin',
    component: AdminPage,
    meta: { role: 'admin' }
  },
  {
    path: '/viewer',
    component: ViewerPage,
    meta: { role: 'viewer' }
  }
]

router.beforeEach((to, from, next) => {
  const authStore = useAuthStore()
  const requiredRole = to.meta.role

  if (
    requiredRole &&
    authStore.userRole !== requiredRole
  ) {
    return next('/unauthorized')
  }

  next()
})
```

### 4. Ẩn/hiện UI theo role

```vue
<button v-if="userRole === 'admin'">
  Xóa dữ liệu
</button>
```

### 5. Backend cũng phải kiểm tra quyền

Frontend chỉ hỗ trợ UX, backend mới là lớp bảo mật thật sự.

## 🔹 Kết luận

- Frontend: Vue Router + Pinia/Vuex.

- Backend: luôn xác thực quyền.

👉 RBAC trong Vue.js = kiểm soát route + UI + backend validation.
