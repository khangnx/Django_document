# Design Patterns trong Laravel

Laravel là một framework PHP hiện đại, được xây dựng dựa trên nhiều design pattern để giúp code gọn gàng, dễ bảo trì và mở rộng. Dưới đây là tài liệu tổng hợp các pattern phổ biến trong Laravel kèm ví dụ minh họa.

## 1. Singleton Pattern

Đảm bảo chỉ có một instance duy nhất trong toàn bộ ứng dụng.
```
// App\Providers\AppServiceProvider.php
public function register()
{
    $this->app->singleton('PaymentGateway', function ($app) {
        return new \App\Services\PaymentGateway();
    });
}

// Sử dụng
$gateway = app('PaymentGateway');
```
## 2. Factory Pattern

Laravel hỗ trợ Model Factory để tạo dữ liệu giả.
```
// database/factories/UserFactory.php
public function definition()
{
    return [
        'name' => $this->faker->name(),
        'email' => $this->faker->unique()->safeEmail(),
        'password' => bcrypt('password'),
    ];
}

// Sử dụng trong seeder
User::factory()->count(10)->create();
```
## 3. Builder Pattern

Laravel Query Builder cho phép xây dựng query theo từng bước.
```
$users = DB::table('users')
            ->where('active', true)
            ->orderBy('created_at', 'desc')
            ->limit(10)
            ->get();
```

## 4. Repository Pattern

Tách logic truy cập dữ liệu khỏi business logic.
```
// App\Repositories\UserRepository.php
namespace App\Repositories;

use App\Models\User;

class UserRepository
{
    public function allActive()
    {
        return User::where('active', true)->get();
    }
}

// Controller
public function index(UserRepository $repo)
{
    $users = $repo->allActive();
    return view('users.index', compact('users'));
}```

## 5. Observer Pattern

Theo dõi sự kiện và phản ứng lại.
```
// App\Observers\UserObserver.php
namespace App\Observers;

use App\Models\User;

class UserObserver
{
    public function created(User $user)
    {
        \Log::info("User created: ".$user->email);
    }
}

// AppServiceProvider.php
public function boot()
{
    User::observe(\App\Observers\UserObserver::class);
}
```
## 6. Strategy Pattern

Cho phép thay đổi thuật toán mà không ảnh hưởng đến client.
```
// App\Services\Payment\PaymentStrategy.php
interface PaymentStrategy {
    public function pay($amount);
}

// PayPal
class PayPalPayment implements PaymentStrategy {
    public function pay($amount) {
        echo "Paying $amount via PayPal";
    }
}

// Stripe
class StripePayment implements PaymentStrategy {
    public function pay($amount) {
        echo "Paying $amount via Stripe";
    }
}

// Context
class PaymentContext {
    protected $strategy;
    public function __construct(PaymentStrategy $strategy) {
        $this->strategy = $strategy;
    }
    public function execute($amount) {
        $this->strategy->pay($amount);
    }
}

// Sử dụng
$payment = new PaymentContext(new StripePayment());
$payment->execute(100);
```
## 7. Facade Pattern

Laravel cung cấp interface tĩnh để truy cập service.
```
Cache::put('key', 'value', 600);
DB::table('users')->get();
```
## 8. Dependency Injection (DI)

Giúp giảm coupling bằng cách inject dependency qua constructor.
```
class UserController extends Controller
{
    protected $service;

    public function __construct(UserService $service)
    {
        $this->service = $service;
    }

    public function index()
    {
        return $this->service->getAllUsers();
    }
}
```
## 9. Adapter Pattern

Chuyển đổi interface của class này sang interface khác mà client mong muốn.
```
// Adapter cho API bên ngoài
class ExternalApiAdapter
{
    protected $api;

    public function __construct(ExternalApi $api)
    {
        $this->api = $api;
    }

    public function getUsers()
    {
        return $this->api->fetchUsers();
    }
}
```
## 10. Proxy Pattern

Đóng vai trò trung gian, kiểm soát truy cập đến object.
```
class AuthProxy
{
    protected $service;

    public function __construct(UserService $service)
    {
        $this->service = $service;
    }

    public function getUsers($user)
    {
        if ($user->isAdmin()) {
            return $this->service->getAllUsers();
        }
        throw new \Exception('Unauthorized');
    }
}
```
## 📌 Kết luận

- Laravel tích hợp sẵn nhiều design pattern.

- Các pattern giúp giảm coupling, tăng tái sử dụng và dễ test.

- Bạn có thể mở rộng thêm Repository, Strategy… để code dễ bảo trì hơn.
