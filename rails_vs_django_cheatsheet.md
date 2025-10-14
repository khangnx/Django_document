# 📘 So sánh Ruby on Rails (ActiveRecord) vs Django ORM với PostgreSQL


## 🔍 1. Khai báo Model và Table

| Tính năng | Ruby on Rails (ActiveRecord) | Django ORM |
|----------|-------------------------------|------------|
| Khai báo model | `class User < ApplicationRecord` | `class User(models.Model):` |
| Tên bảng | Tự động suy ra từ tên model (số nhiều) | Tự động suy ra từ tên model (số ít) |
| Gán tên bảng thủ công | `self.table_name = "my_users"` | `class Meta: db_table = "my_users"` |

---

## 🔗 2. Khai báo Quan hệ (Associations)

| Quan hệ | Rails | Django |
|--------|-------|--------|
| One-to-many | `has_many :posts` / `belongs_to :user` | `user = models.ForeignKey(User, on_delete=models.CASCADE)` |
| One-to-one | `has_one :profile` / `belongs_to :user` | `profile = models.OneToOneField(Profile, on_delete=models.CASCADE)` |
| Many-to-many | `has_and_belongs_to_many :tags` hoặc `has_many :tags, through: :taggings` | `tags = models.ManyToManyField(Tag)` |

---

## 📥 3. Truy vấn dữ liệu (Querying)

| Mục tiêu | Rails | Django |
|----------|-------|--------|
| Lấy tất cả | `User.all` | `User.objects.all()` |
| Lấy 1 bản ghi | `User.find(1)` | `User.objects.get(id=1)` |
| Điều kiện | `User.where(name: "Khang")` | `User.objects.filter(name="Khang")` |
| LIKE | `User.where("name LIKE ?", "%Khang%")` | `User.objects.filter(name__icontains="Khang")` |
| Sắp xếp | `User.order(:name)` | `User.objects.order_by("name")` |
| Giới hạn | `User.limit(5)` | `User.objects.all()[:5]` |
| Đếm | `User.count` | `User.objects.count()` |

---

## 🧠 4. Scope / Custom Query

| Rails | Django |
|-------|--------|
| `scope :active, -> { where(active: true) }` | `@classmethod def active(cls): return cls.objects.filter(active=True)` |

---

## 🛠️ 5. Migration

| Rails | Django |
|-------|--------|
| Tạo model | `rails generate model User name:string` | `python manage.py startapp users` + tạo model thủ công |
| Tạo migration | `rails db:migrate` | `python manage.py makemigrations` |
| Apply migration | `rails db:migrate` | `python manage.py migrate` |

---

## 🧪 6. Seed / Insert dữ liệu

| Rails | Django |
|-------|--------|
| Tạo bản ghi | `User.create(name: "Khang")` | `User.objects.create(name="Khang")` |
| Seed file | `db/seeds.rb` | Tự viết script hoặc dùng `loaddata` |

---

## 🔐 7. Kết nối PostgreSQL

| Rails | Django |
|-------|--------|
| Gem | `pg` | `psycopg2-binary` |
| Cấu hình | `config/database.yml` | `settings.py > DATABASES` |

---

## 🔁 8. Transaction (Giao dịch)

| Tính năng | Ruby on Rails | Django |
|----------|----------------|--------|
| Bắt đầu transaction | `ActiveRecord::Base.transaction do ... end` | `with transaction.atomic(): ...` |
| Rollback thủ công | `raise ActiveRecord::Rollback` | `transaction.set_rollback(True)` |
| Nested transaction | `requires_new: true` | `savepoint=True` |

---

## 🧾 9. Raw SQL (Truy vấn SQL thuần)

| Tính năng | Ruby on Rails | Django |
|----------|----------------|--------|
| Truy vấn SQL | `User.find_by_sql("SELECT * FROM users")` | `User.objects.raw("SELECT * FROM users")` |
| Truy vấn thủ công | `ActiveRecord::Base.connection.execute("SQL")` | `from django.db import connection` + `cursor = connection.cursor()` |
| Lấy kết quả | `result.each do |row| puts row["name"] end` | `for row in cursor.fetchall(): print(row[0])` |
| Truy vấn có tham số | `connection.exec_query("SELECT * FROM users WHERE id = $1", "SQL", [[nil, 1]])` | `cursor.execute("SELECT * FROM users WHERE id = %s", [1])` |

---

## 🧱 10. Indexing (Tạo chỉ mục)

| Tính năng | Ruby on Rails | Django |
|----------|----------------|--------|
| Tạo index cơ bản | `add_index :users, :email` | `email = models.CharField(max_length=100, db_index=True)` |
| Index nhiều cột | `add_index :users, [:first_name, :last_name]` | `class Meta: indexes = [models.Index(fields=['first_name', 'last_name'])]` |
| Unique index | `add_index :users, :email, unique: true` | `email = models.EmailField(unique=True)` |
| Partial index | `add_index :users, :email, where: "deleted_at IS NULL"` | `class Meta: indexes = [models.Index(fields=['email'], name='email_idx', condition=Q(deleted_at=None))]` |
| Full-text search index | `execute "CREATE INDEX ... USING gin(to_tsvector(...))"` | `GinIndex(fields=['content'])` |

---

## 🚀 11. Performance Tuning (Tối ưu hiệu năng)

| Kỹ thuật | Ruby on Rails | Django |
|---------|----------------|--------|
| Eager Loading | `User.includes(:posts).all` | `select_related('profile')`, `prefetch_related('posts')` |
| Chọn trường cần thiết | `User.select(:id, :name)` | `only('id', 'name')`, `values('id', 'name')` |
| Batching | `find_in_batches(batch_size: 1000)` | `iterator()` |
| Raw SQL | `ActiveRecord::Base.connection.execute("SQL")` | `cursor.execute("SQL")` |
| Explain query | `User.where(...).explain` | `User.objects.filter(...).explain()` |
| Caching | `Rails.cache.fetch(...)` | `django.core.cache` |
| Connection Pooling | `pgbouncer` + `database.yml` | `pgbouncer` + `settings.py` |
| Query Logging | `ActiveRecord::Base.logger` | `LOGGING` trong `settings.py` |
