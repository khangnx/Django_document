
# 📘 Cheatsheet So sánh Hàm Xử Lý Ruby on Rails vs Python (Django/Flask)

---

## 🔤 Xử lý Chuỗi (String)

| Tác vụ                          | Ruby on Rails (`String`)                  | Python (`str`) – Django/Flask         |
|----------------------------------|-------------------------------------------|----------------------------------------|
| Tách chuỗi từ phải (`rsplit`)   | `str.split('_')` (không có `rsplit`)     | `filename.rsplit('.', 1)`             |
| Tách chuỗi từ trái              | `str.split('_')`                         | `str.split('_')`                       |
| Kiểm tra bắt đầu/kết thúc       | `str.start_with?("abc")` / `end_with?`   | `str.startswith("abc")` / `endswith()`|
| Thay thế chuỗi                  | `str.gsub("a", "b")`                     | `str.replace("a", "b")`                |
| Loại bỏ khoảng trắng            | `str.strip` / `str.lstrip` / `str.rstrip`| `str.strip()` / `lstrip()` / `rstrip()`|
| Đếm số lần xuất hiện            | `str.count("a")`                         | `str.count("a")`                       |
| Tìm vị trí chuỗi con            | `str.index("abc")`                       | `str.find("abc")`                      |
| Chuyển chuỗi thành mảng ký tự   | `str.chars`                              | `list(str)`                            |

---

## 🧮 Xử lý Mảng / Danh sách

| Tác vụ                          | Ruby on Rails (`Array`)                  | Python (`list`) – Django/Flask         |
|----------------------------------|-------------------------------------------|----------------------------------------|
| Duyệt mảng                      | `array.each do |x| ... end`             | `for x in list:`                       |
| Lọc phần tử                     | `array.select { |x| x > 5 }`            | `list(filter(lambda x: x > 5, list))`  |
| Biến đổi phần tử                | `array.map { |x| x * 2 }`               | `list(map(lambda x: x * 2, list))`     |
| Sắp xếp                         | `array.sort`                            | `sorted(list)`                         |
| Loại bỏ trùng lặp               | `array.uniq`                            | `list(set(list))`                      |
| Tách phần tử cuối (`pop`)       | `array.pop`                             | `list.pop()`                           |
| Tách phần tử đầu (`shift`)      | `array.shift`                           | `list.pop(0)`                          |
| Ghép mảng                       | `array1 + array2`                       | `list1 + list2`                        |
| Xóa phần tử                     | `array.delete("x")`                     | `list.remove("x")`                     |
| Đếm phần tử                     | `array.size`                            | `len(list)`                            |
| Kiểm tra tồn tại                | `array.include?("x")`                   | `"x" in list`                          |
| Chuyển mảng thành chuỗi         | `array.join(",")`                       | `",".join(list)`                       |

---

## 📅 Xử lý Ngày tháng

| Tác vụ                          | Ruby on Rails (`Date`, `Time`)           | Python (`datetime`) – Django/Flask     |
|----------------------------------|-------------------------------------------|----------------------------------------|
| Lấy ngày hiện tại               | `Date.today` / `Time.now`               | `datetime.date.today()` / `datetime.datetime.now()` |
| Tạo đối tượng ngày              | `Date.new(2025, 10, 17)`                 | `datetime.date(2025, 10, 17)`          |
| Tạo đối tượng thời gian         | `Time.new(2025, 10, 17, 14, 30)`         | `datetime.datetime(2025, 10, 17, 14, 30)` |
| Định dạng ngày                  | `date.strftime("%d/%m/%Y")`             | `date.strftime("%d/%m/%Y")`            |
| Cộng/trừ ngày                   | `date + 5.days`                         | `date + timedelta(days=5)`             |
| Chuyển chuỗi thành ngày         | `Date.parse("2025-10-17")`              | `datetime.strptime("2025-10-17", "%Y-%m-%d")` |
| So sánh ngày                    | `date1 > date2`                         | `date1 > date2`                        |
| Tính khoảng cách ngày           | `(date1 - date2).to_i`                  | `(date1 - date2).days`                 |

---

## 📁 Xử lý File

| Tác vụ                          | Ruby on Rails (`File`)                   | Python (`open`) – Django/Flask         |
|----------------------------------|-------------------------------------------|----------------------------------------|
| Đọc file                        | `File.read("file.txt")`                  | `open("file.txt").read()`              |
| Ghi file                        | `File.write("file.txt", "abc")`          | `open("file.txt", "w").write("abc")`   |
| Đọc từng dòng                   | `File.foreach("file.txt")`               | `with open("file.txt") as f: for line in f:` |
| Tách tên file và phần mở rộng   | `File.basename("file.txt", ".txt")`      | `filename.rsplit('.', 1)`              |
| Kiểm tra tồn tại                | `File.exist?("file.txt")`                | `os.path.exists("file.txt")`           |
| Lấy tên file từ đường dẫn       | `File.basename(path)`                    | `os.path.basename(path)`               |
| Lấy thư mục chứa file           | `File.dirname(path)`                     | `os.path.dirname(path)`                |
