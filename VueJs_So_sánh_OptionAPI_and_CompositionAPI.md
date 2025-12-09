# So sánh Options API và Composition API trong Vue.js

## 📖 Khái niệm

### Options API

- Xuất hiện từ các phiên bản đầu của Vue.js.

- Component được định nghĩa bằng cách chia logic theo options: data, methods, computed, watch, props...

- Dễ tiếp cận cho người mới vì cấu trúc rõ ràng, tách biệt từng phần.

### Composition API

- Giới thiệu từ Vue 3.0.

- Định nghĩa logic trong hàm setup() với các reactive APIs (ref, reactive, computed, watch, provide/inject...).

- Logic được nhóm theo chức năng thay vì theo loại (options).

  <img width="900" height="462" alt="image" src="https://github.com/user-attachments/assets/dcb336ad-dea3-4473-a7f3-7dd53e7600a9" />

  
## 💻 Ví dụ minh họa

### Options API

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
    }
  },
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>
```

### Composition API

```
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double Count: {{ doubleCount }}</p>
    <p>Message: {{ message }}</p>
    <button @click="increment">Tăng</button>
    <button @click="decrement">Giảm</button>
    <button @click="reset">Đặt lại</button>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  setup() {
    const count = ref(0)

    // Function 1: Tăng giá trị count
    const increment = () => {
      count.value++
    }

    // Function 2: Giảm giá trị count
    const decrement = () => {
      count.value--
    }

    // Function 3: Đặt lại count về 0
    const reset = () => {
      count.value = 0
    }

    // Computed property để tính giá trị gấp đôi
    const doubleCount = computed(() => count.value * 2)

    // Một message đơn giản dựa trên giá trị count
    const message = computed(() => {
      if (count.value > 0) return 'Số dương'
      else if (count.value < 0) return 'Số âm'
      else return 'Bằng 0'
    })

    return { count, increment, decrement, reset, doubleCount, message }
  }
}
</script>
 ```
