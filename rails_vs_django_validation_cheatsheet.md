# 🛡️ Cheatsheet So sánh Validation giữa Ruby on Rails và Django

---

## ✅ Validation cơ bản

| Loại kiểm tra | Ruby on Rails | Django |
|---------------|----------------|--------|
| Bắt buộc (presence) | `validates :name, presence: true` | `name = models.CharField(..., blank=False)` |
| Duy nhất (uniqueness) | `validates :email, uniqueness: true` | `email = models.EmailField(unique=True)` |
| Độ dài | `validates :bio, length: { minimum: 10, maximum: 300 }` | `bio = models.TextField(validators=[MinLengthValidator(10), MaxLengthValidator(300)])` |
| Định dạng (regex) | `validates :phone, format: { with: /\A\d{10}\z/ }` | `phone = models.CharField(validators=[RegexValidator(r'^\d{10}$')])` |
| Số (numericality) | `validates :age, numericality: { only_integer: true, greater_than: 0 }` | `age = models.IntegerField(validators=[MinValueValidator(1)])` |
| Inclusion | `validates :status, inclusion: { in: %w[active inactive] }` | `status = models.CharField(validators=[validate_status])` |
| Exclusion | `validates :role, exclusion: { in: %w[admin superuser] }` | Tự viết validator loại trừ |

---

## 📝 Validation ở Form

| Mục tiêu | Ruby on Rails | Django |
|----------|----------------|--------|
| Form validation | Dùng `ActiveModel::Validations` hoặc `form_with` | Dùng `forms.Form` hoặc `forms.ModelForm` |
| Ví dụ | `form_with model: @user` | `class UserForm(forms.ModelForm): ...` |
| Custom clean method | `validate :custom_check` | `def clean_<field>()` hoặc `def clean()` |
| Hiển thị lỗi | `form.errors.full_messages` | `form.errors` hoặc `form[field].errors` |

---

## 🎨 Hiển thị lỗi trong Template

| Ruby on Rails | Django |
|---------------|--------|
| `<%= form.errors.full_messages %>` | `{% for error in form.field.errors %}{{ error }}{% endfor %}` |
| `<%= form.field_name %>` tự động highlight lỗi | `{{ form.field }}` tự động thêm class lỗi |
| Dùng `error_messages_for` helper | Dùng `form.non_field_errors` và `form[field].errors` |

---

## 🧠 Custom Validation nâng cao

| Ruby on Rails | Django |
|---------------|--------|
| Tạo method `validate :check_logic` | Override `clean()` trong `ModelForm` hoặc `Model` |
| Dùng `validate_each` cho từng field | Dùng `def clean_<field>()` |
| Tạo validator riêng | Tạo class kế thừa `Validator` hoặc dùng `@deconstructible` |
| Gắn validator vào field | `validates_with CustomValidator` | `validators=[CustomValidator()]` |

---

## 📌 Ghi chú

- Rails validation nằm trong model, còn Django có thể nằm ở model hoặc form.
- Django hỗ trợ `clean()` để kiểm tra logic tổng thể.
- Cả hai framework đều hỗ trợ custom validator và hiển thị lỗi trong template.

