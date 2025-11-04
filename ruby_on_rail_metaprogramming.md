
# TÓM TẮT CHỦ ĐỀ: METAPROGRAMMING TRONG RUBY

## 1. Mọi thứ trong Ruby on Rails đều là object
- Ruby là ngôn ngữ hướng đối tượng thuần túy.
- Mọi thứ đều là object: số (Integer), chuỗi (String), nil (NilClass), true/false (TrueClass, FalseClass).
- Rails kế thừa triết lý này: controller, model, view đều là class và instance.
```
Ví dụ:
  5.class        # => Integer
  "hello".class  # => String
  nil.class      # => NilClass
```
## 2. Khái niệm Metaprogramming
- Là kỹ thuật cho phép chương trình tự tạo hoặc thay đổi hành vi của chính nó trong lúc runtime.
- Ruby hỗ trợ mạnh mẽ metaprogramming, giúp viết mã ngắn gọn, linh hoạt.

## 3. Ví dụ Metaprogramming
```
- define_method – Tạo phương thức động
  class Hello
    [:vietnam, :japan].each do |country|
      define_method("say_hello_to_#{country}") do
        puts "Hello, #{country.capitalize}!"
      end
    end
  end

### Cách gọi như sau:

hello = Hello.new
hello.say_hello_to_vietnam
==> Kết quả sẽ in ra:Hello, Vietnam!
```
- method_missing – Bắt phương thức chưa định nghĩa
  class DynamicHello
    def method_missing(method_name, *args)
      if method_name.to_s.start_with?("say_hello_to_")
        country = method_name.to_s.split("_").last
        puts "Hello, #{country.capitalize}!"
      else
        super
      end
    end
  end
```
```
- send – Gọi phương thức bằng tên
  class Greet
    def hello
      "Hello!"
    end
  end

  g = Greet.new
  puts g.send(:hello)
```
## 4. Ảnh hưởng đến hiệu suất
- Tăng chi phí xử lý tại runtime.
- Khó tối ưu hóa bởi trình thông dịch.
- Gây khó khăn khi debug nếu lạm dụng.
```
Ví dụ:
  1000.times do
    obj.send(:some_dynamic_method)
  end
  # Có thể gây chậm nếu không cache hoặc tối ưu
```
## 5. Cách tối ưu Metaprogramming
- Ưu tiên define_method thay vì method_missing.
- Dùng respond_to_missing? để hỗ trợ tốt hơn.
- Tránh dùng eval nếu không cần thiết.
- Cache kết quả nếu gọi nhiều lần.
- Chỉ dùng khi thật sự cần thiết.
```
Ví dụ:
  class SafeDynamic
    def method_missing(name, *args)
      if name.to_s.start_with?("greet_")
        "Hello #{name.to_s.split('_').last.capitalize}"
      else
        super
      end
    end

    def respond_to_missing?(name, include_private = false)
      name.to_s.start_with?("greet_") || super
    end
  end
  ```
