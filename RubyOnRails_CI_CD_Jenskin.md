
# Tài liệu quy trình CI/CD với Jenkins cho Ruby on Rails
## 1. Cài đặt Jenkins và môi trường
### 1.1 Cài đặt Jenkins

```
sudo apt update
sudo apt install openjdk-11-jdk jenkins
```

Truy cập Jenkins tại http://localhost:8080.
### 1.2 Cài đặt plugin cần thiết

```
Git Plugin
RVM Plugin
Ruby Plugin
JUnit Plugin
```
### 1.3 Cài đặt RVM và Ruby cho user Jenkins
```
sudo su - jenkins
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
curl -sSL https://get.rvm.io | bash -s stable --ruby
source ~/.rvm/scripts/rvm
rvm install 2.7.0
```

## 2. Thiết lập Freestyle Job
### 2.1 Tạo Job mới

Vào Jenkins → New Item → Freestyle project

### 2.2 Cấu hình Git

Repository URL: https://github.com/your-org/your-rails-app.git
Credentials: Thêm nếu cần
### 2.3 Build Environment

Chọn "Run the build in a RVM-managed environment"
Chọn Ruby version: 2.7.0
### 2.4 Build Steps

```
bundle install
RAILS_ENV=test bundle exec rake db:create db:migrate
RAILS_ENV=test bundle exec rspec --format RspecJunitFormatter --out results.xml

```
### 2.5 Post-build Actions

Publish JUnit test result report: results.xml

## 3. Thiết lập Pipeline với Jenkinsfile
### 3.1 Tạo file Jenkinsfile trong repo

```
pipeline {
  agent any

  environment {
    RVM_CMD = 'source /etc/profile.d/rvm.sh && rvm use 2.7.0 --create'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Setup Ruby') {
      steps {
        sh "${env.RVM_CMD}"
        sh 'gem install bundler'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'bundle install'
      }
    }

    stage('Database Setup') {
      steps {
        sh 'bundle exec rake db:create db:migrate RAILS_ENV=test'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'bundle exec rspec --format RspecJunitFormatter --out results.xml'
      }
    }

    stage('Archive Artifacts') {
      steps {
        archiveArtifacts artifacts: '**/schema.rb', fingerprint: true
      }
    }

    stage('Deploy') {
      when {
        branch 'main'
      }
      steps {
        sh 'bundle exec cap production deploy'
      }
    }
  }

  post {
    always {
      junit '**/results.xml'
    }
    failure {
      mail to: 'dev-team@example.com', subject: "Build Failed #${env.BUILD_NUMBER}", body: "Check Jenkins console output."
    }
  }
}
```
### 3.2 Giải thích các bước

```
Checkout: kéo mã nguồn từ Git.
Setup: load RVM, cài Ruby và Bundler.
Bundle: cài dependencies.
DB Migrate: tạo và migrate DB test (RAILS_ENV=test).
Test: chạy RSpec.
Archive artifacts: lưu schema.rb để debug. [the-pi-guy.com]
Deploy: deploy chỉ khi là branch main.
Post: xuất báo cáo JUnit. [stackoverflow.com], [elitedev.in]

```
4. Lưu ý

Đảm bảo Jenkins có quyền truy cập vào RVM và Ruby.
Sử dụng rspec_junit_formatter để xuất kết quả test.
Không nên chạy migrate trên production trong CI.
Có thể tích hợp thêm các bước như kiểm tra code style, bảo mật, deploy staging, v.v.
