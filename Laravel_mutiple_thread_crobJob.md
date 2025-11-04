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
