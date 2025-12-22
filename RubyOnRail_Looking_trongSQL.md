# Tài liệu về Locking trong Ruby on Rails và PostgreSQL

## 1. Khái niệm Locking

Locking là cơ chế kiểm soát truy cập đồng thời vào dữ liệu nhằm đảm bảo tính toàn vẹn và tránh race condition. Trong Rails và PostgreSQL có hai loại chính:

- **Optimistic Locking**: Khóa lạc quan, phát hiện xung đột sau khi cập nhật.
- **Pessimistic Locking**: Khóa bi quan, ngăn chặn xung đột ngay từ khi đọc dữ liệu.

---

## 2. Optimistic Locking trong Rails

**Cách hoạt động**: Rails sử dụng cột `lock_version`. Mỗi lần cập nhật, giá trị tăng lên. Nếu có xung đột, tiến trình thứ hai sẽ bị lỗi `ActiveRecord::StaleObjectError`.

### Thiết kế bảng

```ruby
create_table :posts do |t|
  t.string :title
  t.text :content
  t.integer :lock_version, default: 0, null: false
  t.timestamps
end
```

### Ví dụ sử dụng

```ruby
post = Post.find(1)
post.title = "New title"
post.save! # lock_version tăng lên
```

**Ưu điểm**: Hiệu năng cao, phù hợp khi xung đột hiếm.  

**Nhược điểm**: Nếu xung đột thường xuyên sẽ gây nhiều lỗi rollback.

---

## 3. Pessimistic Locking trong Rails

**Cách hoạt động**: Rails dùng SQL `SELECT ... FOR UPDATE` để khóa bản ghi ngay khi đọc.

**Thiết kế bảng**: Không cần thêm cột đặc biệt, chỉ cần các trường dữ liệu nghiệp vụ.

```ruby
create_table :accounts do |t|
  t.string :name, null: false
  t.decimal :balance, precision: 15, scale: 2, default: 0.0, null: false
  t.timestamps
end
```

### Ví dụ sử dụng

```ruby
Account.transaction do
  source = Account.lock("FOR UPDATE").find_by(name: "Alice")
  target = Account.lock("FOR UPDATE").find_by(name: "Bob")

  source.balance -= 100
  source.save!

  target.balance += 100
  target.save!
end
```

Hoặc:

```ruby
user.with_lock do
  user.balance -= 50
  user.save!
end
```

**Ưu điểm**: Ngăn race condition ngay từ đầu.  

**Nhược điểm**: Có thể gây nghẽn hoặc deadlock.

---

## 4. Locking mặc định trong PostgreSQL khi không sử dụng  Optimistic Lockin và Pessimistic Locking

PostgreSQL sử dụng một hệ thống khóa phức tạp để quản lý truy cập đồng thời nhằm đảm bảo tính nhất quán và toàn vẹn dữ liệu. Các loại khóa mặc định bao gồm:

- **INSERT**: Khi thực hiện thao tác chèn dữ liệu, PostgreSQL áp dụng *Row Exclusive Lock* trên toàn bộ bảng. Loại khóa này cho phép nhiều tiến trình cùng thực hiện insert đồng thời mà không gây xung đột.
- **UPDATE / DELETE**: Các thao tác cập nhật hoặc xóa dữ liệu sẽ đặt *Row Exclusive Lock* trên từng hàng dữ liệu cụ thể. Điều này ngăn chặn các tiến trình khác cùng cập nhật hoặc xóa chính xác hàng đó.
- **SELECT**: Các truy vấn đọc dữ liệu không đặt khóa mạnh mà sử dụng cơ chế **MVCC (Multi-Version Concurrency Control)** để đọc snapshot dữ liệu.
- **Table-level lock**: PostgreSQL còn áp dụng một loại khóa nhẹ trên bảng để ngăn chặn các thay đổi cấu trúc bảng (DDL) trong khi có các thao tác DML đang diễn ra.

Hệ thống khóa mặc định này giúp PostgreSQL cân bằng giữa hiệu năng và tính toàn vẹn dữ liệu trong môi trường đa người dùng và truy cập đồng thời.

---

## 5. So sánh Optimistic vs Pessimistic

| Tiêu chí | Optimistic Locking | Pessimistic Locking |
|--------|------------------|-------------------|
| Cách hoạt động | Dùng lock_version để phát hiện xung đột | Dùng SQL FOR UPDATE để khóa ngay |
| Hiệu năng | Cao, ít tốn tài nguyên | Thấp hơn, dễ gây nghẽn |
| Khi nào dùng | Khi xung đột hiếm | Khi xung đột thường xuyên, nghiệp vụ tài chính |
| Lỗi thường gặp | StaleObjectError | Deadlock, blocking |

---

## 6. Lưu ý khi thiết kế hệ thống

- **Optimistic**: Phù hợp cho dữ liệu ít quan trọng, xung đột hiếm.
- **Pessimistic**: Bắt buộc cho nghiệp vụ tài chính, giỏ hàng, tồn kho.
- **PostgreSQL**: Luôn có row-level lock mặc định, nhưng để tránh race condition nghiệp vụ, cần dùng pessimistic locking.

---

## 7. Kết luận

Rails hỗ trợ cả hai loại locking.

PostgreSQL mặc định đã có row-level lock, nhưng không đủ để ngăn race condition nghiệp vụ.

Với các thao tác quan trọng như trừ tiền khỏi tài khoản, nên dùng pessimistic locking để đảm bảo tính toàn vẹn dữ liệu.

Điều này có nghĩa là khi thực hiện các thao tác nhạy cảm như trừ tiền, cần khóa bản ghi tài khoản ngay từ đầu giao dịch để ngăn chặn các giao dịch khác cùng thay đổi dữ liệu đó đồng thời.

Ví dụ:

```ruby
Account.transaction do
  source = Account.lock("FOR UPDATE").find_by(name: "Alice")
  target = Account.lock("FOR UPDATE").find_by(name: "Bob")

  source.balance -= 100
  source.save!

  target.balance += 100
  target.save!
end
```
