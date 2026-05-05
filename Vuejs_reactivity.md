*** Trong Vue.js, “reactivity” là hệ thống giúp dữ liệu thay đổi thì giao diện (UI) tự động cập nhật theo, mà không cần bạn viết thêm nhiều logic thủ công. Đây là một trong những đặc điểm nổi bật khiến Vue dễ dùng và mạnh mẽ.

** 🔑 Khái niệm Reactivity trong Vue.js
Reactivity là cơ chế phản ứng tự động khi dữ liệu thay đổi.

Ví dụ: nếu bạn có biến count hiển thị trong template, khi count tăng lên thì UI sẽ tự động hiển thị giá trị mới.

Vue theo dõi việc đọc và ghi dữ liệu, sau đó kích hoạt lại các hàm (effect) liên quan để cập nhật giao diện.

** Cách Vue triển khai Reactivity
Vue 2: sử dụng getter/setter để theo dõi thay đổi trong object.

Vue 3: dùng JavaScript Proxy để tạo reactive object, giúp theo dõi cả thuộc tính lồng nhau.

Các API chính:

ref(): tạo biến reactive đơn giản (ví dụ: const count = ref(0)).

reactive(): biến object thành reactive proxy (ví dụ: const state = reactive({ name: 'Nguyen' })).

computed(): tạo giá trị phụ thuộc, tự động tính lại khi dữ liệu gốc thay đổi.

** 📊 Ví dụ minh họa

```
js
import { ref, reactive, computed } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const user = reactive({ name: 'Nguyen', age: 25 })
    const double = computed(() => count.value * 2)

    return { count, user, double }
  }
}

```
Khi count.value++, cả count và double trong template sẽ tự động cập nhật.

** ⚠️ Lưu ý quan trọng
Phải khai báo trước trong data() hoặc setup(): nếu thêm thuộc tính mới ngoài hệ thống reactive, Vue sẽ không theo dõi được.

Refs trong template: không cần .value, Vue tự động unwrap.

Reactive object: luôn truy cập qua proxy, không dùng object gốc.

** 📌 Tóm lại
Vue.js reactivity là hệ thống theo dõi và phản ứng với thay đổi dữ liệu, giúp UI luôn đồng bộ với state. Nó hoạt động nhờ Proxy (Vue 3) hoặc getter/setter (Vue 2), và được sử dụng thông qua các API như ref(), reactive(), computed(). Đây chính là nền tảng giúp Vue trở nên trực quan và dễ phát triển ứng dụng.
