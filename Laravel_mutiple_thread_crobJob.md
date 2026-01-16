```
Giới thiệu
Laravel là một framework PHP phổ biến, được thiết kế theo mô hình MVC và hỗ trợ nhiều tính năng mạnh mẽ cho phát triển ứng dụng web. Tuy nhiên, PHP bản chất là ngôn ngữ xử lý theo mô hình single-threaded cho mỗi request, nên Laravel không hỗ trợ đa luồng (multi-threading) trực tiếp.

1) Multi-threading trong Laravel
- PHP không có native multi-threading như Java hoặc C#.
- Để xử lý song song, bạn có thể dùng:
  * Queue Jobs (Laravel Queue): cho phép đẩy các tác vụ nặng sang hàng đợi và xử lý bất đồng bộ.
  * Supervisor hoặc Laravel Horizon: quản lý nhiều worker chạy song song.
  * Async với Swoole hoặc ReactPHP: nếu bạn muốn chạy PHP theo kiểu event-loop, nhưng đây là giải pháp nâng cao.

2) Active Job trong Laravel
- Laravel không dùng thuật ngữ “Active Job” (đây là khái niệm của Ruby on Rails).
- Laravel có Jobs và Queue System:
  * Tạo Job bằng lệnh: php artisan make:job
  * Job được đẩy vào Queue để xử lý bất đồng bộ.
  * Hỗ trợ nhiều driver: database, redis, beanstalkd, sqs, v.v.
  * Có thể thiết lập retry, delay, chaining, batch processing.

3) Cách triển khai song song trong Laravel
- Dùng Queue với nhiều worker:
  php artisan queue:work --queue=high,default --sleep=3 --tries=3
- Hoặc dùng Horizon để giám sát và scale workers.
- Nếu cần thực sự chạy song song trong một request, có thể dùng Process hoặc Thread qua extension như pthreads (nhưng không phổ biến trong Laravel).
```
### 🔹 php artisan queue:work
- Chạy worker process để xử lý các job trong hàng đợi.
- Worker sẽ khởi động một lần, load toàn bộ code và config, sau đó liên tục xử lý job.
- Nhanh hơn vì không phải reload framework cho mỗi job.
- Nhưng: nếu bạn thay đổi code/config, worker không tự động cập nhật. Bạn phải restart worker để áp dụng thay đổi.
- Thường dùng trong môi trường production để tiết kiệm tài nguyên và tăng hiệu năng.
### 🔹 php artisan queue:listen
- Lắng nghe queue và khởi động lại framework cho mỗi job.
- Điều này giúp bạn thấy ngay thay đổi code mà không cần restart worker.
- Nhưng: tốn nhiều tài nguyên hơn, xử lý chậm hơn vì mỗi job phải reload toàn bộ ứng dụng.
- Thường dùng trong development để tiện test và debug.


## 👉 Tóm lại:
- Dev thì hay dùng queue:listen cho tiện.
- Prod thì nên dùng queue:work để tối ưu hiệu năng.
