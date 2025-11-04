```
ác component chính thường được tổ chức theo cấu trúc chuẩn của dự án. Dưới đây là giải thích chi tiết:
1. App.vue

Đây là root component (component gốc) của ứng dụng Vue.
Nó được khai báo trong main.js hoặc main.ts và được mount vào phần tử HTML có id="app".
Vai trò:

Chứa layout tổng thể của ứng dụng.
Thường bao gồm <router-view> nếu dùng Vue Router, hoặc các component con khác.
Là điểm khởi đầu để render toàn bộ ứng dụng.




2. Các component con

Được đặt trong thư mục components/.
Vai trò:

Chia nhỏ giao diện thành các phần tái sử dụng (ví dụ: Header.vue, Footer.vue, Sidebar.vue).
Mỗi component có:

template: phần HTML.
script: logic xử lý.
style: CSS cho component.






3. Layout components

Nếu ứng dụng lớn, thường có các layout như:

DefaultLayout.vue
AdminLayout.vue


Chúng chứa cấu trúc chung cho từng loại trang.


4. Page components

Thường nằm trong thư mục views/ hoặc pages/.
Vai trò:

Đại diện cho từng trang (ví dụ: Home.vue, About.vue).
Được liên kết với route trong router/index.js.




5. Main.js / Main.ts

Không phải là component, nhưng là file khởi tạo ứng dụng.
Import App.vue và mount vào DOM:
JavaScriptimport { createApp } from 'vue';
import App from './App.vue';
createApp(App).mount('#app');



✅ Tóm lại:

App.vue = Root component.
components/ = Component con tái sử dụng.
views/ = Component đại diện cho từng trang.
layouts/ = Component chứa layout tổng thể.

```
