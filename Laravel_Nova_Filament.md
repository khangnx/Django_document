⚙️ Laravel Nova vs Filament – So sánh chi tiết
1. Giới thiệu
- Nova: Admin panel chính thức của Laravel, trả phí, giao diện chuyên nghiệp, tích hợp sâu với hệ sinh thái Laravel.
- Filament: Admin panel mã nguồn mở, miễn phí, hiện đại, dễ tùy biến, cộng đồng mạnh.

2. So sánh tổng quan
<img width="687" height="535" alt="image" src="https://github.com/user-attachments/assets/5856a7d3-9ed2-46e6-a52a-e5f2a97a31f9" />

3. Ví dụ thực tế
Nova
- Resource:
  
  ```
class UserResource extends Resource {
    public static $model = \App\Models\User::class;

    public function fields(Request $request) {
        return [
            ID::make()->sortable(),
            Text::make('Name')->sortable()->rules('required'),
            Text::make('Email')->rules('required', 'email'),
        ];
    }
}
```

- Use case: CRM doanh nghiệp, cần dashboard ổn định, metrics, filter phức tạp.
Filament
- Form & Table:

```
class ProductResource extends Resource {
    public static $model = Product::class;

    public static function form(Form $form): Form {
        return $form->schema([
            TextInput::make('name')->required(),
            TextInput::make('price')->numeric()->required(),
        ]);
    }

    public static function table(Table $table): Table {
        return $table->columns([
            TextColumn::make('name')->sortable(),
            TextColumn::make('price')->sortable(),
        ]);
    }
}
```

- Use case: E-commerce startup, cần admin panel nhanh, miễn phí.

4. Ưu nhược điểm
Nova
- ✅ Chính thức, giao diện chuyên nghiệp, tích hợp sâu.
- ❌ Trả phí, ít tùy biến, cộng đồng nhỏ.
Filament
- ✅ Miễn phí, dễ tùy biến, cộng đồng mạnh, nhiều plugin.
- ❌ Không chính thức, có thể thiếu support enterprise, thay đổi nhanh theo cộng đồng.

5. Kết luận
- Chọn Nova: nếu dự án lớn, cần sự ổn định, support chính thức, có ngân sách.
- Chọn Filament: nếu muốn tốc độ phát triển, chi phí thấp, linh hoạt, tận dụng sức mạnh cộng đồng.
👉 Trong phỏng vấn, bạn có thể nói:
“Nova là admin panel chính thức, phù hợp cho enterprise app cần sự ổn định. Filament thì miễn phí, dễ tùy biến, cộng đồng mạnh, phù hợp cho startup hoặc dự án nhỏ. Ví dụ: tôi dùng Nova cho CRM doanh nghiệp, còn Filament cho e-commerce startup vì cần tốc độ và chi phí thấp.”

=============================================================


*** Nói về Filament
- Filament hoạt động theo kiểu: bạn định nghĩa Resource cho mỗi Model, và Resource này sẽ tự động sinh ra các Page (List, Create, Edit) cùng với các Form và Table tương ứng.
- Ví dụ với User model, bạn sẽ tạo một UserResource để quản lý bảng users. Trong đó, phần table() sẽ định nghĩa cách hiển thị dữ liệu:

```
<?php

namespace App\Filament\Resources;

use App\Models\User;
use Filament\Resources\Resource;
use Filament\Tables;
use Filament\Forms;

class UserResource extends Resource
{
    protected static ?string $model = User::class;

    protected static ?string $navigationIcon = 'heroicon-o-user';

    public static function form(Forms\Form $form): Forms\Form
    {
        return $form->schema([
            Forms\Components\TextInput::make('name')->required(),
            Forms\Components\TextInput::make('email')->email()->required(),
            Forms\Components\TextInput::make('password')
                ->password()
                ->required()
                ->dehydrateStateUsing(fn ($state) => bcrypt($state)),
        ]);
    }

    public static function table(Tables\Table $table): Tables\Table
    {
        return $table->columns([
            Tables\Columns\TextColumn::make('id')->sortable(),
            Tables\Columns\TextColumn::make('name')->searchable(),
            Tables\Columns\TextColumn::make('email')->searchable(),
            Tables\Columns\TextColumn::make('created_at')->dateTime(),
        ])
        ->filters([
            Tables\Filters\Filter::make('verified')
                ->query(fn ($query) => $query->whereNotNull('email_verified_at')),
        ])
        ->actions([
            Tables\Actions\EditAction::make(),
        ])
        ->bulkActions([
            Tables\Actions\DeleteBulkAction::make(),
        ]);
    }
}
```


👉 Ở đây:
  - UserResource đại diện cho Model User.
  - form() định nghĩa form khi tạo/sửa user.
  - table() định nghĩa bảng hiển thị danh sách user.
  - Filament sẽ tự động tạo các trang CRUD dựa trên Resource này.
=> Nói cách khác: Model là dữ liệu, Resource là "component" quản lý dữ liệu đó trong Filament.

* cấu trúc thư mục cho project Laravel như thế nào? Ví dụ trong project tôi có 3 model là User, Product và Order

```
app/
  Models/
    User.php
    Product.php
    Order.php
  Http/
    Controllers/
    Middleware/
  Filament/
    Resources/
      UserResource.php
      ProductResource.php
      OrderResource.php
    Pages/
    Widgets/

database/
  migrations/
    2024_01_01_000000_create_users_table.php
    2024_01_01_000001_create_products_table.php
    2024_01_01_000002_create_orders_table.php
  seeders/
    UserSeeder.php
    ProductSeeder.php
    OrderSeeder.php

routes/
  web.php
  api.php
  ```

  * Giải thích:
  - app/Models/: chứa các Model chính (User, Product, Order).
  - app/Filament/Resources/: mỗi Model sẽ có một Resource tương ứng (UserResource, ProductResource, OrderResource) để định nghĩa form và table CRUD trong Filament.
  - database/migrations/: định nghĩa cấu trúc bảng trong database cho từng Model.
  - database/seeders/: tạo dữ liệu mẫu cho User, Product, Order.
  - routes/: định nghĩa các route web và API.
* Ví dụ:
  - UserResource.php sẽ quản lý CRUD cho bảng users.
  - ProductResource.php sẽ quản lý CRUD cho bảng products.
  - OrderResource.php sẽ quản lý CRUD cho bảng orders.
* Filament sẽ tự động sinh ra các trang quản trị tương ứng, ví dụ:
  - /admin/users
  - /admin/products
  - /admin/orders
  👉 Như vậy, mỗi Model có một Resource trong Filament để quản lý, và bạn sẽ thấy chúng nằm gọn trong thư mục app/Filament/Resources.

  ** Và ta hiều tầng View như sau:
  
```
app/
  Models/
    User.php
    Product.php
    Order.php
  Filament/
    Resources/
      UserResource.php
      ProductResource.php
      OrderResource.php
    Pages/
      Dashboard.php
      Reports.php

resources/
  views/
    products/
      index.blade.php
      show.blade.php
    orders/
      checkout.blade.php
    users/
      profile.blade.php
  ```

- Nếu bạn làm frontend cho khách hàng, bạn sẽ code trong resources/views.
- Nếu bạn làm backend admin panel với Filament, bạn sẽ tạo Resource hoặc Page trong app/Filament/.... Filament sẽ tự động render UI dựa trên các định nghĩa form/table mà bạn viết trong class PHP.

==> tức là:
- resources/views/... → giao diện cho khách hàng (frontend).
- app/Filament/Resources/... → giao diện quản trị CRUD trong admin panel.


