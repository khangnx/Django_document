
# RuboCop trong Ruby on Rails - Tài liệu tổng hợp

---

## 1. 🛡️ RuboCop là gì?

RuboCop là công cụ phân tích tĩnh mã nguồn Ruby giúp:
- Phát hiện lỗi tiềm ẩn.
- Áp dụng chuẩn coding style Ruby.
- Tự động sửa lỗi (autocorrect).
- Duy trì chất lượng mã nguồn.

---

## 2. 🚀 RuboCop dùng trong Ruby on Rails như thế nào?

Cài thêm gem mở rộng:
gem 'rubocop-rails', require: false

rubocop-rails bổ sung các quy tắc đặc thù cho Rails:
- Kiểm tra ActiveRecord, controller, view, migration.
- Gợi ý cách viết code Rails chuẩn hơn.

Ví dụ:
- Cảnh báo khi dùng flash[:notice] trước render.
- Nhắc nhở thêm dependent: :destroy cho has_many.

---

## 3. ⚙️ Cài đặt và cấu hình
```
Gemfile:
group :development, :test do
  gem 'rubocop', require: false
  gem 'rubocop-rails', require: false
end

Chạy:
bundle install

Tạo file .rubocop.yml:
require:
  - rubocop-rails

AllCops:
  TargetRubyVersion: 3.2
  TargetRailsVersion: 7.1

Rails:
  Enabled: true

```

## 4. 🧪 Cách chạy RuboCop

- Kiểm tra toàn bộ dự án:
  rubocop

- Kiểm tra một file cụ thể:
  rubocop app/models/user.rb

- Tự động sửa lỗi:
  rubocop -A

---

## 5. 📏 Chuẩn hóa code với RuboCop

- Viết code đồng nhất.
- Giảm bugs.
- Tối ưu hóa hiệu suất.
- Tích hợp dễ dàng với CI/CD.

---

# 6. ✅ Tích hợp RuboCop vào CI/CD

GitHub Actions (.github/workflows/rubocop.yml):
```
name: RuboCop
on: [push, pull_request]
jobs:
  rubocop:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.2'
          bundler-cache: true
      - run: bundle install
      - run: bundle exec rubocop

GitLab CI (.gitlab-ci.yml):

rubocop:
  image: ruby:3.2
  script:
    - bundle install
    - bundle exec rubocop
  only:
    - merge_requests
    - main

CircleCI (.circleci/config.yml):

version: 2.1
jobs:
  rubocop:
    docker:
      - image: cimg/ruby:3.2
    steps:
      - checkout
      - run: bundle install
      - run: bundle exec rubocop
workflows:
  version: 2
  test:
    jobs:
      - rubocop

```

## 7. 🧩 Dùng pre-commit hook với RuboCop
```
Cách 1: Git hook thủ công

Tạo file .git/hooks/pre-commit:
#!/bin/sh
echo "🔍 Running RuboCop..."
bundle exec rubocop
if [ $? -ne 0 ]; then
  echo "❌ RuboCop failed. Commit aborted."
  exit 1
fi
echo "✅ RuboCop passed. Proceeding with commit."

Cách 2: Dùng gem overcommit

Gemfile:
gem 'overcommit'

Cài đặt:
bundle install
bundle exec overcommit --install

Tạo file .overcommit.yml:
PreCommit:
  RuboCop:
    enabled: true
    command: ['bundle', 'exec', 'rubocop']
    on_warn: fail

Ký hook:
bundle exec overcommit --sign

```

## 8. 📚 Tài nguyên tham khảo

- https://docs.rubocop.org/rubocop/
- https://docs.rubocop.org/rubocop-rails/
- https://github.com/rubocop/rubocop-rails
- https://docs.rubocop.org/rubocop-rails/cops_rails.html
