# 📘 Tổng hợp kiến thức Swagger (OpenAPI) trong Ruby on Rails

---

## ✅ 1. Swagger là gì?

Swagger (nay gọi là **OpenAPI Specification**) là một chuẩn để mô tả REST API dưới dạng **file YAML hoặc JSON**. Nó giúp:

- Định nghĩa **endpoint**, **method**, **request**, **response**, **schema**.
- Tạo tài liệu API tự động.
- Kiểm tra tính hợp lệ của request/response (ví dụ với `assert_schema_conform`).


## ✅ 2. Cấu trúc cơ bản của Swagger (OpenAPI)

Một file Swagger thường có các phần chính:

```
openapi: 3.0.0
info:
  title: My API
  version: 1.0.0

servers:
  - url: http://localhost:3000

paths:
  /users:
    get:
      summary: Lấy danh sách người dùng
      responses:
        '200':
          description: Thành công
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'

    post:
      summary: Tạo người dùng
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UserCreate'
      responses:
        '201':
          description: Đã tạo
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
      required:
        - id
        - name
        - email

    UserCreate:
      type: object
      properties:
        name:
          type: string
        email:
          type: string
      required:
        - name
        - email
```
# ✅ 3. Cách dùng Swagger trong Rails

```

gem 'committee'
gem 'committee-rails'
- Trong rails_helper.rb:
Committee.configure do |config|
  config.schema_path = Rails.root.join('docs', 'openapi.yaml')
end
- Viết test với assert_schema_conform
RSpec.describe 'Users API', type: :request do
  it 'DELETE /users/:id trả về 204 và đúng schema' do
    user = create(:user)
    delete "/users/#{user.id}"
    expect(response).to have_http_status(:no_content)
    assert_schema_conform(204) # kiểm tra theo schema trong openapi.yaml
  end
end
```
## ✅ 4. Chạy Swagger UI
Để hiển thị tài liệu API đẹp:

Cài gem:
```

gem 'rswag-api'
gem 'rswag-ui'
gem 'rswag-specs'
- Mount route trong routes.rb:

mount Rswag::Ui::Engine => '/api-docs'
mount Rswag::Api::Engine => '/api-docs'
 - Truy cập: http://localhost:3000/api-docs
```
