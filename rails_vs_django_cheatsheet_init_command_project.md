
# 📘 Cheatsheet So sánh Ruby on Rails và Django

---

## 🆕 Tạo mới Project và App

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| Tạo project | `rails new myapp` | `django-admin startproject myapp` |
| Tạo app/module | `rails generate scaffold Product name:string` | `python manage.py startapp products` |
| Chạy server | `rails server` | `python manage.py runserver` |
| Tạo model | `rails generate model Product name:string` | Tạo class trong `models.py` |
| Tạo controller/view | `rails generate controller Products` | Tạo thủ công trong `views.py` |
| Tạo migration | Tự động khi tạo model | `python manage.py makemigrations` |
| Apply migration | `rails db:migrate` | `python manage.py migrate` |

---

## 📦 Quản lý dữ liệu

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| Tạo bản ghi | `Product.create(name: "Sách")` | `Product.objects.create(name="Sách")` |
| Lấy tất cả | `Product.all` | `Product.objects.all()` |
| Lấy theo ID | `Product.find(1)` | `Product.objects.get(id=1)` |
| Điều kiện | `Product.where(name: "Sách")` | `Product.objects.filter(name="Sách")` |
| Sắp xếp | `Product.order(:name)` | `Product.objects.order_by("name")` |
| Giới hạn | `Product.limit(5)` | `Product.objects.all()[:5]` |
| Xóa | `product.destroy` | `product.delete()` |

---

## 🧪 Test và Console

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| Console tương tác | `rails console` | `python manage.py shell` |
| Chạy test | `rails test` | `python manage.py test` |

---

## ⚙️ Cấu hình và Môi trường

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| Cấu hình DB | `config/database.yml` | `settings.py > DATABASES` |
| Cấu hình môi trường | `RAILS_ENV=production` | `DJANGO_SETTINGS_MODULE=myapp.settings` |

---

## 🔗 Routing

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| Định nghĩa route | `config/routes.rb` | `urls.py` |
| Route RESTful | `resources :products` | `path('products/', views.list)` |
| Route động | `get '/products/:id' => 'products#show'` | `path('products/<int:id>/', views.detail)` |

---

## 🧱 Template Rendering

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| File template | `.html.erb` | `.html` với Django Template Language |
| Biến trong template | `<%= product.name %>` | `{{ product.name }}` |
| Lặp danh sách | `<% @products.each do |p| %>` | `{% for product in products %}` |
| Điều kiện | `<% if p.active? %>` | `{% if product.active %}` |

---

## 🔐 Authentication

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| Hệ thống auth | `Devise` gem | `django.contrib.auth` |
| Tạo user | `User.create(...)` | `User.objects.create_user(...)` |
| Login | `sign_in(user)` | `login(request, user)` |
| Logout | `sign_out(user)` | `logout(request)` |
| Kiểm tra đăng nhập | `user_signed_in?` | `request.user.is_authenticated` |

---

## 🌐 REST API

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| Framework | `rails-api`, `jbuilder`, `active_model_serializers` | `Django REST Framework (DRF)` |
| Tạo API | `resources :products, defaults: { format: :json }` | `@api_view(['GET'])` hoặc `ModelViewSet` |
| Serializer | `ProductSerializer < ActiveModel::Serializer` | `ProductSerializer(serializers.ModelSerializer)` |
| Trả JSON | `render json: @product` | `return Response(serializer.data)` |
| Route API | `namespace :api do ... end` | `router = DefaultRouter()` |

---

## 📌 Ghi chú

- Rails dùng convention nhiều hơn, Django cho phép cấu hình linh hoạt hơn.
- Django REST Framework mạnh mẽ cho API, Rails có thể dùng thêm gem để hỗ trợ.
- Cả hai đều hỗ trợ template, routing, auth, migration, ORM mạnh mẽ.

