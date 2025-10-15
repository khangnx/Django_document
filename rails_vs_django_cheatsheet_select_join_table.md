# 📘 Cheatsheet: So sánh cách Load và Join bảng giữa Ruby on Rails và Python Django

---

## 1. LOAD DỮ LIỆU TỪ BẢNG

## 🔹 Ruby on Rails
  - Load tất cả bản ghi
    
  User.all

  - Load có điều kiện
    
  User.where(active: true)

  - Load một bản ghi
    
  User.find(1)

## 🔹Python Django
- Load tất cả bản ghi
  
User.objects.all()

  - Load có điều kiện
    
  User.objects.filter(active=True)

  - Load một bản ghi
    
  User.objects.get(id=1)

---

## 2. JOIN BẢNG (QUAN HỆ GIỮA CÁC BẢNG)

## 🔹 Ruby on Rails (ActiveRecord)
  - Giả sử User has_many :posts
    
  User.joins(:posts).where(posts: { published: true })

  - Eager loading để tránh N+1
    
  User.includes(:posts).where(posts: { published: true })

## 🔹 Python Django (ORM)
  - Giả sử User có quan hệ ForeignKey với Post
    
  User.objects.filter(post__published=True)

  - Select related (join)
    
  User.objects.select_related('post').filter(post__published=True)

  - Prefetch related (eager loading nhiều bản ghi)
    
  User.objects.prefetch_related('posts').filter(posts__published=True)


## 3. SO SÁNH NHANH

| Tính năng           | Ruby on Rails (ActiveRecord) | Python Django (ORM)         |
|---------------------|------------------------------|------------------------------|
| Load tất cả         | Model.all                    | Model.objects.all()          |
| Điều kiện           | Model.where(...)             | Model.objects.filter(...)    |
| Join bảng           | joins(:assoc)                | select_related()             |
| Eager loading       | includes(:assoc)             | prefetch_related()           |
| Truy xuất 1 bản ghi | Model.find(id)               | Model.objects.get(id=id)     |
