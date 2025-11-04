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

```
