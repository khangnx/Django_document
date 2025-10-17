# HƯỚNG DẪN ĐẦY ĐỦ CÁC CÁCH ĐỊNH NGHĨA ROUTES TRONG RUBY ON RAILS
===============================================================

## 1. Basic Route
```
Cú pháp:
    get 'home/index'
Giải thích: Khi người dùng truy cập /home/index, Rails sẽ gọi HomeController#index.
Ví dụ:
    URL: http://localhost:3000/home/index
    Controller: app/controllers/home_controller.rb
    Action: def index
```
## 2. Root Route
```
Cú pháp:
    root 'home#index'
Giải thích: Định nghĩa trang chủ của ứng dụng.
Ví dụ:
    Truy cập http://localhost:3000/ sẽ gọi HomeController#index
```
## 3. HTTP Verb Routing
```
Cú pháp:
    get 'products/:id' => 'products#show'
    post 'products' => 'products#create'
    put 'products/:id' => 'products#update'
    patch 'products/:id' => 'products#update'
    delete 'products/:id' => 'products#destroy'
Giải thích: Tạo route tương ứng với các phương thức HTTP.
Ví dụ:
    GET /products/1 → ProductsController#show
    POST /products → ProductsController#create
```
## 4. Named Routes
```
Cú pháp:
    get 'login', to: 'sessions#new', as: 'login'
Giải thích: Tạo route /login và đặt tên là login_path.
Ví dụ:
    Trong view: <%= link_to 'Đăng nhập', login_path %>
```
## 5. Resourceful Routing
```
Cú pháp:
    resources :products
Giải thích: Tạo đầy đủ 7 routes chuẩn RESTful cho controller.
Ví dụ:
    GET /products → index
    GET /products/:id → show
    GET /products/new → new
    POST /products → create
    GET /products/:id/edit → edit
    PATCH/PUT /products/:id → update
    DELETE /products/:id → destroy
```
## 6. Nested Resources
```
Cú pháp:
    resources :articles do
      resources :comments
    end
Giải thích: Comments thuộc về Articles.
Ví dụ:
    GET /articles/1/comments → CommentsController#index
```
## 7. Shallow Nesting
```
Cú pháp:
    resources :articles do
      resources :comments, shallow: true
    end
Giải thích: Giảm độ sâu URL cho các action như show, edit, update, destroy.
Ví dụ:
    GET /comments/1 → CommentsController#show
```
## 8. Only / Except
```
Cú pháp:
    resources :products, only: [:index, :show]
    resources :orders, except: [:destroy]
Giải thích: Tùy chỉnh các action được tạo.
Ví dụ:
    products chỉ có index và show
    orders có tất cả trừ destroy
```
## 9. Collection và Member
```
Cú pháp:
    resources :photos do
      collection do
        get 'search'
      end
      member do
        get 'preview'
      end
    end
Giải thích:
    collection: không cần id
    member: cần id
Ví dụ:
    GET /photos/search → PhotosController#search
    GET /photos/1/preview → PhotosController#preview
```
## 10. Concerns
```
Cú pháp:
    concern :commentable do
      resources :comments
    end
    resources :posts, concerns: :commentable
    resources :videos, concerns: :commentable
Giải thích: Tái sử dụng đoạn route.
Ví dụ:
    GET /posts/1/comments
    GET /videos/1/comments
```
## 11. Namespace
```
Cú pháp:
    namespace :admin do
      resources :users
    end
Giải thích: Tạo route /admin/users, ánh xạ đến Admin::UsersController.
Ví dụ:
    GET /admin/users → Admin::UsersController#index
```
## 12. Scope
```
Cú pháp:
    scope '/admin' do
      resources :users
    end
Giải thích: Tạo route /admin/users nhưng controller vẫn là UsersController.
Ví dụ:
    GET /admin/users → UsersController#index
```
## 13. Constraints
```
Cú pháp:
    get 'photos/:id', to: 'photos#show', constraints: { id: /\d+/ }
Giải thích: Giới hạn id phải là số.
Ví dụ:
    GET /photos/abc → Không hợp lệ
    GET /photos/123 → Hợp lệ
    ```

## 14. Redirect
```
Cú pháp:
    get '/old', to: redirect('/new')
Giải thích: Chuyển hướng từ /old sang /new.
Ví dụ:
    Truy cập /old sẽ tự động chuyển sang /new
```
## 15. Match (tùy chọn verb)
```
Cú pháp:
    match 'about', to: 'pages#about', via: [:get, :post]
Giải thích: Cho phép nhiều verb cùng ánh xạ đến một action.
Ví dụ:
    GET /about → PagesController#about
    POST /about → PagesController#about
```
