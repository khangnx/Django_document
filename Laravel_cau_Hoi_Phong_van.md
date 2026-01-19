# 📘 Tài liệu về Eloquent và Bảo mật API trong Laravel

---

## 1. Eloquent Accessors và Mutators

### 🔹 Accessors

Định nghĩa cách **lấy dữ liệu** từ cột trong database.

```php
public function getNameAttribute($value)
{
    return ucfirst($value);
}
```

### 🔹 Mutators

Định nghĩa cách **ghi dữ liệu** vào database.

```php
public function setNameAttribute($value)
{
    $this->attributes['name'] = strtolower($value);
}
```

👉 Giúp **chuẩn hóa dữ liệu** khi lưu và hiển thị.

---

## 2. Mass Assignment trong Eloquent

* Cho phép gán nhiều thuộc tính cùng lúc khi tạo hoặc cập nhật model.

```php
User::create([
    'name' => 'Nguyen Van A',
    'email' => 'nguyen@example.com'
]);
```

* Để bảo mật, Laravel dùng `$fillable` hoặc `$guarded`:

```php
protected $fillable = ['name', 'email'];
```

👉 Ngăn chặn việc gán dữ liệu **không mong muốn** (Mass Assignment Vulnerability).

---

## 3. Eager Loading trong Eloquent

* **Lazy Loading**
  Truy vấn dữ liệu liên quan khi cần, dễ gây **N+1 problem**.

* **Eager Loading**
  Tải trước dữ liệu liên quan bằng `with()`:

```php
$users = User::with('posts')->get();
```

### ✅ Lợi ích

* Giảm số lượng query
* Tăng hiệu năng khi làm việc với dữ liệu liên quan

---

## 4. Quan hệ trong Eloquent

### 🔹 One-to-Many

```php
public function posts()
{
    return $this->hasMany(Post::class);
}

$user->posts;
```

### 🔹 Many-to-Many

```php
public function roles()
{
    return $this->belongsToMany(Role::class);
}

$user->roles;
```

👉 Eloquent cung cấp cú pháp **rõ ràng và dễ đọc** để định nghĩa và truy vấn quan hệ.

---

## 5. Query Scopes trong Eloquent

### 🔹 Local Scope

```php
public function scopeActive($query)
{
    return $query->where('status', 'active');
}

User::active()->get();
```

### 🔹 Global Scope

```php
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Scope;

class ActiveScope implements Scope
{
    public function apply(Builder $builder, Model $model)
    {
        $builder->where('status', 'active');
    }
}
```

```php
class User extends Model
{
    protected static function booted()
    {
        static::addGlobalScope(new ActiveScope);
    }
}
```

```php
$users = User::all();
```

👉 Mọi query mặc định chỉ lấy user có `status = 'active'`.

---

## 6. Best Practices bảo mật API trong Laravel

* Authentication:

  * Laravel Passport
  * Laravel Sanctum
  * JWT

* Authorization:

  * Gate
  * Policy

### 🔐 Bảo mật bổ sung

* HTTPS
* Rate limiting
* Validation dữ liệu đầu vào
* Ẩn thông tin nhạy cảm trong API response

---

## 7. Chiến lược tối ưu truy vấn Database

* Dùng **Eager Loading** để tránh N+1
* Tạo **Index** cho các cột hay query
* **Paginate** thay vì lấy toàn bộ dữ liệu
* Cache dữ liệu (Redis, Memcached)
* Dùng `select()` để chỉ lấy cột cần thiết
* Tránh query phức tạp trong vòng lặp

---

## 8. Phòng chống lỗ hổng bảo mật

* **SQL Injection**
  Eloquent và Query Builder tự động bind parameter

* **XSS**
  Dùng:

  ```blade
  {{ $variable }}
  ```

* **CSRF**
  Laravel tự động thêm token:

  ```blade
  @csrf
  ```

* **Validation**
  Luôn validate dữ liệu đầu vào

* **CORS & Rate Limiting**
  Ngăn chặn request không mong muốn và spam

---

## 📌 Tóm lại

* **Eloquent ORM** cung cấp:

  * Accessors & Mutators
  * Mass Assignment
  * Eager Loading
  * Relationships
  * Query Scopes (Local & Global)

* **Laravel API** hỗ trợ đầy đủ:

  * Authentication & Authorization
  * Tối ưu hiệu năng
  * Phòng chống SQL Injection, XSS, CSRF

🚀 Rất phù hợp cho **phỏng vấn Laravel** và **xây dựng API thực tế**
