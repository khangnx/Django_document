# Tổng hợp kiến thức RSpec trong Ruby on Rails

## ✅ 1. Các ngữ cảnh trong RSpec: describe, context, it
### 🔹 describe

Dùng để nhóm các test liên quan đến một đối tượng hoặc hành vi cụ thể.
Thường dùng cho class, method, hoặc API endpoint.
Có thể lồng nhiều describe để phân cấp.

Ví dụ :
```

RSpec.describe User, type: :model do
  describe '#full_name' do
    it 'trả về tên đầy đủ' do
      user = User.new(first_name: 'Khang', last_name: 'Nguyen')
      expect(user.full_name).to eq('Khang Nguyen')
    end
  end
end

```
#### 🔹 context

Dùng để mô tả điều kiện hoặc trạng thái khác nhau trong cùng một hành vi.
Giúp phân biệt các tình huống như "khi hợp lệ" và "khi không hợp lệ".

Ví dụ:

```
describe '#valid?' do
  context 'khi email hợp lệ' do
    it 'trả về true' do
      user = User.new(email: 'test@example.com')
      expect(user.valid?).to be true
    end
  end

  context 'khi email không hợp lệ' do
    it 'trả về false' do
      user = User.new(email: 'invalid')
      expect(user.valid?).to be false
    end
  end
end
```
### 🔹 it

Dùng để định nghĩa một test case cụ thể.
Nên viết mô tả rõ ràng về hành vi mong đợi.

```
it 'tăng số lượng người dùng sau khi tạo' do
  expect { User.create!(name: 'Khang') }.to change(User, :count).by(1)
end
```
## ✅ 2. Các loại expect trong RSpec
expect là cách kiểm tra giá trị thực tế so với giá trị mong đợi. Một số matcher phổ biến:

### 🔸 So sánh giá trị
```
expect(actual).to eq(expected)       # bằng nhau
expect(actual).not_to eq(expected)   # không bằng nhau
```
### 🔸 Kiểm tra kiểu hoặc trạng thái
```
expect(actual).to be_a(String)       # là kiểu String
expect(actual).to be_truthy          # true hoặc giá trị truthy
expect(actual).to be_falsey          # false hoặc nil
```
### 🔸 Kiểm tra thay đổi
```
expect { action }.to change(Model, :count).by(1)
```
### 🔸 Kiểm tra bao gồm
```

expect(array).to include('item')
expect(hash).to include(key: 'value')

```
### 🔸 Kiểm tra regex
```
expect('hello').to match(/he/)
```
### 🔸 Kiểm tra raise exception
```
expect { raise 'error' }.to raise_error(RuntimeError)
```
###  Kiểm tra HTTP response (trong request spec)
```

expect(response).to have_http_status(:ok)
expect(response.body).to include('success')

```


# Cách viết Kiểm thử cho validations trong Rails bằng RSpec

## 1. Sử dụng gem `shoulda-matchers`

Đây là cách **phổ biến nhất** vì ngắn gọn và dễ đọc.

### Ví dụ: Model `Article` có validations

```ruby
class Article < ApplicationRecord
  validates :title, presence: true, uniqueness: true
  validates :content, presence: true, length: { minimum: 10 }
end
```
## Test với RSpec + shoulda-matchers

```
require 'rails_helper'

RSpec.describe Article, type: :model do
  it { should validate_presence_of(:title) }
  it { should validate_uniqueness_of(:title) }
  it { should validate_presence_of(:content) }
  it { should validate_length_of(:content).is_at_least(10) }
end
```
## 2. Viết test thủ công (không dùng shoulda-matchers)

- Bạn có thể kiểm tra trực tiếp bằng cách tạo instance và gọi .valid?.

  ```
  RSpec.describe Article, type: :model do
  it "is invalid without a title" do
    article = Article.new(title: nil, content: "Nội dung hợp lệ")
    expect(article).not_to be_valid
    expect(article.errors[:title]).to include("can't be blank")
  end

  it "is invalid if content is too short" do
    article = Article.new(title: "Tiêu đề", content: "ngắn")
    expect(article).not_to be_valid
    expect(article.errors[:content])
      .to include("is too short (minimum is 10 characters)")
  end
end
```
## 🚀 Lợi ích khi test validations

- Đảm bảo dữ liệu hợp lệ: Không cho phép lưu dữ liệu sai vào database.

- Phát hiện lỗi sớm: Khi validations thay đổi, test sẽ báo lỗi ngay.

- Tăng độ tin cậy: Codebase ổn định hơn khi có test bao phủ.

## 👉 Kết luận

- Bạn có thể test validations trong Rails model bằng:

### ✅ shoulda-matchers — ngắn gọn, dễ đọc, tiện lợi

### ✅ Test thủ công — kiểm tra chi tiết với .valid? và errors
