
Phần 1: So sánh double vs instance_double trong RSpec

| Tiêu chí                | double                              | instance_double                          |
|-------------------------|-------------------------------------|------------------------------------------|
| Định nghĩa             | Tạo đối tượng giả không ràng buộc  | Tạo đối tượng giả dựa trên class cụ thể |
| Kiểm tra phương thức   | Không kiểm tra                     | Kiểm tra phương thức có tồn tại trong class |
| Khi nào dùng           | Khi chỉ cần đối tượng giả đơn giản | Khi muốn đảm bảo tính đúng đắn với class thực |

Ví dụ:
# double
```
user = double("User", name: "Alice")
puts user.name  # => "Alice"
```

# instance_double
```
class User
  def name; end
end
user = instance_double(User, name: "Alice")
puts user.name  # => "Alice"
```

Phần 2: So sánh stub vs spy trong RSpec

| Tiêu chí                | stub                                | spy                                     |
|-------------------------|-------------------------------------|-----------------------------------------|
| Định nghĩa             | Thay thế phương thức bằng giá trị  | Theo dõi phương thức được gọi          |
| Kiểm tra gọi phương thức| Không                              | Có thể kiểm tra số lần gọi và tham số  |
| Khi nào dùng           | Khi chỉ cần trả về giá trị giả     | Khi muốn kiểm tra hành vi gọi phương thức |

Ví dụ:
# stub
```
allow(user).to receive(:name).and_return("Alice")
puts user.name  # => "Alice"
```
```
✅ 1. Stub là gì trong RSpec?

Stub = thay thế một method thật bằng bản giả trong lúc chạy test.

Ví dụ: method UserService.get_user_info gọi tới API ngoài → bạn muốn stub nó để trả về giá trị giả.

✅ 2. Cách stub API trong RSpec (không dùng gem ngoài)
Ví dụ service:
class UserService
  def self.get_user_info(id)
    HTTParty.get("https://api.example.com/users/#{id}")
  end
end

Stub trong RSpec:
RSpec.describe UserService do
  it "returns stubbed response" do
    fake_response = { "name" => "Khang", "age" => 20 }

    allow(HTTParty).to receive(:get)
      .with("https://api.example.com/users/1")
      .and_return(fake_response)

    result = UserService.get_user_info(1)
    expect(result["name"]).to eq("Khang")
  end
end


Giải thích:

allow(...).to receive(...) → intercept lời gọi thật

.and_return(fake_response) → trả về data giả

API thật không hề được gọi

✅ 3. Stub API “chuẩn” nhất với WebMock + RSpec

WebMock là gem phổ biến chuyên thiết kế để mock HTTP request.

Cài:
# Gemfile
group :test do
  gem 'webmock'
end

Ví dụ:
require 'webmock/rspec'

RSpec.describe UserService do
  it "stubs external API" do
    stub_request(:get, "https://api.example.com/users/1")
      .to_return(
        status: 200,
        body: { name: "Khang", age: 20 }.to_json,
        headers: { "Content-Type" => "application/json" }
      )

    result = UserService.get_user_info(1)
    expect(result["name"]).to eq("Khang")
  end
end

🔥 4. Khi nào cần stub API?
Tình huống	Stub?
Test logic trong ứng dụng	✔️ Nên stub
Test endpoint nội bộ	✔️ Nên stub external API
Integration test thực sự gọi API	❌ Không stub
Load test, performance test	❌ Không stub
```

# spy
```
user = spy("User")
user.name("Alice")
expect(user).to have_received(:name).with("Alice")
```

```
Trong RSpec, “tạo spy API” nghĩa là giám sát (track) lời gọi API xem:

method có được gọi không,

được gọi bao nhiêu lần,

được gọi với tham số gì.

Spy khác stub:

Stub → thay thế kết quả trả về.

Spy → theo dõi xem method đã được gọi hay chưa (có thể kèm stub hoặc không).

✅ 1. Spy trong RSpec là gì?

spy trong RSpec là một test double có khả năng ghi nhận lại lời gọi method.

Dùng để kiểm tra side effects hoặc logic điều hướng.

✅ 2. Ví dụ đơn giản (không dùng API thật)
Code:
class Notifier
  def self.send_request
    HTTParty.get("https://api.example.com/ping")
  end
end

Spy API call:
RSpec.describe Notifier do
  it "calls the API" do
    # tạo spy cho HTTParty
    api_spy = spy("HTTParty")

    # thay thế HTTParty bằng spy
    stub_const("HTTParty", api_spy)

    Notifier.send_request

    expect(api_spy).to have_received(:get)
      .with("https://api.example.com/ping")
  end
end


Ý nghĩa:

Không quan tâm kết quả API trả về

Chỉ xác nhận API đã được gọi đúng URL

✅ 3. Spy + Stub API cùng lúc

Dùng khi bạn muốn vừa theo dõi, vừa trả về fake data.

api_spy = spy("HTTParty", get: { "status" => "ok" })

stub_const("HTTParty", api_spy)

Notifier.send_request

expect(api_spy).to have_received(:get)

✅ 4. Spy kiểu RSpec built-in (không dùng stub_const)

Bạn có thể spy trực tiếp trên object thật:

RSpec.describe Notifier do
  it "calls external API" do
    allow(HTTParty).to receive(:get).and_call_original # hoặc .and_return(data giả)

    Notifier.send_request

    expect(HTTParty).to have_received(:get)
      .with("https://api.example.com/ping")
  end
end


have_received chỉ dùng sau khi method đã được gọi.

🔥 5. Spy khác WebMock kiểu nào?
Phương pháp	Dùng để	Theo dõi call?	Chặn API thật?
Stub (allow)	Trả về fake data	❌	✔️
Spy (have_received)	Kiểm tra method có gọi không	✔️	❌/✔️ (tuỳ stub)
WebMock	Chặn và mô phỏng HTTP	✔️	✔️
📌 Kết luận ngắn

Spy API trong RSpec = tạo test double để theo dõi xem API được gọi ra sao.
Thường dùng để kiểm tra logic “có gọi API hay không” hơn là nội dung trả về.
```
