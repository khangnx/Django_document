# CircleCI cho dự án Ruby on Rails — Hướng dẫn chi tiết

---

## . Tạo & kết nối dự án trên CircleCI 🧩

### 1. Đăng nhập vào https://circleci.com bằng tài khoản GitHub (hoặc Bitbucket).  
### 2. Chọn tab Projects → tìm dự án Rails của bạn → bấm Set Up Project. CircleCI sẽ tự tạo một branch test (vd. circleci-project-setup) và file .circleci/config.yml mẫu.

---

## 2. Xây dựng file .circleci/config.yml

Bạn có thể sử dụng ví dụ từ repo demo chính thức của CircleCI dành cho Rails:

Sử dụng Orb Ruby & Node do CircleCI cung cấp:
Sử dụng Docker image cimg/ruby:2.7-node, môi trường postgres, chạy build/test, parallelization…
```
version: 2.1
orbs:
  ruby: circleci/ruby@1.1.0
  node: circleci/node@2

jobs:
  build:
    docker:
      - image: cimg/ruby:2.7-node
    steps:
      - checkout
      - ruby/install-deps
      - node/install-packages:
          pkg-manager: yarn
          cache-key: "yarn.lock"

  test:
    parallelism: 3
    docker:
      - image: cimg/ruby:2.7-node
      - image: circleci/postgres:9.5-alpine
        environment:
          POSTGRES_USER: circleci-demo-ruby
          POSTGRES_DB: rails_blog_test
          POSTGRES_PASSWORD: ""
      environment:
        BUNDLE_JOBS: "3"
        BUNDLE_RETRY: "3"
        PGHOST: 127.0.0.1
        PGUSER: circleci-demo-ruby
        PGPASSWORD: ""
        RAILS_ENV: test
    steps:
      - checkout
      - ruby/install-deps
      - node/install-packages:
          pkg-manager: yarn
          cache-key: "yarn.lock"
      - run:
          name: Wait for DB
          command: dockerize -wait tcp://localhost:5432 -timeout 1m
      - run:
          name: Setup DB
          command: bundle exec rails db:schema:load --trace
      - ruby/rspec-test
      - ruby/rubocop-check

workflows:
  build_and_test:
    jobs:
      - build
      - test:
          requires:
            - build

```

## 3. Giải thích các phần chính
```
- Orbs: gói sẵn các cài đặt Ruby và Node (Yarn). Tiết kiệm công sức cấu hình.
- Docker images:
  - cimg/ruby:2.7-node: chứa Ruby + Node cần thiết cho Rails + Webpacker.
  - circleci/postgres:9.5-alpine: cung cấp PostgreSQL cho môi trường test.
- Caching dependencies:
  - Ruby: ruby/install-deps
  - Node: node/install-packages với caching theo yarn.lock
- Parallel test: job test chạy với parallelism: 3 để đẩy nhanh tốc độ test.
- Database setup:
  - Docker dịch vụ PostgreSQL và chờ service khởi động với dockerize.
  - Dùng bundle exec rails db:schema:load để nạp schema test.
```

## 4. Ví dụ nâng cao từ BigBinary Academy

Dưới đây là ví dụ config cập nhật, phù hợp Rails 6–7, sử dụng thêm Redis + SQLite:
```
version: 2.1
orbs:
  node: circleci/node@5.0.3

jobs:
  build:
    docker:
      - image: cimg/ruby:3.3.5
      - image: cimg/redis:5.0.14
      - image: cimg/postgres:9.6
        environment:
          POSTGRES_HOST_AUTH_METHOD: trust
    working_directory: ~/app-name
    environment:
      RAILS_ENV: test
    steps:
      - checkout
      - node/install:
          install-yarn: true
          node-version: "22.13"
      - run: gem install bundler:2.2.20
      - run: sudo apt-get update && sudo apt-get install -y libsqlite3-dev zlib1g-dev
      - run: bundle check --path=vendor/bundle || bundle install --path=vendor/bundle --jobs=4 --retry=3
      - run: yarn install --cache-folder vendor/node_modules
      - run: bundle exec rubocop
      - run: npx prettier "…"
      - run: cp config/database.yml.ci config/database.yml
      - run: bundle exec rails db:setup
      - run: bundle exec rails test

```

## 5. Tóm tắt luồng CI/CD
```
1. Push code/pull request → CircleCI tự kích hoạt.
2. Build job: cài dependencies Ruby/Node, cache gem/yarn, lint code.
3. Test job: chạy song song qua nhiều executor, thiết lập DB/test service, chạy RSpec/Minitest, kiểm tra chất lượng code.
4. (Tùy chọn) thêm deploy, security scan, code coverage…

```

## 6. Tài nguyên & link tham khảo

- Repo mẫu: CircleCI Public — circleci-demo-ruby-rails: https://github.com/CircleCI-Public/circleci-demo-ruby-rails
- Hướng dẫn cấu hình job + parallel tests, caching & DB từ blog CircleCI
- Hướng dẫn từ BigBinary Academy: chi tiết setup CI cho Rails

---

## ✅ Kết luận

Bạn đã có:
- Cách kết nối dự án Rails với CircleCI.
- Mẫu config.yml đầy đủ để build & test.
- Cách sử dụng Orbs, Docker, caching, parallel test và lint.
- Hướng tùy chỉnh thêm Redis, SQLite, lint tools,...

Bạn có thể điều chỉnh theo version Ruby/Rails hoặc CI workflow riêng.



# CircleCI + GitHub hoàn toàn có thể chạy các test UI sử dụng Chrome + Selenium (hoặc Chromedriver) và Headless Chrome (Silicon hay Chromium headless) để test giao diện web.

## Giải thích chi tiết:

### CircleCI tích hợp với GitHub

 - Bạn push code lên GitHub → CircleCI sẽ trigger pipeline tự động.

- CircleCI có thể chạy container (Docker) hoặc VM Linux/Windows/macOS để thực hiện job.

### Chrome / Chromium / Headless

- Trên CircleCI, bạn có thể cài Chrome hoặc Chromium headless.

- Selenium hoặc Puppeteer có thể kết nối để chạy các test UI.

### Ví dụ config .circleci/config.yml cho UI test với Chrome
```
version: 2.1
jobs:
  test-ui:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - run:
          name: Install dependencies
          command: |
            sudo apt-get update
            sudo apt-get install -y unzip xvfb libxi6 libgconf-2-4
            wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
            sudo dpkg -i google-chrome-stable_current_amd64.deb || sudo apt-get -f install -y
      - run:
          name: Run Selenium tests
          command: |
            xvfb-run -a npm run test-ui
workflows:
  version: 2
  ui-tests:
    jobs:
      - test-ui

```
 - xvfb-run giúp chạy Chrome headless trên môi trường Linux không có GUI.

- Nếu bạn dùng macOS executor trên CircleCI, bạn có thể chạy Chrome trực tiếp, hỗ trợ Apple Silicon nếu cần test trên macOS.

### Các lưu ý

- Phiên bản Chrome / ChromeDriver phải tương thích.

- Nếu dùng container Linux, cần cài thêm các thư viện phụ trợ (libnss3, libxss1, …) để Chrome chạy headless.

- CircleCI hỗ trợ Docker, Windows, macOS executor → bạn có thể test UI trên nhiều nền tảng.

## ✅ Kết luận: CircleCI + GitHub hoàn toàn hỗ trợ test UI với Chrome/Selenium/Headless Chrome, kể cả môi trường headless hoặc Apple Silicon.
