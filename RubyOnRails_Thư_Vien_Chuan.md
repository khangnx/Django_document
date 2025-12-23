# 📚 Thư viện Toán & Chuỗi trong Ruby (Tương tự Prime)

Tài liệu này tổng hợp **các thư viện toán học và xử lý chuỗi trong
Ruby**, bao gồm **thư viện chuẩn (standard library)** và **gem phổ
biến**, thường dùng trong **Ruby on Rails** và **phỏng vấn**.

------------------------------------------------------------------------

## I. 📐 THƯ VIỆN TOÁN (Math / Number)

### 1. `Math` (Standard Library)

``` ruby
Math.sqrt(16)        # => 4
Math.log(10)
Math.sin(Math::PI)
```

**Dùng khi:** - Căn bậc hai - Logarithm - Lượng giác

------------------------------------------------------------------------

### 2. `Rational`, `Complex`

``` ruby
Rational(1, 3)   # => (1/3)
Complex(2, 3)    # => (2+3i)
```

**Dùng khi:** - Cần độ chính xác phân số - Xử lý số phức

------------------------------------------------------------------------

### 3. `BigDecimal` (Rất quan trọng trong Rails)

``` ruby
require 'bigdecimal'
BigDecimal("0.1") + BigDecimal("0.2")
```

**Dùng khi:** - Tính tiền - Tránh lỗi làm tròn của Float

------------------------------------------------------------------------

### 4. `Matrix`

``` ruby
require 'matrix'

m = Matrix[[1, 2], [3, 4]]
m.det   # => -2
```

**Chủ yếu dùng cho học thuật**

------------------------------------------------------------------------

### 5. `Enumerable` (CỰC KỲ QUAN TRỌNG)

``` ruby
[1, 2, 3, 4].select(&:even?)
[1, 2, 3].map { |x| x * 2 }
```

**Cung cấp:** - `map` - `select` - `reject` - `reduce`

> `Prime.lazy` hoạt động dựa trên `Enumerable` + `Enumerator`

------------------------------------------------------------------------

### 6. `ActiveSupport::Numeric` (Rails)

``` ruby
5.days
2.hours.ago
10.minutes.from_now
```

**Toán + thời gian trong Rails**

------------------------------------------------------------------------

## II. 🔤 THƯ VIỆN CHUỖI (String / Text)

### 1. `String` (Core Ruby)

``` ruby
"hello".reverse
"ruby".upcase
"rails".include?("ai")
```

------------------------------------------------------------------------

### 2. `Regexp` (Rất mạnh)

``` ruby
/email/.match?("a@b.com")
"abc123".gsub(/\d/, "")
```

**Dùng cho:** - Validate - Tìm kiếm - Thay thế chuỗi

------------------------------------------------------------------------

### 3. `UnicodeUtils`

``` ruby
require 'unicode_utils/upcase'
UnicodeUtils.upcase("đặng")
```

**Xử lý Unicode nâng cao**

------------------------------------------------------------------------

### 4. `I18n`

``` ruby
I18n.transliterate("Đặng Văn A")
# => "Dang Van A"
```

**Dùng cho:** - Chuẩn hóa chuỗi - Search - Slug URL

------------------------------------------------------------------------

### 5. `ActiveSupport::Inflector`

``` ruby
"users".singularize      # => "user"
"order_item".camelize    # => "OrderItem"
```

**Dùng cho:** - Convention Rails - Tên bảng, class, file

------------------------------------------------------------------------

### 6. Gem `stringex`

``` ruby
"Tiếng Việt có dấu".to_url
# => "tieng-viet-co-dau"
```

**Dùng cho SEO / slug**

------------------------------------------------------------------------

## III. 🔄 TOÁN & CHUỖI KẾT HỢP

### `Date`, `Time`, `DateTime`

``` ruby
Time.now + 2.days
Date.parse("2025-12-23")
```

------------------------------------------------------------------------

### `Digest` (Chuỗi → Hash)

``` ruby
require 'digest'
Digest::SHA256.hexdigest("password")
```

**Dùng cho:** - Hash - Checksum - Bảo mật

------------------------------------------------------------------------

### `SecureRandom`

``` ruby
require 'securerandom'
SecureRandom.hex(16)
```

**Sinh chuỗi ngẫu nhiên**

------------------------------------------------------------------------

## IV. 🧠 BẢNG SO SÁNH NHANH

  Mục đích       Thư viện
  -------------- --------------
  Số nguyên tố   `Prime`
  Toán học       `Math`
  Tiền tệ        `BigDecimal`
  Toán tập hợp   `Enumerable`
  Chuỗi          `String`
  Regex          `Regexp`
  Chuỗi Rails    `Inflector`
  Unicode        `I18n`
  Slug SEO       `stringex`
  Hash           `Digest`

------------------------------------------------------------------------

## V. 🎯 KẾT LUẬN (Rails Thực Tế)

-   **Toán**: `Math`, `BigDecimal`, `Enumerable`
-   **Chuỗi**: `String`, `Regexp`, `Inflector`, `I18n`
-   **SEO / URL**: `stringex`
-   **Bảo mật**: `Digest`, `SecureRandom`

------------------------------------------------------------------------

📌 *Tài liệu phù hợp cho:* - Ruby on Rails developer - Ôn phỏng vấn -
Ghi chú học tập
