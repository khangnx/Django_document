```

===============================
TỔNG HỢP KIẾN THỨC LARAVEL AUTHORIZATION
===============================

1. Middleware `can:` hoạt động thế nào?
---------------------------------------
- Khi bạn khai báo route với middleware `can:permission-name`, Laravel sẽ gọi:
    Gate::allows('permission-name')
- Nếu người dùng không có quyền, Laravel trả về 403 Forbidden.
- Middleware này dựa vào các quyền được định nghĩa trong Gate hoặc Policy.

Ví dụ route:
Route::post('country/create', 'Master\MCountryController@store')->middleware('can:add-country-master');

---------------------------------------

2. Laravel lấy quyền ở đâu để kiểm tra?
---------------------------------------
- Laravel kiểm tra quyền thông qua Gate hoặc Policy.
- Gate được định nghĩa trong Service Provider (thường là AuthServiceProvider hoặc PermissionsServiceProvider).
- Policy là class riêng cho từng model, đăng ký trong AuthServiceProvider.

---------------------------------------

3. Gate::define() trong Service Provider
---------------------------------------
Ví dụ trong PermissionsServiceProvider:

use Illuminate\Support\Facades\Gate;

class PermissionsServiceProvider extends ServiceProvider
{
    public function boot()
    {
        Gate::define('add-country-master', function ($user) {
            return $user->hasPermission('add-country-master');
        });

        Gate::define('access-country-master', function ($user) {
            return $user->hasPermission('access-country-master');
        });

        Gate::define('edit-country-master', function ($user) {
            return $user->hasPermission('edit-country-master');
        });

        Gate::define('delete-country-master', function ($user) {
            return $user->hasPermission('delete-country-master');
        });
    }
}

---------------------------------------

4. Laravel gọi boot() của provider nào?
---------------------------------------
- Laravel gọi boot() của TẤT CẢ các Service Provider được đăng ký trong config/app.php.
- Nếu bạn khai báo:
    App\Providers\PermissionsServiceProvider::class,
    Illuminate\Auth\AuthServiceProvider::class,
  thì cả hai boot() đều được gọi.
- Miễn là Gate::define() được gọi trước khi middleware xử lý, Laravel sẽ nhận diện quyền.

---------------------------------------

5. User model với hasPermission()
---------------------------------------
Trong model User, định nghĩa phương thức kiểm tra quyền:

public function hasPermission($permission)
{
    return $this->roles()->whereHas('permissions', function ($query) use ($permission) {
        $query->where('name', $permission);
    })->exists();
}

Hoặc nếu bạn lưu trực tiếp quyền cho user:
public function hasPermission($permission)
{
    return $this->permissions()->where('name', $permission)->exists();
}

---------------------------------------

6. Cấu trúc bảng roles/permissions
---------------------------------------
- Bảng users: id, name, email, ...
- Bảng roles: id, name
- Bảng permissions: id, name
- Bảng role_user: user_id, role_id
- Bảng permission_role: role_id, permission_id

Quan hệ:
- User có nhiều Role (belongsToMany)
- Role có nhiều Permission (belongsToMany)

Migration ví dụ:
Schema::create('roles', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->timestamps();
});

Schema::create('permissions', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->timestamps();
});

Schema::create('role_user', function (Blueprint $table) {
    $table->foreignId('role_id')->constrained()->onDelete('cascade');
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
});

Schema::create('permission_role', function (Blueprint $table) {
    $table->foreignId('permission_id')->constrained()->onDelete('cascade');
    $table->foreignId('role_id')->constrained()->onDelete('cascade');
});

---------------------------------------

7. Tích hợp package Spatie Laravel Permission (tuỳ chọn)
---------------------------------------
- Cài đặt: composer require spatie/laravel-permission
- Cung cấp sẵn model Role, Permission và middleware 'role' và 'permission'.
- Giúp quản lý quyền dễ dàng hơn.

===============================
KẾT LUẬN:
- Middleware `can:` dựa vào Gate hoặc Policy.
- Gate::define() phải được gọi trong boot() của Service Provider.
- Laravel gọi boot() của tất cả provider trong config/app.php.
- Kiểm tra quyền thường dựa vào roles/permissions trong database.
===============================
===============================================



Cách Laravel gọi boot() của nhiều Service Provider và cách middleware can: hoạt động với Gate::define()

1. Laravel gọi boot() như thế nào?
- Trong file config/app.php, bạn khai báo danh sách các service provider trong mảng 'providers'.
- Khi ứng dụng khởi động, Laravel sẽ lần lượt gọi phương thức boot() của TẤT CẢ các service provider đã đăng ký.
- Không có chuyện Laravel chọn một provider duy nhất, mà tất cả provider đều được thực thi boot().

Ví dụ:
'providers' => [
    App\Providers\PermissionsServiceProvider::class,
    Illuminate\Auth\AuthServiceProvider::class,
    // Các provider khác...
]

=> Laravel sẽ gọi boot() của cả hai provider này.

2. Middleware can: hoạt động như thế nào?
- Middleware can: được dùng để kiểm tra quyền truy cập thông qua Gate.
- Khi bạn khai báo route với middleware can: như sau:
    Route::post('country/create', 'Master\MCountryController@store')->middleware('can:add-country-master');

- Laravel sẽ gọi Gate::allows('add-country-master') để kiểm tra quyền.
- Gate::allows() sẽ dựa vào các định nghĩa quyền được khai báo bằng Gate::define() trong các service provider.

3. Gate::define() ở đâu?
- Bạn có thể định nghĩa quyền trong AuthServiceProvider hoặc PermissionsServiceProvider.
- Miễn là provider đó được đăng ký trong config/app.php, Laravel sẽ gọi boot() và thực thi Gate::define().

Ví dụ trong PermissionsServiceProvider:
use Illuminate\Support\Facades\Gate;

public function boot()
{
    Gate::define('add-country-master', function ($user) {
        return $user->hasPermission('add-country-master');
    });
}

4. Tóm tắt:
- Laravel gọi boot() của tất cả provider đã đăng ký.
- Middleware can: sẽ kiểm tra quyền thông qua Gate::allows().
- Gate::define() phải được gọi trong boot() của một provider (AuthServiceProvider hoặc PermissionsServiceProvider).
- Logic kiểm tra quyền thường nằm trong phương thức hasPermission() của model User, dựa vào dữ liệu roles/permissions trong database.
```
