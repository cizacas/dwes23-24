# Plantilla de administración para Bootstrap [AdminLTE](https://adminlte.io/)
- [Plantilla de administración para Bootstrap AdminLTE](#plantilla-de-administración-para-bootstrap-adminlte)
  - [Librerias y Plugins](#librerias-y-plugins)
  - [Componentes](#componentes)
  - [Istalación a través de composer](#istalación-a-través-de-composer)
  - [Manejo de roles con Enums en Laravel](#manejo-de-roles-con-enums-en-laravel)
  - [Middleware para controlar el acceso de Administradores](#middleware-para-controlar-el-acceso-de-administradores)
  - [Crear el usuario administrador](#crear-el-usuario-administrador)
  - [Controlador para el panel AdminLTE](#controlador-para-el-panel-adminlte)
  - [Utilizando el layout de AdminLTE en Laravel](#utilizando-el-layout-de-adminlte-en-laravel)
  - [Defininir el sistema de rutas](#defininir-el-sistema-de-rutas)
  - [Ajustar el inicio de sesión para los administradores](#ajustar-el-inicio-de-sesión-para-los-administradores)
  - [Módulos adicionales que gestionan roles y permisos en Laravel](#módulos-adicionales-que-gestionan-roles-y-permisos-en-laravel)

## Librerias y Plugins 
AdminLTE viene con una serie de librerías y plugins JavaScript que añaden funcionalidades y efectos visuales a la interfaz de usuario. Algunos de los plugins que incluye AdminLTE son:
1.	Bootstrap: es un framework de diseño web que proporciona un conjunto de estilos y componentes para crear sitios y aplicaciones web.
2.	jQuery: es una librería JavaScript que facilita el uso de funciones y efectos en la interfaz de usuario.
3.	Morris.js: es un plugin JavaScript que permite crear gráficos de línea, barras y áreas de manera fácil y rápida.
4.	Flot: es un plugin JavaScript que permite crear gráficos y diagramas en tiempo real.
5.	Sparkline: es un plugin JavaScript que permite crear gráficos pequeños y compactos que muestran datos de manera atractiva.
6.	jQuery Knob: es un plugin JavaScript que permite crear controles giratorios y diales gráficos.
7.	jQuery Slider: es un plugin JavaScript que permite crear controles deslizantes y ruedas de selección.
8.	jQuery Inputmask: es un plugin JavaScript que permite validar y formatear datos de entrada, como números de teléfono y direcciones de correo electrónico.
9.	jQuery Validation: es un plugin JavaScript que permite validar formularios y asegurar que los datos ingresados cumplan con las reglas y criterios establecidos.
10.	jQuery UI: es un conjunto de plugins JavaScript que añaden funcionalidades y efectos visuales a elementos comunes de la interfaz de usuario, como menús, botones y tabs.

## Componentes

AdminLTE ofrece de forma gratuita una amplia variedad de componentes y elementos de diseño que pueden ser utilizados para crear una interfaz de usuario atractiva y fácil de usar. Algunos de los componentes que ofrece AdminLTE son:
1.	Menús de navegación: permite a los usuarios navegar por las diferentes secciones de la aplicación y acceder a las opciones de configuración y preferencias.
2.	Widgets: son elementos gráficos que muestran información importante de manera clara y concisa, como gráficos, tablas y otros tipos de datos.
3.	Grillas: permiten presentar información de manera ordenada y estructurada en forma de tablas o listas.
4.	Formularios: son elementos que permiten a los usuarios ingresar y enviar datos a la aplicación.
5.	Botones y enlaces: son elementos de acción que permiten a los usuarios realizar tareas específicas, como guardar o eliminar datos.
6.	Iconos: son elementos gráficos utilizados para mejorar la visualización y legibilidad de la interfaz de usuario.
7.	Alertas y mensajes: son elementos que permiten mostrar avisos y mensajes importantes a los usuarios de la aplicación.
8.	Modales: son ventanas emergentes que se muestran encima de la interfaz de usuario principal y permiten presentar contenido adicional o realizar tareas específicas.
9.	Gráficos y diagramas: son elementos gráficos que permiten visualizar datos y estadísticas de manera atractiva y fácil de entender.
10.	Tablas: son elementos que permiten presentar información de manera estructurada y ordenada en forma de filas y columnas.
11.	Páginas predefinidas: son diseños predefinidos que pueden ser utilizados para crear páginas web de manera rápida y sencilla.
12.	Estilos de diseño: son elementos de diseño que permiten personalizar la apariencia de la interfaz de usuario, como colores, tipografía y otros aspectos visuales.
13.	Plugins JavaScript: son librerías de código que añaden funcionalidades y efectos visuales a la interfaz de usuario.
14.	Documentación y recursos: son materiales que brindan información y ayuda para el uso y personalización de AdminLTE.

Su código también figura en [github](https://github.com/ColorlibHQ/AdminLTE/releases)

## Istalación a través de composer

```php
composer require jeroennoten/laravel-adminlte
php artisan adminlte:install
```

Una vez instalado el paquete, debes publicar los archivos de configuración del paquete utilizando el siguiente comando:

```php
php artisan adminlte:install --only=config
composer require laravel/ui
php artisan ui bootstrap --auth
php artisan adminlte:install --only=auth_views
npm install && npm run dev
```

## Manejo de roles con Enums en Laravel

```php
namespace App\Enums;
enum UserType: string{
    case Admin = 'admin';
    case User = 'user';

    public static function forMigration(): array { return collect(self::cases())->map(function ($enum) {

        if (property_exists($enum, "value")) return $enum->value;
        return $enum->name;
    })->values()->toArray();    
    }  
}
```
Ahora vamos a actualizar **la migración de usuarios** para utilizar nuestro **enum UserType**:
```php

use App\Enums\UserType;
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration{
/**
* Run the migrations.
*
* @return void
*/
public function up(){

    Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->enum("user_type", UserType::forMigration())->default(UserType::Admin->value);
    $table->string('name');
    $table->string('email')->unique();
    $table->timestamp('email_verified_at')->nullable();
    $table->string('password');
    $table->rememberToken();
    $table->timestamps();
});
    /// ...
}
```
Hacemos también un ajuste en nuestro **modelo User** para hacer buen uso de nuestra configuración:

```php
namespace App\Models;
use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable{
    use HasApiTokens, HasFactory, Notifiable;
    protected $fillable = ['user_type','name','email','password',];

    /// ...

    public function isAdmin(): Attribute { 
        return new Attribute(get: fn () => $this->user_type === \App\Enums\UserType::Admin,);
    }   
}
```

## Middleware para controlar el acceso de Administradores
Es importante proteger el acceso a la sección de administración de nuestras aplicaciones. En Laravel esta tarea la podemos gestionar fácilmente con Middlewares, creemos uno con el siguiente código:

```php
php artisan make:middleware IsAdmin
```

Instala:

```php
namespace App\Http\Middleware;
use Closure;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

class IsAdmin{
    public function handle(Request $request, Closure $next): Closure|RedirectResponse|Response
    {
        if (! $request->user()->isAdmin()) {
            return redirect()->route('home');
        }
        return $next($request);
    }
}
```

**El Middleware IsAdmin** lo utilizaremos para proteger las rutas de administración más adelante, pero para ello, necesitamos registrarlo en el **Kernel Http** de la siguiente forma:

```php
protected $routeMiddleware = [
//...
'admin' => \App\Http\Middleware\IsAdmin::class,
];
```

## Crear el usuario administrador
Vamos a crear un **usuario administrador** para gestionar nuestro sistema a través de **los seeders** de Laravel:

```php
namespace Database\Seeders;
use Illuminate\Database\Seeder;
class DatabaseSeeder extends Seeder{

    public function run() {
        \App\Models\User::factory()->create([
            'user_type' => \App\Enums\UserType::Admin,
            'name' => 'Admin',
            'email' => 'admin@adminlte.com',]);
    }
}
```
Con esto ya lo tenemos todo listo y estamos en condiciones de iniciar controladores y rutas para nuestros administradores.

## Controlador para el panel AdminLTE

Creemos un **controlador AdminController** rápidamente, el cual se encargará de mostrar el panel AdminLTE a los administradores del sistema:

```php
php artisan make:controller AdminController
```
En el controlador:

```php
namespace App\Http\Controllers;
use Illuminate\Http\Request;
class AdminController extends Controller{

    public function index(Request $request){
        return view('admin.index');
    }   
}
```
## Utilizando el layout de AdminLTE en Laravel
El layout de administración de AdminLTE ya está diseñado, sólo debemos utilizarlo de la siguiente forma, en este caso en nuestro archivo de vista **admin/index.blade.php**:

```php
@extends('adminlte::page')
@section('title', 'Dashboard Administración')
@section('content_header')
<h1>Dashboard</h1>
@stop  // igual a @endsection
@section('content')
<p>Bienvenido al panel de administración de AdminLTE.</p>
@stop
```

## Defininir el sistema de rutas
Nuestro sistema de rutas web será sencillo, pero lo necesario para que tú más adelante puedas extenderlo a tus necesidades:

```php
use App\Http\Controllers\AdminController;
use App\Http\Controllers\HomeController;
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
return view('welcome');
});

Auth::routes();
Route::group(['middleware' => ['auth', 'admin']], function () {
Route::get('/admin', [AdminController::class, 'index'])->name('admin.index');
});

Route::group(['middleware' => ['auth']], function () {
Route::get('/home', [HomeController::class, 'index'])->name('home');
});
```
Como puedes ver, por un lado tenemos la ruta para la sección de administración, la cual está protegida por el middleware admin que hemos desarrollado anteriormente, y por otro lado la ruta home que se supone está pensada para mostrar la interfaz para usuarios identificados que no sean administradores.

## Ajustar el inicio de sesión para los administradores

Si ahora te diriges a la ruta /login y accedes con las credenciales de administrador que hemos creado anteriormente verás que Laravel te deja en la ruta /home, y es normal, necesitamos ajustar algunas cosas para que esto funcione como necesitamos.

Lo primero que necesitamos es actualizar el **LoginController** con lo siguiente:

```php
namespace App\Http\Controllers\Auth;
use App\Http\Controllers\Controller;
use App\Providers\RouteServiceProvider;
use Illuminate\Foundation\Auth\AuthenticatesUsers;

class LoginController extends Controller{
// ...
    protected function redirectTo(): string{
        if (auth()->user()->isAdmin()) {
            return '/admin';    }
        return '/home';
    }
}   
```
**El método redirectTo** en combinación con nuestro **Acceso isAdmin** hacen el trabajo que necesitamos, que es mandar al usuario identificado a la zona que corresponda dependiendo de si es administrador o no.

Lo segundo que necesitamos es ajustar el **RouteServiceProvider** para añadir la siguiente constante, la cual nos servirá a continuación:

```php
namespace App\Providers;
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Foundation\Support\Providers\RouteServiceProvider as ServiceProvider;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Support\Facades\Route;

class RouteServiceProvider extends ServiceProvider{
    public const HOME = '/home';
    public const ADMIN = '/admin';
    //...
}
```
Lo tercero y último que necesitamos es actualizar el **middleware RedirectIfAuthenticated** de la siguiente forma:
```php
namespace App\Http\Middleware;
use App\Providers\RouteServiceProvider;
use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class RedirectIfAuthenticated {

    public function handle(Request $request, Closure $next, ...$guards){

        $guards = empty($guards) ? [null] : $guards;
        foreach ($guards as $guard) {
            if (Auth::guard($guard)->check()) {
                if (auth()->user()->isAdmin())
                    {   return redirect(RouteServiceProvider::ADMIN);
                    }
            return redirect(RouteServiceProvider::HOME);
            }
        }
        return $next($request);
    }
}
```
A partir de ahora, si vuelves a acceder con el usuario administrador, verás que Laravel te deja en la ruta /admin, desde donde podrás ver el panel de administración de AdminLTE.

## Módulos adicionales que gestionan roles y permisos en Laravel

* [Laratrust](https://laratrust.santigarcor.me/)

* [Spatie](https://spatie.be)



