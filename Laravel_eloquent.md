# 📘 Tài liệu tổng hợp về Eloquent ORM trong Laravel

## 1. Giới thiệu
**Eloquent ORM** là công cụ tích hợp trong Laravel để thao tác cơ sở dữ liệu theo mô hình **Active Record**.

- Mỗi bảng dữ liệu tương ứng với một **Model**
- Giúp bạn viết code PHP thay vì SQL thuần
- Hỗ trợ quan hệ, scope, soft delete, eager loading…

---

## 2. Khởi tạo Model & Migration

Tạo model kèm migration:
```bash
php artisan make:model Post -m
```

Ví dụ migration:
```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('content');
    $table->boolean('is_published')->default(false);
    $table->timestamps();
});
```

---

## 3. Mass Assignment & Bảo mật

**Mass Assignment** là việc gán dữ liệu hàng loạt bằng `create()` hoặc `update()`.

⚠️ Nguy cơ: hacker có thể chèn field không mong muốn (ví dụ `is_admin`).

### Cách bảo vệ
```php
class Post extends Model {
    protected $fillable = ['title', 'content', 'is_published']; // cho phép
    // hoặc
    protected $guarded = ['is_admin']; // không cho phép
}
```

👉 **Khuyến nghị**: dùng `$fillable` để code rõ ràng và an toàn hơn.

---

## 4. CRUD với Eloquent

### CREATE
```php
$post = Post::create([
    'title' => 'Hello World',
    'content' => 'Nội dung bài viết',
    'is_published' => true
]);
```

### READ
```php
$posts = Post::all();
$post  = Post::find(1);
```

### UPDATE
```php
$post->update([
    'title' => 'Tiêu đề mới'
]);
```

### DELETE
```php
$post->delete();
```

---

## 5. Query Scopes

### Local Scope
```php
class Post extends Model {
    public function scopePublished($query) {
        return $query->where('is_published', true);
    }
}
```

Sử dụng:
```php
$posts = Post::published()->get();
```

### Global Scope
```php
class PublishedScope implements Scope {
    public function apply(Builder $builder, Model $model) {
        $builder->where('is_published', true);
    }
}
```

👉 **Local Scope**: điều kiện tùy chọn  
👉 **Global Scope**: logic mặc định cho mọi query

---

## 6. Hiệu năng với `chunk()` và `cursor()`

### chunk()
Chia dữ liệu thành từng lô nhỏ, tránh tràn RAM:
```php
Post::chunk(100, function ($posts) {
    foreach ($posts as $post) {
        // xử lý
    }
});
```

### cursor()
Duyệt từng bản ghi, rất tiết kiệm bộ nhớ:
```php
foreach (Post::cursor() as $post) {
    // xử lý
}
```

---

## 7. Quan hệ (Relationships)

### One to One (1–1)
**Ví dụ**: User – Profile

```php
// User.php
public function profile() {
    return $this->hasOne(Profile::class);
}

// Profile.php
public function user() {
    return $this->belongsTo(User::class);
}
```

---

### One to Many (1–nhiều)
**Ví dụ**: Post – Comment

```php
// Post.php
public function comments() {
    return $this->hasMany(Comment::class);
}

// Comment.php
public function post() {
    return $this->belongsTo(Post::class);
}
```

---

### Many to Many (nhiều–nhiều)
**Ví dụ**: User – Role

```php
// User.php
public function roles() {
    return $this->belongsToMany(Role::class);
}

// Role.php
public function users() {
    return $this->belongsToMany(User::class);
}
```

👉 Laravel sử dụng **pivot table**, ví dụ: `role_user`

---

### Polymorphic (đa hình)
**Ý nghĩa**: Một model có thể liên kết với nhiều model khác nhau qua cùng một quan hệ.
**Ví dụ**: Comment ↔ Post / Video

```php
// Comment.php
public function commentable() {
    return $this->morphTo();
}

// Post.php
public function comments() {
    return $this->morphMany(Comment::class, 'commentable');
}

// Video.php
public function comments() {
    return $this->morphMany(Comment::class, 'commentable');
}
```

---

## 8. Soft Deletes

Cho phép xóa mềm (bản ghi vẫn tồn tại trong DB).

```php
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model {
    use SoftDeletes;
}
```

Mặc định Eloquent sẽ **không lấy** bản ghi đã xóa mềm.

```php
Post::withTrashed()->get();
Post::onlyTrashed()->get();
```

---

## 📊 Tóm tắt nhanh

| Chủ đề | Ý nghĩa | Cách dùng |
|------|-------|---------|
| Mass Assignment | Ngăn chèn field nguy hiểm | `$fillable`, `$guarded` |
| CRUD | Thao tác dữ liệu | `create`, `find`, `update`, `delete` |
| Query Scopes | Giữ code gọn | `scopePublished()` |
| Hiệu năng | Tránh tràn RAM | `chunk()`, `cursor()` |
| Relationships | Quan hệ bảng | `hasOne`, `hasMany`, `belongsToMany`, `morphMany` |
| Soft Deletes | Xóa mềm | `use SoftDeletes` |

---

📌 *Tài liệu phù hợp cho người mới học Laravel đến Middle level.*
