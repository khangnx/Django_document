## 📌 Service Provider trong Laravel

### 1. Hai phương thức chính

#### 🔹 `register()`

-   Dùng để **bind (đăng ký)** các service, class, hoặc interface vào
    **Service Container**
-   **Không thực thi logic ngay**, chỉ khai báo để sau này có thể inject
-   Thường dùng để:
    -   Đăng ký Repository
    -   Bind Interface → Implementation
    -   Đăng ký Helper, Service class

#### 🔹 `boot()`

-   Dùng để **khởi tạo hoặc chạy logic** sau khi **tất cả service đã
    được đăng ký**
-   Đây là nơi **thực thi code**
-   Thường dùng cho:
    -   Định nghĩa `Gate`
    -   Blade directive
    -   Event listener
    -   Observer
    -   Route macro

------------------------------------------------------------------------

### 2. Code gốc ví dụ

``` php
class PermissionsServiceProvider extends ServiceProvider
{
    public function boot()
    {
        try {
            Permission::get()->map(function ($permission) {
                Gate::define($permission->code, function ($user) use ($permission) {
                    return $user->hasPermissionTo($permission);
                });
            });
        } catch (\Exception $e) {
            report($e);
            return false;
        }
    }

    public function register()
    {
        // Không cần đăng ký service nào thêm
    }
}
```

------------------------------------------------------------------------

### 3. Ví dụ sử dụng `register()`

``` php
public function register()
{
    $this->app->bind(
        App\Repositories\UserRepositoryInterface::class,
        App\Repositories\UserRepository::class
    );
}
```

``` php
use App\Repositories\UserRepositoryInterface;

class UserController extends Controller
{
    public function __construct(UserRepositoryInterface $users)
    {
        $this->users = $users;
    }

    public function index()
    {
        return $this->users->all();
    }
}
```

------------------------------------------------------------------------

### 4. Ví dụ sử dụng `boot()`

``` php
public function boot()
{
    Gate::define('edit-post', function ($user) {
        return $user->role === 'editor';
    });
}
```

``` blade
@can('edit-post')
    <button>Sửa bài viết</button>
@endcan
```

------------------------------------------------------------------------

## ✅ Tóm lại

-   `register()` → đăng ký service, không chạy logic
-   `boot()` → thực thi logic sau khi service đã sẵn sàng
-   Gate, Blade directive, Event → **luôn viết trong `boot()`**
