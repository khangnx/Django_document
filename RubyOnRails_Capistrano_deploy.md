
# Triển khai Ruby on Rails với Capistrano + Puma
## 1. Giới thiệu ban đầu
### 1.1 Thêm gem vào Gemfile

```
group :development do
  gem 'capistrano', '~> 3.17', require: false
  gem 'capistrano-rails', '~> 1.6', require: false
  gem 'capistrano-rvm', '~> 0.2.0', require: false
  gem 'capistrano-bundler', '~> 2.0', require: false
  gem 'capistrano3-puma', '~> 7.1', require: false
end

bundle install
```

### 1.2 Khởi tạo cấu hình Capistrano
 
 ```
bundle exec cap install


Sinh ra các file:

Capfile
config/deploy.rb
config/deploy/production.rb
config/deploy/staging.rb (nếu cần)

```


## 2. Cấu hình Capfile

```
require 'capistrano/setup'
require 'capistrano/deploy'
require 'capistrano/rvm'
require 'capistrano/bundler'
require 'capistrano/rails'
require 'capistrano/puma'
install_plugin Capistrano::Puma::Systemd

```

## 3. Cấu hình config/deploy.rb
```
set :application, 'your_app_name'
set :repo_url, 'git@github.com:your-org/your-rails-app.git'
set :deploy_to, "/var/www/#{fetch(:application)}"

append :linked_files, 'config/database.yml', 'config/master.key', 'config/puma.rb'
append :linked_dirs, 'log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'vendor/bundle', 'public/system'

set :rvm_type, :user
set :rvm_ruby_version, '2.7.0'

set :puma_bind, "unix://#{shared_path}/tmp/sockets/puma.sock"
set :puma_state, "#{shared_path}/tmp/pids/puma.state"
set :puma_pid, "#{shared_path}/tmp/pids/puma.pid"
set :puma_access_log, "#{shared_path}/log/puma_access.log"
set :puma_error_log, "#{shared_path}/log/puma_error.log"

set :puma_roles, [:app]
set :puma_env, fetch(:rails_env)
set :puma_threads, [4,16]
set :puma_workers, 2
```


## 4. Cấu hình config/deploy/production.rb
```
server 'your.server.ip', user: 'deploy', roles: %w{app db web}

set :branch, 'main'
set :rails_env, 'production'

```

# 5. Tạo file config/puma.rb
```
threads 4, 16
workers 2
app_dir = File.expand_path('..', __dir__)
shared_dir = "#{app_dir}/../shared"

bind "unix://#{shared_dir}/tmp/sockets/puma.sock"
pidfile "#{shared_dir}/tmp/pids/puma.pid"
state_path "#{shared_dir}/tmp/pids/puma.state"
stdout_redirect "#{shared_dir}/log/puma_access.log", "#{shared_dir}/log/puma_error.log", true

preload_app!

on_worker_boot do
  require 'active_record'
  ActiveRecord::Base.establish_connection if defined?(ActiveRecord)
end

```

## 6. Cài đặt Puma systemd trên server
cap production puma:systemd:config


Sinh ra file systemd cho user deploy để quản lý Puma.


## 7. Các lệnh hỗ trợ Capistrano + Puma

```
Lệnh	Mục đích
cap production deploy	Triển khai ứng dụng
cap production deploy:rollback	Quay lại bản trước nếu lỗi
cap production puma:restart	Khởi động lại Puma
cap production puma:reload	Reload Puma không downtime
cap production puma:start / stop / status	Quản lý dịch vụ Puma
```


## 8. Cấu hình Nginx proxy Puma
```
server {
  listen 80;
  server_name your.domain.com;
  root /var/www/your_app_name/current/public;

  location / {
    try_files $uri @app;
  }

  location @app {
    proxy_pass http://unix:/var/www/your_app_name/shared/tmp/sockets/puma.sock;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
  }

  error_page 500 502 503 504 /500.html;
}
```


## 9. Kết luận

Capistrano + Puma là sự lựa chọn tối ưu cho triển khai Rails production:

Tự động hoá nhanh chóng, rollback dễ dàng
Hỗ trợ triển khai nhiều môi trường
Quản lý Puma qua systemd
