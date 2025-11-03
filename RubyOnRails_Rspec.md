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
