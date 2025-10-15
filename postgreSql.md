# 📘 Cheatsheet PostgreSQL Tổng Hợp

Tài liệu này tổng hợp các lệnh và cú pháp thường dùng trong PostgreSQL từ nhiều nguồn uy tín.

---

## 🔹 Kết nối và quản lý cơ sở dữ liệu

- `psql -U username -d dbname` – Kết nối đến cơ sở dữ liệu
- `\l` – Liệt kê tất cả các cơ sở dữ liệu
- `\c dbname` – Kết nối đến cơ sở dữ liệu `dbname`
- `\dt` – Liệt kê các bảng trong cơ sở dữ liệu hiện tại
- `\q` – Thoát khỏi psql
- `createdb dbname` – Tạo cơ sở dữ liệu mới
- `dropdb dbname` – Xóa cơ sở dữ liệu
- `createuser username` – Tạo người dùng mới
- `dropuser username` – Xóa người dùng

---

## 🔹 Truy vấn hệ thống

- `SELECT version();` – Phiên bản PostgreSQL
- `SELECT current_user;` – Người dùng hiện tại
- `SELECT * FROM pg_tables;` – Liệt kê tất cả các bảng
- `SELECT * FROM pg_roles;` – Liệt kê tất cả các vai trò
- `\df` – Liệt kê tất cả các hàm
- `\x` – Chuyển sang chế độ hiển thị mở rộng

---

## 🔹 DDL – Data Definition Language

- `CREATE TABLE table_name (...);` – Tạo bảng
- `DROP TABLE table_name;` – Xóa bảng
- `ALTER TABLE table_name ADD COLUMN column_name TYPE;` – Thêm cột
- `ALTER TABLE table_name RENAME TO new_name;` – Đổi tên bảng
- `TRUNCATE TABLE table_name;` – Xóa toàn bộ dữ liệu trong bảng

---

## 🔹 DML – Data Manipulation Language

- `INSERT INTO table_name (...) VALUES (...);` – Thêm dữ liệu
- `UPDATE table_name SET column = value WHERE condition;` – Cập nhật dữ liệu
- `DELETE FROM table_name WHERE condition;` – Xóa dữ liệu
- `SELECT * FROM table_name;` – Truy vấn dữ liệu

---

## 🔹 JSON & Window Functions

- `SELECT json_column->>'key' FROM table;` – Truy xuất giá trị từ JSON
- `SELECT jsonb_column @> '{"key": "value"}';` – Truy vấn JSONB
- `SELECT column, RANK() OVER (ORDER BY column DESC) FROM table;` – Hàm cửa sổ RANK
- `SELECT column, ROW_NUMBER() OVER (PARTITION BY group_column ORDER BY column) FROM table;` – ROW_NUMBER theo nhóm

---

## 🔹 Quản lý người dùng và quyền

- `CREATE ROLE role_name;` – Tạo vai trò
- `GRANT SELECT ON table TO role_name;` – Cấp quyền SELECT
- `REVOKE SELECT ON table FROM role_name;` – Thu hồi quyền
- `ALTER ROLE role_name WITH LOGIN PASSWORD 'secret';` – Gán mật khẩu đăng nhập

---

## 🔹 Backup và Restore

- `pg_dump dbname > backup.sql` – Sao lưu cơ sở dữ liệu
- `psql dbname < backup.sql` – Khôi phục từ file sao lưu
- `pg_dump -Fc dbname > backup.dump` – Sao lưu định dạng nén
- `pg_restore -d dbname backup.dump` – Khôi phục từ file nén

---

## 🔹 Tối ưu hiệu năng

- `EXPLAIN ANALYZE SELECT ...;` – Phân tích truy vấn
- `VACUUM;` – Dọn dẹp cơ sở dữ liệu
- `REINDEX TABLE table_name;` – Tái lập chỉ mục
- `CREATE INDEX idx_name ON table(column);` – Tạo chỉ mục
- `CLUSTER table_name USING index_name;` – Sắp xếp bảng theo chỉ mục

---


# Truy vấn trong PostgreSQL: Cơ bản, Nâng cao và Tối ưu hiệu năng

## Truy vấn Cơ bản

### 1. SELECT
Giải thích: Truy xuất dữ liệu từ một hoặc nhiều bảng.
Ví dụ:
SELECT * FROM employees;
SELECT name, salary FROM employees WHERE department = 'IT';

### 2. WHERE
Giải thích: Lọc dữ liệu theo điều kiện cụ thể.
Ví dụ:
SELECT * FROM employees WHERE salary > 1000;

### 3. ORDER BY
Giải thích: Sắp xếp kết quả theo một hoặc nhiều cột.
Ví dụ:
SELECT * FROM employees ORDER BY salary DESC;

### 4. GROUP BY + HAVING
Giải thích: Nhóm dữ liệu theo cột và lọc nhóm bằng HAVING.
Ví dụ:
SELECT department, AVG(salary) 
FROM employees 
GROUP BY department 
HAVING AVG(salary) > 2000;

### 5. INNER JOIN
Giải thích: Trả về các bản ghi có giá trị khớp ở cả hai bảng.
Ví dụ:
SELECT e.name, d.name 
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;

### 6. LEFT JOIN
Giải thích: Trả về tất cả bản ghi từ bảng bên trái và các bản ghi khớp từ bảng bên phải.
Ví dụ:
SELECT e.name, d.name 
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id;

### 7. RIGHT JOIN
Giải thích: Trả về tất cả bản ghi từ bảng bên phải và các bản ghi khớp từ bảng bên trái.
Ví dụ:
SELECT e.name, d.name 
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.id;

### 8. FULL JOIN
Giải thích: Trả về tất cả bản ghi khi có khớp ở một trong hai bảng.
Ví dụ:
SELECT e.name, d.name 
FROM employees e
FULL JOIN departments d ON e.department_id = d.id;

## Truy vấn Nâng cao

### 1. CTE (Common Table Expressions)
Giải thích: Tạo truy vấn tạm thời có thể tái sử dụng trong truy vấn chính.
Ví dụ:
WITH high_salary AS (
  SELECT * FROM employees WHERE salary > 3000
)
SELECT * FROM high_salary WHERE department = 'Sales';

### 2. Window Functions
Giải thích: Thực hiện các phép tính trên một tập con của dữ liệu mà không nhóm lại.
Ví dụ:
SELECT name, salary, RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
FROM employees;

### 3. Subqueries
Giải thích: Truy vấn lồng bên trong truy vấn khác.
Ví dụ:
SELECT name 
FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);

### 4. Truy vấn với JSON
Giải thích: Làm việc với dữ liệu dạng JSON trong PostgreSQL.
Ví dụ:
SELECT data->>'name' AS name 
FROM employees_json 
WHERE data->>'department' = 'IT';

### 5. Full-text Search
Giải thích: Tìm kiếm văn bản hiệu quả trong PostgreSQL.
Ví dụ:
SELECT * 
FROM documents 
WHERE to_tsvector(content) @@ to_tsquery('PostgreSQL');

## Tối ưu hiệu năng

### 1. Dùng INDEX
Giải thích: Tăng tốc độ truy vấn bằng cách tạo chỉ mục trên cột thường được lọc hoặc sắp xếp.
Ví dụ:
CREATE INDEX idx_salary ON employees(salary);

### 2. Phân vùng bảng (Partitioning)
Giải thích: Chia bảng lớn thành các phần nhỏ để cải thiện hiệu năng.
Ví dụ:
CREATE TABLE sales_2023 PARTITION OF sales 
FOR VALUES FROM ('2023-01-01') TO ('2023-12-31');

### 3. Phân tích truy vấn với EXPLAIN
Giải thích: Kiểm tra kế hoạch thực thi truy vấn để tối ưu.
Ví dụ:
EXPLAIN ANALYZE SELECT * FROM employees WHERE salary > 3000;

### 4. Dọn dẹp dữ liệu với VACUUM
Giải thích: Giải phóng không gian và cập nhật thống kê cho trình tối ưu hóa.
Ví dụ:
VACUUM ANALYZE employees;

### 5. Connection Pooling
Giải thích: Giảm chi phí kết nối bằng cách tái sử dụng kết nối thông qua công cụ như PgBouncer.


# Các ví dụ minh họa cho các hàm thường dùng trong PostgreSQL:

## 1. Hàm chuỗi:
- LENGTH('Hello') → 5
- LOWER('HELLO') → 'hello'
- UPPER('hello') → 'HELLO'
- CONCAT('Hello', ' ', 'World') → 'Hello World'
- SUBSTRING('abcdef' FROM 2 FOR 3) → 'bcd'

## 2. Hàm số học:
- ROUND(3.14159, 2) → 3.14
- CEIL(4.2) → 5
- FLOOR(4.8) → 4
- ABS(-10) → 10
- RANDOM() → trả về số thực ngẫu nhiên từ 0 đến 1

## 3. Hàm ngày giờ:
- NOW() → trả về thời gian hiện tại
- CURRENT_DATE → trả về ngày hiện tại
- AGE('2020-01-01') → khoảng thời gian từ ngày đó đến hiện tại
- DATE_PART('year', NOW()) → trả về năm hiện tại

## 4. Hàm tổng hợp:
- COUNT(*) → đếm số dòng
- SUM(salary) → tổng lương
- AVG(score) → điểm trung bình
- MIN(age) → tuổi nhỏ nhất
- MAX(age) → tuổi lớn nhất

## 5. Hàm điều kiện:
- COALESCE(NULL, 'default') → 'default'
- NULLIF(5, 5) → NULL
- CASE WHEN score >= 90 THEN 'A' ELSE 'B' END → phân loại điểm

## 6. Hàm xử lý mảng:
- ARRAY[1,2,3] → tạo mảng
- UNNEST(ARRAY[1,2,3]) → tách mảng thành các dòng
- ARRAY_LENGTH(ARRAY[1,2,3], 1) → 3

## 7. Hàm JSON:
- TO_JSON(ROW(1, 'abc')) → chuyển sang JSON
- JSONB_EXTRACT_PATH('{"a": {"b": 1}}'::jsonb, 'a', 'b') → 1
- JSONB_SET('{"a":1}'::jsonb, '{a}', '2'::jsonb) → '{"a":2}'
