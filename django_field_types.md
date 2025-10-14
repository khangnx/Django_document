# 🧠 Tổng hợp các kiểu trường trong Django ORM

Django ORM cung cấp nhiều kiểu trường để định nghĩa các thuộc tính của mô hình (`models.Model`). Dưới đây là bảng tổng hợp các kiểu trường phổ biến, được phân loại theo nhóm:

---

## 🔤 Trường văn bản

| Kiểu trường     | Mô tả                                              | Ví dụ |
|------------------|----------------------------------------------------|-------|
| `CharField`      | Chuỗi ngắn, cần khai báo `max_length`.             | `name = models.CharField(max_length=100)` |
| `TextField`      | Chuỗi dài, không giới hạn độ dài.                  | `description = models.TextField()` |
| `SlugField`      | Chuỗi URL-friendly (thường dùng cho đường dẫn).    | `slug = models.SlugField(unique=True)` |
| `EmailField`     | Chuỗi email, có kiểm tra định dạng.               | `email = models.EmailField()` |
| `URLField`       | Chuỗi URL, có kiểm tra định dạng.                 | `website = models.URLField()` |
| `UUIDField`      | Lưu UUID (mã định danh duy nhất).                 | `uuid = models.UUIDField(default=uuid.uuid4)` |

---

## 🔢 Trường số

| Kiểu trường             | Mô tả                                           | Ví dụ |
|--------------------------|--------------------------------------------------|-------|
| `IntegerField`           | Số nguyên.                                       | `age = models.IntegerField()` |
| `PositiveIntegerField`   | Số nguyên dương.                                 | `quantity = models.PositiveIntegerField()` |
| `SmallIntegerField`      | Số nguyên nhỏ.                                   | `rating = models.SmallIntegerField()` |
| `BigIntegerField`        | Số nguyên lớn.                                   | `views = models.BigIntegerField()` |
| `FloatField`             | Số thực (float).                                 | `price = models.FloatField()` |
| `DecimalField`           | Số thập phân chính xác cao.                      | `amount = models.DecimalField(max_digits=10, decimal_places=2)` |

---

## 📅 Trường ngày giờ

| Kiểu trường       | Mô tả                          | Ví dụ |
|--------------------|-------------------------------|-------|
| `DateField`        | Ngày (yyyy-mm-dd).            | `birth_date = models.DateField()` |
| `TimeField`        | Giờ (hh:mm:ss).               | `start_time = models.TimeField()` |
| `DateTimeField`    | Ngày và giờ.                  | `created_at = models.DateTimeField(auto_now_add=True)` |
| `DurationField`    | Khoảng thời gian.             | `duration = models.DurationField()` |

---

## ✅ Trường boolean và lựa chọn

| Kiểu trường         | Mô tả                                         | Ví dụ |
|----------------------|-----------------------------------------------|-------|
| `BooleanField`       | True hoặc False.                              | `is_active = models.BooleanField(default=True)` |
| `ChoiceField`        | Dùng `choices` với `CharField` hoặc `IntegerField`. | `status = models.CharField(max_length=10, choices=STATUS_CHOICES)` |

---

## 🔗 Trường quan hệ

| Kiểu trường         | Mô tả                          | Ví dụ |
|----------------------|-------------------------------|-------|
| `ForeignKey`         | Quan hệ 1-nhiều.              | `author = models.ForeignKey(User, on_delete=models.CASCADE)` |
| `OneToOneField`      | Quan hệ 1-1.                  | `profile = models.OneToOneField(Profile, on_delete=models.CASCADE)` |
| `ManyToManyField`    | Quan hệ nhiều-nhiều.          | `tags = models.ManyToManyField(Tag)` |

---

## 🧪 Trường đặc biệt khác

| Kiểu trường     | Mô tả                                      | Ví dụ |
|------------------|--------------------------------------------|-------|
| `FileField`      | Lưu file.                                  | `document = models.FileField(upload_to='docs/')` |
| `ImageField`     | Lưu ảnh (cần cài `Pillow`).                | `avatar = models.ImageField(upload_to='avatars/')` |
| `JSONField`      | Lưu dữ liệu dạng JSON.                     | `metadata = models.JSONField()` |
| `BinaryField`    | Lưu dữ liệu nhị phân.                      | `data = models.BinaryField()` |
| `AutoField`      | Tự động tăng (thường là `id`).             | `id = models.AutoField(primary_key=True)` |
| `BigAutoField`   | Tự động tăng với số lớn.                   | `id = models.BigAutoField(primary_key=True)` |

---

## 📌 Ghi chú

- Một số trường yêu cầu thêm tham số như `max_length`, `choices`, `default`, `null`, `blank`,...
- Các trường quan hệ cần khai báo `on_delete` để xử lý khi đối tượng liên quan bị xóa.

---
