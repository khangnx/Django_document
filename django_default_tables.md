# 🗃️ Các bảng mặc định của Django sau khi migrate

Sau khi chạy `python manage.py migrate` lần đầu, Django sẽ tạo ra một số bảng hệ thống để phục vụ cho việc xác thực, phân quyền, quản lý session và admin. Dưới đây là giải thích chi tiết từng bảng:

---
## 1. `auth_user`
- **Chức năng**: Lưu thông tin người dùng.
- **Trường chính**: `username`, `password`, `email`, `is_staff`, `is_superuser`, `last_login`, `date_joined`.
- **Ví dụ**: Dùng để xác thực người dùng khi đăng nhập.


---
## 2. `auth_group`
- **Chức năng**: Lưu thông tin nhóm người dùng.
- **Trường chính**: `name`.
- **Ví dụ**: Tạo nhóm "Biên tập viên", "Quản trị viên" để phân quyền theo nhóm.

## 3. `auth_permission`
- **Chức năng**: Lưu các quyền (permission) như `add`, `change`, `delete`, `view` cho từng model.
- **Trường chính**: `name`, `content_type`, `codename`.
- **Ví dụ**: Gán quyền `change_article` cho nhóm "Biên tập viên".


---
## 4. `auth_group_permissions`
- **Chức năng**: Quan hệ nhiều-nhiều giữa nhóm và quyền.
- **Ví dụ**: Nhóm "Quản trị viên" có quyền `add_user`, `delete_user`.

## 5. `auth_user_groups`
- **Chức năng**: Quan hệ nhiều-nhiều giữa người dùng và nhóm.
- **Ví dụ**: Người dùng A thuộc nhóm "Biên tập viên".

## 6. `auth_user_user_permissions`
- **Chức năng**: Quan hệ nhiều-nhiều giữa người dùng và quyền riêng lẻ.
- **Ví dụ**: Người dùng B có quyền `delete_comment` mà không cần thuộc nhóm.


---
## 7. `django_admin_log`
- **Chức năng**: Ghi lại lịch sử thao tác trong trang admin.
- **Trường chính**: `action_time`, `user`, `content_type`, `object_id`, `object_repr`, `action_flag`, `change_message`.
- **Ví dụ**: Ghi lại ai đã xóa bài viết nào vào lúc nào.

## 8. `django_content_type`
- **Chức năng**: Lưu thông tin về các model trong project.
- **Trường chính**: `app_label`, `model`.
- **Ví dụ**: Dùng để liên kết với bảng `auth_permission`.


---
## 9. `django_migrations`
- **Chức năng**: Theo dõi các migration đã chạy.
- **Trường chính**: `app`, `name`, `applied`.
- **Ví dụ**: Django biết migration nào đã được áp dụng để không chạy lại.

## 10. `django_session`
- **Chức năng**: Lưu dữ liệu session của người dùng.
- **Trường chính**: `session_key`, `session_data`, `expire_date`.
- **Ví dụ**: Lưu trạng thái đăng nhập, giỏ hàng,...
