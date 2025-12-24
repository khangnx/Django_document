📘 Ruby / Ruby on Rails – Phép toán trên Array, Hash, Object
# Ruby / Ruby on Rails – Phép toán trên Array, Hash, Object

Tài liệu tổng hợp **đầy đủ các phép toán thường dùng** trên **Array, Hash, Object** trong Ruby/Rails, kèm **ví dụ thực tế**.

---

## 1. Array (Mảng)

### 1.1 Truy cập & cắt mảng
```ruby
arr = [10, 20, 30, 40, 50]

arr[0]        # 10
arr[-1]       # 50
arr[1..3]     # [20, 30, 40]
arr[2..-1]    # [30, 40, 50]

arr.first     # 10
arr.last      # 50
arr.take(2)   # [10, 20]
arr.drop(2)   # [30, 40, 50]
```
### 1.2 Thêm / xóa phần tử
```
arr = [1, 2, 3]

arr << 4
arr.push(5)
arr.unshift(0)

arr.pop
arr.shift
arr.delete(2)
arr.delete_at(1)
```
### 1.3 Duyệt & xử lý
```
arr = [1, 2, 3, 4]

arr.map { |x| x * 2 }
arr.select { |x| x.even? }
arr.reject { |x| x < 3 }
arr.find { |x| x > 2 }
```
### 1.4 Biến đổi
```
[3,1,2].sort
[1,nil,2].compact
[[1,2],[3]].flatten
[1,1,2,3].uniq
```

### 1.5 Phép toán tập hợp
```
[1,2,3] + [4,5]
[1,2,3] - [2]
[1,2,3] & [2,3,4]
[1,2,3] | [3,4]
```
### 1.6 Lấy size của Array (mảng)
```
arr.size    # 4
arr.length  # 4
arr.count   # 4
Khi có điều kiện thì dùng count
arr.count { |x| x.even? }
```
## 2. Hash
### 2.1 Truy cập & gán
```
hash = { name: "Khang", age: 30 }

hash[:name]
hash[:email] = "a@gmail.com"
```
### 2.2 Duyệt Hash
```
hash.each do |k, v|
  puts "#{k}: #{v}"
end
```
### 2.3 Gộp / xóa
```
h1 = { a: 1, b: 2 }
h2 = { b: 3, c: 4 }
h1.merge(h2)
h1.merge!(h2)
h1.delete(:a)
```
### 2.4 Lọc & biến đổi
```
hash.select { |k,v| v > 20 }
hash.reject { |k,v| k == :age }

hash.transform_keys(&:to_s)
hash.transform_values(&:to_s)
```
### 2.5 Giá trị mặc định
```
counter = Hash.new(0)
counter[:apple] += 1
counter[:apple] += 1
```
### 2.6 Lấy size của hash 
```
hash.size    # 3
hash.length  # 3
hash.count   # 3
Khi có điều kiện thì dùng count
hash.count { |k, v| v > 1 }
# => 2
```
## 3. Object
### 3.1 Kiểm tra
```
obj.class
obj.is_a?(User)
obj.respond_to?(:email)
```
### .2 Instance variables
```
obj.instance_variables
obj.instance_variable_get(:@name)
obj.instance_variable_set(:@name, "New Name")
```

### 3.3 Chuyển đổi
```
obj.to_s
obj.as_json
obj.to_json
```
### 3.4 So sánh
```
a == b
a.eql?(b)
a.equal?(b)
```
##4. Enumerable
```
arr.reduce(:+)
arr.sum
arr.group_by(&:status)
arr.partition(&:even?)
```
## 5. Rails-specific
```
nil.blank?
"".present?
params.slice(:name, :email)
params.except(:password)
hash.deep_symbolize_keys
```
## 6. Ví dụ Rails
```
Controller
ids = params[:ids].map(&:to_i).uniq

Model
users.group_by(&:role)
```
## 7. Ghi nhớ nhanh

- Array: danh sách

- Hash: key-value

- Enumerable: xử lý dữ liệu

- Rails helpers: code gọn & sạch
