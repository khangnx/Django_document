🔄 Lifecycle Hooks trong Vue 3 (Composition API) vs Options API

📌 Khái niệm Lifecycle Hooks  
Lifecycle hooks là các hàm đặc biệt trong Vue cho phép bạn chạy logic tại những thời điểm khác nhau trong vòng đời của component (khi khởi tạo, render, cập nhật, hoặc hủy bỏ).

⚙️ Lifecycle Hooks trong Options API  
Trong Options API, bạn định nghĩa các hook như thuộc tính trong object component:

```vue
<script>
export default {
  data() {
    return { count: 0 }
  },
  created() {
    console.log('Component đã được tạo')
  },
  mounted() {
    console.log('Component đã gắn vào DOM')
  },
  updated() {
    console.log('Component đã cập nhật')
  },
  unmounted() {
    console.log('Component đã bị hủy')
  }
}
</script>
```

⚙️ Lifecycle Hooks trong Composition API
Trong Composition API, bạn import các hàm hook từ Vue và gọi trực tiếp bên trong setup():

```
<script setup>
import { ref, onMounted, onUpdated, onUnmounted } from 'vue'

const count = ref(0)

onMounted(() => {
  console.log('Component đã gắn vào DOM')
})

onUpdated(() => {
  console.log('Component đã cập nhật')
})

onUnmounted(() => {
  console.log('Component đã bị hủy')
})
</script>
```

<img width="853" height="471" alt="image" src="https://github.com/user-attachments/assets/2deefa50-f21f-49bb-a13c-5d3d29173d64" />
