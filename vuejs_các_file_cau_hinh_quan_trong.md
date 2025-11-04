```
Ý nghĩa và công dụng của các file trong dự án Vue:

1. setting.js
- Đây thường là file chứa các thiết lập cấu hình chung cho ứng dụng.
- Có thể bao gồm: URL API, key, biến môi trường, hoặc các hằng số dùng toàn cục.
- Giúp dễ dàng quản lý và thay đổi cấu hình mà không cần chỉnh sửa nhiều nơi trong code.

2. App.vue
- Là component gốc (root component) của ứng dụng Vue.
- Được mount vào phần tử HTML có id="app" thông qua main.js.
- Thường chứa layout tổng thể như header, footer, sidebar và <router-view> để hiển thị các trang con.
- Có thể chứa CSS toàn cục và điều phối router hoặc state.

3. main.js
- Là điểm khởi động của ứng dụng Vue.
- Import Vue, App.vue, router, store và mount ứng dụng vào DOM.
- Có thể import các file cấu hình hoặc middleware như permission.js.
- Ví dụ:
  import Vue from 'vue'
  import App from './App.vue'
  import router from './router'
  import store from './store'
  import './permission'
  new Vue({
    router,
    store,
    render: h => h(App)
  }).$mount('#app')

4. vue.config.js
- Là file cấu hình tùy chọn cho dự án Vue CLI.
- Cho phép ghi đè hoặc mở rộng cấu hình mặc định của Vue CLI.
- Công dụng:
  * Tùy chỉnh Webpack (alias, loader, plugin).
  * Thiết lập Dev Server (proxy, port, HTTPS).
  * Cấu hình build (outputDir, source map).
  * Thiết lập loader cho CSS/SCSS/Less.
  * Tùy chỉnh plugin của Vue CLI.
- Ví dụ:
  module.exports = {
    devServer: {
      port: 8080,
      proxy: {
        '/api': {
          target: 'http://localhost:3000',
          changeOrigin: true
        }
      }
    }
  }

5. permission.js
- Thường dùng để kiểm soát quyền truy cập (permission control) trong ứng dụng.
- Được import vào main.js để chạy ngay khi ứng dụng khởi động.
- Thường thiết lập navigation guard cho Vue Router (router.beforeEach).
- Kiểm tra token, role, hoặc quyền hạn trước khi cho phép truy cập route.
- Ví dụ:
  import router from './router'
  import store from './store'
  router.beforeEach((to, from, next) => {
    const token = store.getters.token
    if (token) {
      next()
    } else {
      if (to.path === '/login') {
        next()
      } else {
        next('/login')
      }
    }
  })



TÀI LIỆU: Ý NGHĨA VÀ TÁC DỤNG CÁC THƯ MỤC TRONG DỰ ÁN VUE.JS

1. api
- Ý nghĩa: Chứa các hàm hoặc module để gọi API (HTTP request).
- Tác dụng: Tách biệt logic gọi dữ liệu từ server, giúp dễ bảo trì và tái sử dụng.

2. assets
- Ý nghĩa: Chứa tài nguyên tĩnh như hình ảnh, font, file CSS chưa được xử lý.
- Tác dụng: Các file trong đây sẽ được Webpack xử lý khi build, dùng cho hình ảnh hoặc style global.

3. components
- Ý nghĩa: Chứa các component nhỏ, tái sử dụng được (ví dụ: Button, Modal).
- Tác dụng: Giúp chia nhỏ giao diện thành các phần dễ quản lý.

4. directive
- Ý nghĩa: Chứa các custom directive (ví dụ: v-focus, v-scroll).
- Tác dụng: Mở rộng khả năng của Vue directives mặc định.
- Ví dụ:
    // src/directive/focus.js
    export default {
      mounted(el) {
        el.focus();
      }
    }

    // Đăng ký directive trong main.js
    import { createApp } from 'vue';
    import App from './App.vue';
    import focusDirective from './directive/focus';

    const app = createApp(App);
    app.directive('focus', focusDirective);
    app.mount('#app');

    // Sử dụng trong template:
    <input v-focus />

5. filters
- Ý nghĩa: Chứa các hàm filter để format dữ liệu (ví dụ: format ngày, tiền tệ).
- Tác dụng: Dùng trong template để hiển thị dữ liệu theo định dạng mong muốn.

6. icons
- Ý nghĩa: Chứa các icon SVG hoặc component icon.
- Tác dụng: Quản lý icon tập trung, dễ dùng lại.

7. lang
- Ý nghĩa: Chứa file ngôn ngữ (i18n) cho đa ngôn ngữ.
- Tác dụng: Hỗ trợ dịch giao diện sang nhiều ngôn ngữ.

8. layout
- Ý nghĩa: Chứa các layout chung (ví dụ: layout cho trang có sidebar, header).
- Tác dụng: Tái sử dụng cấu trúc giao diện cho nhiều trang.

9. router
- Ý nghĩa: Chứa cấu hình router (Vue Router).
- Tác dụng: Quản lý điều hướng giữa các trang.

10. store
- Ý nghĩa: Chứa Vuex store hoặc Pinia store.
- Tác dụng: Quản lý state tập trung cho toàn bộ ứng dụng.

11. styles
- Ý nghĩa: Chứa các file CSS/SCSS global.
- Tác dụng: Định nghĩa style chung cho toàn bộ app.

12. utils
- Ý nghĩa: Chứa các hàm tiện ích (helper functions).
- Tác dụng: Tái sử dụng logic chung (ví dụ: format số, xử lý chuỗi).

13. vendor
- Ý nghĩa: Chứa các thư viện bên thứ ba hoặc custom wrapper cho thư viện.
- Tác dụng: Giúp quản lý code của bên ngoài tách biệt với code chính.

14. views
- Ý nghĩa: Chứa các component đại diện cho từng trang (Page-level components).
- Tác dụng: Mỗi file trong đây thường tương ứng với một route.

15. App.vue
- Ý nghĩa: Component gốc của ứng dụng.
- Tác dụng: Là entry point cho Vue, chứa layout chính và router-view.

```
