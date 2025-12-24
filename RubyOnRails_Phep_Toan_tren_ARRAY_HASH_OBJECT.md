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
Cách dùng hay trong mảng:
```
def skip_animals(animals, skip)
  animals.each_with_index.map{ |item, index|
    "#{index}:#{item}"
}.drop(skip)
end
```
### 👉 each_with_index:
- Cho thêm index
- Trả về chính array ban đầu, KHÔNG tạo mảng mới
### 👉 map:
- Biến đổi
- Trả về mảng mới
=> Tức là với code trên sẽ duyệt qua mãng animals biến mảng thành dạng  "#{index}:#{item}" và trả về mảng animals mới với format "#{index}:#{item} và đã drop bỏ đi số phần tử = với skip tính từ đầu mảng

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



# Ruby Array & Hash – Tổng Hợp Đầy Đủ (Giữ Nguyên Nội Dung + Ví Dụ)

Tài liệu này **giữ nguyên toàn bộ nội dung đã giải thích ở trên**, bao gồm **tất cả phép toán Array & Hash trong Ruby / Ruby on Rails kèm ví dụ cụ thể**, dùng để học, tra cứu nhanh hoặc làm README.

---

# 📦 ARRAY – TẤT CẢ PHÉP TOÁN (KÈM VÍ DỤ)

```ruby
arr = [10, 20, 30, 40]
```

---

## 1️⃣ Truy cập phần tử

```ruby
arr[0]        # => 10
arr[-1]       # => 40
arr.first     # => 10
arr.last      # => 40
arr[1, 2]     # => [20, 30]
arr[1..3]     # => [20, 30, 40]
arr.at(2)     # => 30
arr.fetch(10, 0) # => 0 (an toàn)
```

---

## 2️⃣ Thêm / Xóa phần tử

```ruby
arr << 50           # => [10, 20, 30, 40, 50]
arr.push(60)        # => [10, 20, 30, 40, 50, 60]
arr.unshift(5)      # => [5, 10, 20, 30, 40, 50, 60]

arr.pop             # => 60
arr.shift           # => 5
arr.delete(20)      # => xóa giá trị 20
arr.delete_at(1)    # => xóa index 1
arr.clear           # => []
```

---

## 3️⃣ Duyệt mảng (Iteration)

```ruby
arr.each { |x| puts x }

arr.each_with_index do |x, i|
  puts "#{i}: #{x}"
end

arr.map { |x| x * 2 }
# => [20, 40, 60, 80]

arr.map! { |x| x + 1 }
# => thay đổi trực tiếp mảng

arr.select { |x| x > 20 }
# => [30, 40]

arr.reject { |x| x > 20 }
# => [10, 20]

arr.find { |x| x > 25 }
# => 30

arr.find_index { |x| x == 30 }
# => 2

arr.any? { |x| x > 30 }
# => true

arr.all? { |x| x > 5 }
# => true

arr.none? { |x| x < 0 }
# => true

arr.count { |x| x > 15 }
# => 3
```

---

## 4️⃣ Tính toán (Giống `sum`)

```ruby
arr.sum
# => 100

arr.sum { |x| x * 2 }
# => 200

arr.max
# => 40

arr.min
# => 10

arr.max_by { |x| -x }
arr.min_by { |x| x }

arr.reduce(0) { |s, x| s + x }
# => 100
reduce = gộp nhiều phần tử → 1 giá trị duy nhất

Ví dụ:

[1,2,3,4] → 10

[1,2,3,4] → 24
+++++++++++++++++++++++
Ví dụ nâng cao về reduce
reduce(1, :*) nghĩa là sao?
Cú pháp đầy đủ:
reduce(initial_value, symbol)
Trong đó:
initial_value = 1 → giá trị ban đầu
:* → phép nhân

============================

arr.inject(:+)
# => 100



{a:1,b:2} → "a=1,b=2"

```

---

## 5️⃣ Sắp xếp

```ruby
arr.sort
# => [10, 20, 30, 40]

arr.sort.reverse
# => [40, 30, 20, 10]

users = [{ name: "A", age: 30 }, { name: "B", age: 20 }]

users.sort_by { |u| u[:age] }
# => [{age:20}, {age:30}]

users.sort_by { |u| -u[:age] }
# => giảm dần
```

---

## 6️⃣ Kiểm tra & tìm kiếm

```ruby
arr.include?(20)  # => true
arr.empty?        # => false
arr.size          # => 4
arr.length        # => 4
arr.index(30)     # => 2
```

---

## 7️⃣ Biến đổi mảng

```ruby
[1, nil, 2].compact
# => [1, 2]

[1, 1, 2].uniq
# => [1, 2]

[[1, 2], [3, 4]].flatten
# => [1, 2, 3, 4]

[1, [2, [3]]].flatten
# => [1, 2, 3]

[1, 2, 3].join("-")
# => "1-2-3"

[1, 2].zip([3, 4])
# => [[1, 3], [2, 4]]

[[1, 2], [3, 4]].transpose
# => [[1, 3], [2, 4]]
```

---

## 8️⃣ Phép toán tập hợp

```ruby
a = [1, 2, 3]
b = [3, 4]

a + b   # => [1, 2, 3, 3, 4]
a - b   # => [1, 2]
a & b   # => [3]
a | b   # => [1, 2, 3, 4]
a * 2   # => [1, 2, 3, 1, 2, 3]
```

---

# 🗂️ HASH – TẤT CẢ PHÉP TOÁN (KÈM VÍ DỤ)

```ruby
h = { a: 1, b: 2, c: 3 }
```

---

## 1️⃣ Truy cập

```ruby
h[:a]            # => 1
h.fetch(:a)      # => 1
h.fetch(:x, 0)   # => 0
h.dig(:a)        # => 1
h.key(2)         # => :b
```

---

## 2️⃣ Thêm / Cập nhật / Xóa

```ruby
h[:d] = 4
# => {a:1, b:2, c:3, d:4}

h.store(:e, 5)

h.delete(:a)
# => 1

h.clear
# => {}
```

---

## 3️⃣ Duyệt Hash

```ruby
h.each do |k, v|
  puts "#{k} = #{v}"
end

h.each_key { |k| puts k }
h.each_value { |v| puts v }

h.select { |k, v| v > 1 }
# => {:b=>2, :c=>3}

h.reject { |k, v| v > 1 }
# => {:a=>1}

h.map { |k, v| "#{k}:#{v}" }
# => ["a:1", "b:2", "c:3"]
```

---

## 4️⃣ Keys / Values

```ruby
h.keys
# => [:a, :b, :c]

h.values
# => [1, 2, 3]

h.to_a
# => [[:a, 1], [:b, 2], [:c, 3]]

h.size
# => 3

h.empty?
# => false
```

---

## 5️⃣ Merge & Transform

```ruby
h.merge({ b: 20, d: 4 })
# => {:a=>1, :b=>20, :c=>3, :d=>4}

h.merge!({ b: 99 })
# => thay đổi trực tiếp

h.transform_keys { |k| k.to_s }
# => {"a"=>1, "b"=>2, "c"=>3}

h.transform_values { |v| v * 10 }
# => {:a=>10, :b=>20, :c=>30}

h.invert
# => {1=>:a, 2=>:b, 3=>:c}
```

---

## 6️⃣ Kiểm tra

```ruby
h.key?(:a)      # => true
h.value?(2)     # => true
h.has_key?(:a)  # => true
h.has_value?(5) # => false
```

---

## 7️⃣ Hash nâng cao (Rails dùng nhiều)

```ruby
params = { name: "A", age: 20, role: "admin" }

params.slice(:name, :age)
# => {:name=>"A", :age=>20}

params.except(:role)
# => {:name=>"A", :age=>20}

params.deep_symbolize_keys
params.deep_stringify_keys
```

---

# 🧠 Ghi nhớ nhanh (Rails Dev)

- Array: `map`, `select`, `sum`, `each_with_index`
- Hash: `each`, `merge`, `slice`, `transform_keys`
- Rails DB: **luôn ưu tiên** `Model.sum(:column)` thay vì `map.sum`

---

✅ **Tài liệu này giữ nguyên nội dung + ví dụ đầy đủ như đã giải thích trong chat.**

