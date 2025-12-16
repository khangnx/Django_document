# Laravel Core Architecture – Deep Dive

> Tài liệu này tổng hợp và đào sâu các thành phần cốt lõi của Laravel: **Request Lifecycle, Service Container, Facade, Middleware, Service Provider, Kiến trúc ứng dụng, Testing, Clean Architecture**, dành cho người muốn hiểu Laravel ở mức **framework internals & kiến trúc**.

---

## 1. Request Lifecycle (Luồng xử lý Request)

```
HTTP Request
   ↓
public/index.php
   ↓
Bootstrap Application
   ↓
Service Providers (register → boot)
   ↓
Global Middleware
   ↓
Route Middleware
   ↓
Controller
   ↓
Service / Domain Logic
   ↓
Response
```

### public/index.php

```php
$app = require_once __DIR__.'/../bootstrap/app.php';
$kernel = $app->make(HttpKernel::class);
$response = $kernel->handle($request);
```

👉 **Service Container được khởi tạo ngay từ đầu** và xuyên suốt vòng đời request.

---

## 2. Service Container (IoC Container)

### 2.1 Khái niệm

Service Container là **trung tâm quản lý dependency** của Laravel:

* Tạo object
* Inject dependency
* Quản lý vòng đời object (bind / singleton)

Tương đương với:

* `dry-container` trong Rails
* IoC Container trong Spring

---

### 2.2 Binding cơ bản

```php
$this->app->bind(
    PaymentGateway::class,
    StripePaymentGateway::class
);
```

Inject:

```php
public function __construct(PaymentGateway $gateway)
{
    $this->gateway = $gateway;
}
```

---

### 2.3 Các loại binding

#### bind – tạo mới mỗi lần

```php
$this->app->bind(Foo::class, FooImpl::class);
```

#### singleton – dùng chung

```php
$this->app->singleton(CacheManager::class);
```

#### bind bằng closure

```php
$this->app->bind(PaymentGateway::class, function () {
    return new StripeGateway(config('stripe.key'));
});
```

---

### 2.4 Contextual Binding (nâng cao)

```php
$this->app->when(OrderService::class)
    ->needs(PaymentGateway::class)
    ->give(StripeGateway::class);
```

👉 Cùng interface, khác implementation tùy context.

---

### 2.5 Resolve thủ công (hạn chế dùng)

```php
app(PaymentGateway::class);
resolve(PaymentGateway::class);
```

---

## 3. Facade

### 3.1 Facade là gì?

Facade là **proxy** đến Service Container, **KHÔNG phải static thật**.

```
Cache::get()
   ↓
Facade
   ↓
Service Container
   ↓
CacheManager instance
```

---

### 3.2 Cấu trúc Facade

```php
class Cache extends Facade
{
    protected static function getFacadeAccessor()
    {
        return 'cache';
    }
}
```

`'cache'` là key trong Service Container.

---

### 3.3 Khi nào dùng Facade?

✅ Nên dùng:

* Cache
* Log
* DB
* Queue
* Event

❌ Tránh dùng:

* Business logic
* Domain service

---

### 3.4 Facade & Testing

```php
Cache::fake();
Event::fake();
Queue::fake();
```

Laravel sẽ **swap object trong container**, không mock static.

---

## 4. Middleware

### 4.1 Vai trò Middleware

Middleware xử lý request **trước / sau Controller**:

* Auth
* Permission
* Logging
* Rate limiting

---

### 4.2 Luồng Middleware

```
Request
 ↓
Global Middleware
 ↓
Route Middleware
 ↓
Controller
```

---

### 4.3 Ví dụ Middleware

```php
public function handle($request, Closure $next)
{
    if (!auth()->check()) {
        return redirect('/login');
    }

    return $next($request);
}
```

---

### 4.4 Middleware & Dependency Injection

```php
public function __construct(AuthManager $auth)
{
    $this->auth = $auth;
}
```

Middleware cũng được resolve qua Service Container.

---

## 5. Service Provider

### 5.1 Service Provider là gì?

Service Provider là nơi **khai sinh toàn bộ hệ thống**:

* Bind service
* Register event
* Register macro

---

### 5.2 Hai method quan trọng

```php
public function register() {}
public function boot() {}
```

| Method   | Mục đích             |
| -------- | -------------------- |
| register | Bind service         |
| boot     | Dùng service đã bind |

❌ Không dùng DB / service khác trong `register()`

---

## 6. Kiến trúc ứng dụng khuyên dùng

```
Controller
   ↓
Application Service (Use Case)
   ↓
Domain Service
   ↓
Repository (Interface)
   ↓
Eloquent (Infrastructure)
```

---

### 6.1 Controller

```php
class OrderController
{
    public function store(StoreOrder $request, CreateOrder $useCase)
    {
        $useCase->execute($request->dto());
    }
}
```

---

### 6.2 Application Service

```php
class CreateOrder
{
    public function __construct(
        OrderRepository $orders,
        PaymentGateway $gateway
    ) {}

    public function execute(OrderDTO $dto)
    {
        // business logic
    }
}
```

---

### 6.3 Repository

```php
interface OrderRepository
{
    public function save(Order $order);
}
```

---

## 7. Testing

### 7.1 Unit Test Service

```php
$this->mock(PaymentGateway::class)
     ->shouldReceive('pay')
     ->once();
```

---

### 7.2 Test Middleware

```php
$this->get('/dashboard')
     ->assertRedirect('/login');
```

---

### 7.3 Test Facade

```php
Event::fake();
Queue::assertPushed(OrderJob::class);
```

---

## 8. Clean Architecture trong Laravel

### Nguyên tắc

* Domain không phụ thuộc Laravel
* Laravel chỉ là framework
* Interface nằm trong Domain
* Implementation nằm trong Infrastructure

---

## 9. So sánh Laravel vs Rails

| Laravel           | Rails            |
| ----------------- | ---------------- |
| Service Container | dry-container    |
| Facade            | Concern / Helper |
| Middleware        | Rack Middleware  |
| Service Provider  | Initializer      |
| Eloquent          | ActiveRecord     |

---

## 10. Lộ trình làm chủ Laravel

### Tuần 1

* Request lifecycle
* Service Container
* Service Provider

### Tuần 2

* Middleware
* Facade
* Kiến trúc Service / Repository

### Tuần 3

* Clean Architecture
* Event / Queue
* Testing nâng cao

---

> 📌 Ghi chú: Laravel mạnh nhất khi bạn **không viết logic trong Controller**, **không phụ thuộc Facade cho domain**, và **để Service Container làm đúng vai trò của nó**.
