# 👍 Mình giải thích nhé: “Custom validation rule” nghĩa là bạn tự định nghĩa ra một quy tắc kiểm tra dữ liệu thay vì chỉ dùng những rule có sẵn (như required, email, min, max…). Nó thường xuất hiện trong các framework như Laravel, Spring, hoặc khi bạn viết form validation trong JavaScript.

## 🌟 Cách dùng Custom Validation Rule (ví dụ Laravel)
- Tạo rule mới
Chạy lệnh artisan:
```
php artisan make:rule Uppercase
```
- Nó sẽ tạo một class Uppercase trong thư mục App\Rules.
- Viết logic kiểm tra trong rule
```
namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;

class Uppercase implements Rule
{
    public function passes($attribute, $value)
    {
        return strtoupper($value) === $value;
    }

    public function message()
    {
        return 'The :attribute must be uppercase.';
    }
}
```
- Áp dụng rule trong form request hoặc controller
```
use App\Rules\Uppercase;

$request->validate([
    'name' => ['required', new Uppercase],
]);
```
## 🌟 Trong JavaScript (ví dụ với form validation)Bạn có thể viết hàm riêng:
```
function isUppercase(value) {
  return value === value.toUpperCase();
}

function validateForm() {
  const name = document.getElementById("name").value;
  if (!isUppercase(name)) {
    alert("Name must be uppercase!");
    return false;
  }
  return true;
}
```
## 📌 Ý tưởng chung- Custom rule = bạn viết hàm/logic riêng để kiểm tra dữ liệu.
- Cách dùng = gắn rule đó vào chỗ validate (form, request, input).
- Lợi ích = linh hoạt, phù hợp với nghiệp vụ đặc thù (ví dụ: số điện thoại VN, mã nhân viên, định dạng đặc biệt…).
