# Trong Laravel, dependency injection (DI) được thực hiện thông qua Service Container, nơi các class phụ thuộc được “tiêm” vào constructor hoặc method thay vì tự khởi tạo trực tiếp. Điều này giúp code dễ bảo trì, test, và giảm sự phụ thuộc chặt chẽ giữa các thành phần.

## 🔑 Khái niệm cơ bản
- Dependency Injection (DI): Là kỹ thuật cung cấp đối tượng phụ thuộc cho một class thay vì để class đó tự tạo.
- Service Container: Laravel có một container mạnh mẽ để quản lý và tự động resolve (giải quyết) các dependency.
Ví dụ đơn giản:
```
namespace App\Http\Controllers;

use App\Services\AppleMusic;

class PodcastController extends Controller
{
    protected $music;

    public function __construct(AppleMusic $music)
    {
        $this->music = $music;
    }
}
```
 -> Ở đây, Laravel sẽ tự động tạo instance của AppleMusic và truyền vào PodcastController.

## ⚙️ Cách hoạt động trong Laravel
- Automatic Injection (Tự động tiêm):
- Khi bạn khai báo type-hint trong constructor hoặc method, Laravel sẽ tự động resolve từ container.
- Ví dụ: public function __construct(UserRepository $repo) → Laravel sẽ tìm và inject UserRepository.
- Binding (Đăng ký phụ thuộc):
- Bạn có thể đăng ký binding trong AppServiceProvider:
```
$this->app->bind(
    'App\Contracts\UserRepository',
    'App\Repositories\DbUserRepository'
);
```
- Khi cần UserRepository, Laravel sẽ inject DbUserRepository.
- Contextual Binding (Binding theo ngữ cảnh):
- Cho phép bạn chỉ định implementation khác nhau tùy vào class nào đang yêu cầu.
```
$this->app->when(PhotoController::class)
          ->needs(UserRepository::class)
          ->give(PhotoUserRepository::class);

```

- Method Injection:
- Ngoài constructor, bạn có thể inject trực tiếp vào method:
  
```
public function store(Request $request, UserService $service)
{
    $service->create($request->all());
}
```


## 📊 Ưu điểm của Dependency Injection
| Ưu điểm | Giải thích |
|-------|------------|
| **Decoupling (Giảm phụ thuộc)** | Class không cần biết cách tạo dependency, chỉ cần sử dụng. |
| **Test dễ dàng hơn** | Có thể inject mock hoặc fake object khi viết unit test. |
| **Code rõ ràng, dễ bảo trì** | Các dependency được khai báo rõ ràng trong constructor. |
| **Tái sử dụng** | Một service có thể được dùng ở nhiều nơi mà không cần khởi tạo lại. |



## 🚀 Kết luận
- Laravel DI giúp bạn viết code sạch, dễ test, và linh hoạt.
- Hãy tận dụng Service Container để quản lý dependency thay vì khởi tạo trực tiếp bằng new.
- Khi ứng dụng lớn dần, DI sẽ giúp bạn tránh “spaghetti code” và dễ dàng thay đổi logic mà không ảnh hưởng đến toàn bộ hệ thống.


