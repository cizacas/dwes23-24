# Rutas Singleton en Laravel

A partir de la versión 9.42 de Laravel Framework ha publicado una nueva funcionalidad muy interesante que afecta al sistema de rutas. La novedad son varios métodos nuevos que se han añadido a la API de rutas, concretamente los métodos son los siguientes:
* Route::singleton
* Route::singletons
* Route::apiSingleton
* Route::apiSingletons

Mientras que los dos primeros están pensados para las rutas web, los dos últimos están pensados para las rutas API, pero, ¿cómo funcionan estos nuevos métodos del sistema de rutas de Laravel y para qué sirven?

Estos nuevos métodos están pensados para recursos que únicamente deban tener una **única instancia en nuestra aplicación**, ¿y qué significa esto? Muy sencillo, un perfil de usuario, información de contacto de la aplicación etcétera, es decir, algo que sólo pueda ocurrir una vez, sea para un usuario o para el conjunto de la aplicación, no hay identificadores ni nada parecido en estos casos.

```php
use App\Http\Controllers\CompanyContactController;
use App\Http\Controllers\ProfileController;
use Illuminate\Support\Facades\Route;

// Route::apiSingleton(name: 'profile', controller: ProfileController::class);
// Route::apiSingleton(name: 'company-contact', controller: CompanyContactController::class);

Route::apiSingletons([
    'profile' => ProfileController::class,
    'company-contact' => CompanyContactController::class,
]);
```

## Nuevos flags en el comando make:controller

A partir de ahora también tenemos dos nuevos flags en el comando make:controller.

### --singleton

```php
php artisan make:controller CompanyContactController --singleton
```

### --creatable
```php
php artisan make:controller ProfileController --creatable
```
El flag creatable permitirá llevar a cabo todas las operaciones en nuestro singleton, incluso las de crear y eliminar, el resto es exactamente lo mismo. Para hacer que un singleton pueda definir estas rutas, simplemente deberíamos actualizar nuestras rutas por lo siguiente.

```php
use App\Http\Controllers\CompanyContactController;
use App\Http\Controllers\ProfileController;
use Illuminate\Support\Facades\Route;

// Route::singleton(name: 'profile', controller: ProfileController::class)->creatable();
// Route::singleton(name: 'company-contact', controller: CompanyContactController::class)->creatable();

Route::singletons([
  'profile' => ProfileController::class,
  'company-contact' => CompanyContactController::class,
], [
  'creatable' => true,
]);
```

