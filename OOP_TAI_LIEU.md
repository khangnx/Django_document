# 🧠 Tóm tắt nhanh về OOP (Object-Oriented Programming)

**OOP (Object-Oriented Programming – Lập trình hướng đối tượng)**  
Là phương pháp lập trình dựa trên **lớp (class)** và **đối tượng (object)**, giúp code:
- Dễ quản lý
- Dễ tái sử dụng
- Dễ bảo trì và mở rộng

OOP có **4 đặc tính chính**:
1. Đóng gói (Encapsulation)
2. Kế thừa (Inheritance)
3. Đa hình (Polymorphism)
4. Trừu tượng hóa (Abstraction)

---

## 🧩 OOP là gì?

- **OOP**: phương pháp lập trình tập trung vào *đối tượng* thay vì chỉ xử lý logic và hàm.
- **Đối tượng (Object)**: đại diện cho một thực thể cụ thể, gồm:
  - Thuộc tính (*attributes*)
  - Phương thức (*methods*)
- **Lớp (Class)**: khuôn mẫu để tạo ra các đối tượng.

### 📌 Ví dụ
- Lớp `XeHoi` có các thuộc tính:
  - `mauSac`
  - `dongCo`
  - `soChoNgoi`
- Đối tượng `xeToyota` là một instance của `XeHoi`:
  - `mauSac = đỏ`
  - `dongCo = xăng`
  - `soChoNgoi = 5`

---

## 🔑 4 đặc tính cơ bản của OOP

| Đặc tính | Mô tả ngắn | Ví dụ |
|--------|-----------|------|
| **Đóng gói** | Che giấu chi tiết bên trong, chỉ cung cấp cách truy cập an toàn | `soTienTaiKhoan`, `rutTien()`, `napTien()` |
| **Kế thừa** | Lớp con kế thừa thuộc tính & phương thức lớp cha | `Cho` kế thừa `DongVat` |
| **Đa hình** | Cùng phương thức, hành vi khác nhau | `phatTiengKeu()` của chó & mèo |
| **Trừu tượng hóa** | Định nghĩa khái niệm chung, không quan tâm chi tiết | `HinhHoc.tinhDienTich()` |

---

## 💻 Ví dụ minh họa bằng Python

```python
# Lớp cha
class DongVat:
    def __init__(self, ten):
        self.ten = ten
    
    def phatTiengKeu(self):
        pass  # phương thức trừu tượng

# Lớp con kế thừa
class Cho(DongVat):
    def phatTiengKeu(self):
        return "Gâu gâu"

class Meo(DongVat):
    def phatTiengKeu(self):
        return "Meo meo"

# Tạo đối tượng
cho = Cho("Chó nhà")
meo = Meo("Mèo mun")

print(cho.phatTiengKeu())  # Output: Gâu gâu
print(meo.phatTiengKeu())  # Output: Meo meo



# 👉 Phân tích ví dụ

## Kế thừa
Cho và Meo kế thừa từ lớp cha DongVat

## Đa hình
Cùng phương thức phatTiengKeu() nhưng mỗi loài phát ra âm thanh khác nhau

## Trừu tượng hóa
Lớp DongVat chỉ định nghĩa hành vi chung, không cài đặt chi tiết

## Đóng gói
Thuộc tính ten được quản lý bên trong đối tượng

# ✅ Kết luận ngắn gọn

OOP giúp mô hình hóa thế giới thực vào code, làm cho chương trình dễ hiểu – dễ mở rộng – dễ bảo trì hơn.
