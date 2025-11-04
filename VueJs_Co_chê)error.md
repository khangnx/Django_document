```
Các cơ chế thông báo lỗi trong Vue.js

1. errorCaptured Hook (Vue 2 & Vue 3)
- Dùng trong component để bắt lỗi từ component con.
Ví dụ:
errorCaptured(err, vm, info) {
    console.error('Lỗi:', err);
    return false; // Ngăn lỗi lan lên component cha
}

2. Global Error Handler (app.config.errorHandler)
- Định nghĩa một hàm xử lý lỗi toàn cục.
Vue 3:
const app = createApp(App);
app.config.errorHandler = (err, instance, info) => {
    console.error('Global Error:', err);
    // Có thể gửi lỗi về server hoặc hiển thị toast
};

Vue 2:
Vue.config.errorHandler = function (err, vm, info) {
    console.error(err);
};

3. try...catch trong phương thức hoặc async/await
- Khi gọi API hoặc thực hiện logic có thể lỗi:
async fetchData() {
    try {
        const res = await axios.get('/api/data');
    } catch (error) {
        this.errorMessage = 'Không thể tải dữ liệu';
    }
}

4. Validation & Form Error
- Sử dụng thư viện như VeeValidate, Yup, hoặc tự viết logic để hiển thị lỗi cho người dùng.
Ví dụ:
<span v-if="errors.name">Tên không hợp lệ</span>

5. Thông báo lỗi qua UI (Toast, Modal)
- Dùng thư viện như Vue Toastification, Vuetify Snackbar, hoặc tự tạo component thông báo.
Ví dụ:
this.$toast.error('Có lỗi xảy ra!');

6. onErrorCaptured (Vue 3 Composition API)
- Tương tự errorCaptured nhưng dùng trong setup:
import { onErrorCaptured } from 'vue';
onErrorCaptured((err, instance, info) => {
    console.error(err);
    return false;
});

```
