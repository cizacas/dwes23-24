### Base de datos
Laravel facilita la configuración y el uso de diferentes tipos  de base de datos: MySQL,PostgreSQL, SQLite y SQL  Server.

En el fichero de configuración __config/database.php__  tenemos que indicar todos los parámetros de acceso a  nuestras bases de datos y además especificar cuál es la  conexión que se utilizará por defecto.

En Laravel podemos hacer uso de varias bases de datos a  la vez, aunque sean de distinto tipo. Por defecto  se  accederá a la que especifiquemos en la configuración y si  queremos acceder a otra conexión lo tendremos que indicar  expresamente al realizar la consulta.

#### Configuración
Lo primero que tenemos  que hacer para trabajar con bases de datos es completar la
configuración.

Si editamos el fichero con la configuración __config/database.php__ podemos ver en primer lugar  la siguiente línea: 'default' => env('DB_CONNECTION', 'mysql'),

Este valor indica el tipo de base de datos a utilizar  por defecto. El método   env('DB_CONNECTION', 'mysql') lo que hace es obtener el valor de la variable  DB_CONNECTION del __fichero .env__.

En el fichero de configuración __config/database.php__, dentro de la sección connections, podemos encontrar  todos los campos utilizados para configurar cada tipo de base de datos, en concreto la base  de datos tipo mysql tiene los siguientes valores:

```php
 'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],

```
Podríamos modificar directamente los valores del archivo config/database.php,  pero cuando trabajamos en el desarrollo de una aplicación, la mayoría de las  veces,__las credenciales de la base de datos de nuestro entorno local es diferente  de las credenciales de nuestro entorno de producción o pruebas__.

Por lo que no es conveniente tener datos de configuración variables dentro de  nuestro código.

Para solventar este problema podemos hacer uso de las variables de entorno __contenidas en el fichero .env__

Así que para configurar la base de datos vamos a cambiar las siguientes líneas en  el __fichero .env de la raíz del proyecto__:

DB_CONNECTION=mysql

DB_HOST=127.0.0.1

DB_PORT=3306

DB_DATABASE=(nombre de la base de datos)laravel_zoologico

DB_USERNAME=(nombre de usuario)root

DB_PASSWORD=(contraseña de acceso)

#### Migraciones: Schema Builder
Laravel utiliza __las migraciones__ para poder definir y crear las  tablas de la base de datos desde código, y de esta manera tener  un control de las versiones de las mismas.

Permiten que un equipo trabaje sobre una base de datos  añadiendo y modificando campos,manteniendo un histórico de los  cambios realizados y del estado actual de la base de datos. 

Las  migraciones se utilizan de forma conjunta con __la herramienta Schema builder__ para gestionar el esquema de base de datos de la  aplicación.

Para poder empezar a trabajar con las migraciones es necesario en  primer lugar crear la tabla de migraciones. 
Para esto tenemos que  ejecutar el siguiente comando de Artisan:
```php 
php artisan migrate
```
Para crear una nueva migración se utiliza el comando de Artisan make:migration,
al cuál le pasaremos el nombre del fichero a crear:
```php 
php artisan make:migration create_users_table
```
Esto nos creará un fichero de migración en la carpeta database/migrations con el  nombre __*TIMESTAMP*_create_users_table.php__.

Al añadir un timestamp a las migraciones el sistema sabe el orden en el que tiene
que ejecutar (o deshacer) las mismas.

Si lo que queremos es añadir una migración que modifique los campos de una  tabla existente tendremos que ejecutar el siguiente comando:
```php 
php artisan make:migration add_votes_to_user_table --table=users
```
En este caso se creará también un fichero en la misma carpeta, con el nombre __*TIMESTAMP*_add_votes_to_user_table.php__ preparado para modificar los campos de dicha tabla.

Por defecto, al indicar el nombre del fichero de migraciones se suele seguir siempre  el mismo patrón (aunque el realidad el nombre es libre).
* Si es una migración que  crea una tabla el nombre tendrá que ser __create_*table-name*_table__ 
* Si es una  migración que modifica una tabla será __*action*_to_*table-name*_table__

##### Estructura de una migración

El fichero o clase PHP generada para una migración siempre  contiene __los métodos up y down__.
* En el método up es donde tendremos crear o modificar la tabla
* En el método down tendremos que deshacer los cambios que se hagan  en el up (eliminar la tabla o eliminar el campo que se haya añadido).
  
Esto nos permitirá poder ir añadiendo y eliminando cambios sobre la  base de datos y tener un control o histórico de los mismos.

Para especificar la tabla a crear o modificar, así como las columnas  y tipos de datos de las mismas, se utiliza __la clase Schema__.

Esta clase tiene una serie de métodos que nos permitirá especificar  la estructura de las tablas independientemente del sistema de base  de datos que utilicemos.

##### Crear y borrar una tabla
En __la sección up__ para añadir una nueva tabla a la base de datos se utiliza el siguiente
constructor:
```php
 Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
 });
```
Donde el primer argumento es el nombre de la tabla y el segundo es una  función que recibe como  parámetro __un objeto del tipo Blueprint__ que  utilizaremos para configurar las columnas de la tabla.
En __la sección down__ de la migración tendremos que eliminar la tabla que  hemos creado, para esto usaremos el método:
```php
Schema::dropIfExists('users');
```
Al crear una migración con el comando de Artisan __make:migration__ ya nos  viene este código añadido por defecto, la creación y eliminación de la  tabla que se ha indicado y además se añaden un par de columnas por  defecto (id y timestamps).


##### Añadir columnas
El constructor Schema::create recibe como segundo parámetro una función que nos permite especificar las columnas que va a tener dicha tabla.
En esta función podemos ir añadiendo todos los campos que queramos,  indicando para cada uno de ellos su tipo y nombre, y además si queremos  también podremos indicar una serie de modificadores como  valor por  defecto, índices, etc. 
Ejemplo:
```php
 Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
```
Podemos consultar [todos los tipos de datos](https://laravel.com/docs/master/migrations#creating-columns)

##### Añadir índices
Schema soporta los siguientes tipos de índices:
|Comando| Descripción |
| ------------- | ------------------------------ |
|$table->primary('id');|Añadir una clave primaria|
|$table->primary(array('first','last'));|Definir una clave primaria compuesta|
|$table->unique('email');|Definir el campo como UNIQUE|
|$table->index('state');|Añadir un índice a una columna|

En la tabla se especifica como añadir estos índices  después de crear el campo, pero también permite  indicar estos índices a la vez que se crea el campo como figura en el ejemplo anterior al definir el campo email.

##### Claves ajenas
Con Schema también podemos definir claves ajenas entre tablas:
```php
$table->integer('user_id')->unsigned();
$table->foreign('user_id')->references('id')->on('users');
```
En este ejemplo en primer lugar añadimos la columna "user_id" de tipo UNSIGNED  INTEGER (siempre tendremos que crear primero la columna sobre la que se va a  aplicar la clave ajena).

A continuación creamos la clave ajena entre la columna "user_id" y la columna "id"  de la tabla "users".
También podemos especificar las acciones que se tienen que realizar para "on  delete" y "on update":
```php
$table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
```
Para eliminar una clave ajena, en __el método down__ de la migración tenemos que utilizar el siguiente código:
```php
$table->dropForeign('posts_user_id_foreign');
```
Para indicar la clave ajena a eliminar tenemos que seguir el siguiente patrón para especificar el __nombre *tabla*_*columna*_foreign__ donde:
* "tabla" es el nombre de  la tabla actual
* "columna" el nombre de la columna sobre la que se creo la clave  ajena.

##### Ejecutar migraciones
Después de crear una migración y de definir los campos de la tabla tenemos que lanzar la
migración con el siguiente comando:
```php
php artisan migrate
```
>Si nos aparece el error "class not found" lo podremos solucionar indicando a composer que vuelva a compilar el autocargador. Desde la carpeta del proyecto, ejecutamos este comando:
```php
composer dump-autoload
```
El comando migrate aplicará la migración sobre la base de datos. Si hubiera más de una migración pendiente se ejecutarán todas.

Para cada migración se llamará a su método up para que cree o modifique la base de datos.  

Posteriormente en caso de que queramos deshacer los últimos cambios podremos ejecutar:
```php
php artisan migrate:rollback
```
O si queremos deshacer todas las migraciones:
```php
php artisan migrate:reset
```
Un comando interesante cuando estamos desarrollando un nuevo sitio web es migrate:fresh, el
cual deshará todos los cambios y volver a aplicar las migraciones:
```php
php artisan migrate:fresh
```
Además si queremos comprobar el estado de las migraciones, para ver las que ya están  instaladas y las que quedan pendientes, podemos ejecutar:
```php
php artisan migrate:status
```
:computer: Hoja06_MVC_07

#### Modelos de datos mediante ORM
El mapeado objeto-relacional (Object-Relational  mapping o ORM) es una técnica de programación para convertir datos entre un lenguaje de programación orientado a objetos y una base de datos relacional como motor de persistencia.

Esto posibilita el uso de las características propias de la orientación a objetos, podremos acceder directamente a los campos de un objeto para leer los datos de una base de datos o para insertarlos o modificarlos.

__Laravel incluye su propio sistema de  ORM  llamado Eloquent__. Para cada tabla de la base datos tendremos que definir su correspondiente modelo, el cual se utilizará para interactuar desde código con la tabla.

##### Definición de un modelo
Para definir un modelo que use Eloquent únicamente tenemos que crear una clase que herede  de la clase Model. Podemos hacerlas “a mano”, pero es mucho más fácil y rápido crear los  modelos usando el comando make:model de Artisan:
```php
php artisan make:model User
```
##### Nombre del modelo
En general el nombre de los modelos se pone en singular con la primera letra en mayúscula, mientras que el nombre de las tablas suele estar en plural.

Gracias a esto, al definir un modelo no es necesario indicar el nombre de la tabla asociada, sino que Eloquent automáticamente buscará la tabla transformando el nombre del modelo a  minúsculas y buscando su plural (en inglés).

En el ejemplo anterior que hemos creado el modelo User buscará la tabla de la base de  datos llamada users y en caso de no encontrarla daría un error.
Si la tabla tuviese otro nombre lo podemos indicar usando la propiedad protegida $table del modelo:
```php
class User extends Model
{
protected $table = 'my_users';
}
```
##### Clave primaria
Laravel también asume que cada tabla tiene declarada una clave primaria con el nombre id.
En el caso de que no sea así y queramos cambiarlo tendremos que sobrescribir el valor de la
propiedad	protegida	$primaryKey	del	modelo.
```php
protected	$primaryKey='my_id';.
```
##### Timestamps
Otra propiedad que en ocasiones tendremos que establecer son los timestamps automáticos.
Por defecto Eloquent asume  que todas las  tablas contienen  los campos updated_at y  created_at (los cuales los podemos añadir muy fácilmente con Schema añadiendo $table->timestamps() en la migración).

Estos campos se actualizarán automáticamente cuando se cree un nuevo  registro o se  modifique.

En el caso de que no queramos utilizarlos (y que no estén añadidos a la tabla) tendremos que  indicarlo en el modelo o de otra forma nos daría un error. 

Para indicar que no los actualice  automáticamente tendremos que modificar el valor de la propiedad pública $timestamps a false, por ejemplo: 
```php
public $timestamps = false;
```
Ejemplo:
```php
class User extends Model
{
    protected $table = 'my_users';  
    protected $primaryKey = 'my_id';  
    public $timestamps = false;
}
```
##### Uso de un modelo de datos
El sitio correcto donde realizar estas acciones es en el __controlador__, el cual se los tendrá que pasar a la vista ya preparados para su visualización.

Es importante indicar al inicio de la clase el espacio de nombres del modelo o modelos a  utilizar.
Por ejemplo, si vamos a usar los modelos User y Orders tendríamos que añadir:
use App\User;
use App\Orders;

##### Consultar datos
Para obtener todas las filas de la tabla asociada a un modelo usaremos __el método all()__:
Ejemplo:
```php
$users = User::all();  
foreach( $users as $user ) {
    echo $user->name;
}
```
Este método nos devolverá __un array de resultados__, donde __cada item del array__ será una __instancia del modelo User__. Gracias a esto al obtener un elemento del array podemos acceder  a los campos o columnas de la tabla como si fueran propiedades del objeto ($user->name).

También podremos utilizar __where, orWhere, first, get, orderBy, groupBy, having, skip, take,__  etc. para elaborar las consultas.

Eloquent también incorpora __el método find($id)__ para buscar un elemento a partir del  identificador único del modelo, por ejemplo:
```php
$user = User::find(1);
```
Si queremos que se lance una excepción cuando no se encuentre un modelo podemos utilizar  __los métodos findOrFail o firstOrFail__. Esto nos permite capturar las excepciones y mostrar un  error 404 cuando sucedan.
```php
$model = User::findOrFail(1);
$model = User::where('votes', '>', 100)->firstOrFail();
```
A continuación se incluyen otros ejemplos de consultas usando Eloquent con algunos de los  métodos:
```php
// Obtener 10 usuarios con más de 100 votos
$users = User::where('votes', '>', 100)->take(10)->get();
// Obtener el primer usuario con más de 100 votos
$user = User::where('votes', '>', 100)->first();
```
También	podemos	utilizar los métodos agregados para	calcular el	total de registros obtenidos, o el máximo, mínimo, media o suma de una determinada columna. Por ejemplo:
```php
$count = User::where('votes', '>', 100)->count();
$price = Orders::max('price');
$price = Orders::min('price');
$price = Orders::avg('price');
$total = User::sum('votes');
```
##### Insertar datos
Para insertar un  dato en  una  tabla de la base de datos tenemos  que crear una __nueva instancia__ de dicho modelo, __asignar los valores__ y guardarlos con el __método save()__:
```php
$user = new User(); 
$user->name = "Juan"; 
$user->save();
```
Para obtener el identificador asignado en la base de datos después de guardar, lo podremos recuperar accediendo al campo id del objeto que habíamos creado, por  ejemplo:
```php
$insertedId = $user->id;
```
##### Actualizar datos
Para actualizar una instancia de un modelo sólo tendremos que recuperar la instancia  que queremos actualizar, a continuación modificarla y por último guardar los datos:
```php
$user = User::find(1); 
$user->email = "juan@gmail.com"; 
$user->save();
```
##### Borrar datos
Para borrar fila de una tabla en la base de datos tenemos que usar su __método delete()__:
```php
$user = User::find(1);	
$user->delete();
```
Si queremos  borrar un conjunto de resultados también podemos usar el método  delete():
```php
$affectedRows = User::where('votes', '>', 100)->delete();
```
### Seeders
Los	seeders	sirven para	rellenar la	base de	datos con datos iniciales.
Para ello se puede rellenar __el método run__ de database/seeds/DatabaseSeeder.php
Luego ejecutaremos la migración con:
```php
php artisan db:seed
```
Pero lo habitual es crear distintos	seeders para cada modelo de nuestra base de datos. Para crearlos usamos:
```php
php artisan make:seeder UserSeeder
```
Nos creará __una clase con el método run__. Ahí escribiremos los datos de creación de objetos.
Por	último,	desde el método	run	de la clase	DatabaseSeeder llamaremos a las clases Seeder creadas:
```php
$this->call(UserSeeder::class);
```
:computer: Hoja06_MVC_08

### Factories

las factorias pasan a ser clases
para crearla haremos
```php
php artisan make:factory UserFactory
```
Nos creará una clase en la carpeta database/factories. Completaremos el atributo $model y el método definition:

```php
class UserFactory extends Factory
{
    public function definition()
    {
        return [
            'name' => fake()->name(),
            'email' => fake()->unique()->safeEmail(),
            'email_verified_at' => now(),
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),
        ];
    }
}
```
Luego desde una clase Seeder podemos llamar a la clase:

```php
class DatabaseSeeder extends Seeder
{
    public function run()
    {
        \App\Models\User::factory(10)->create();

    }
}
```
Para generar los datos se utiliza [la librería Faker](https://github.com/fzaninotto/Faker#formatters)

## Datos de entrada

Para conseguir acceso los datos de entrada del usuario Laravel utiliza __inyección de dependencias__.
Añade __la clase Request__ al constructor o método del controlador en el que lo  necesitemos.
Laravel	se encargará de	inyectar dicha dependencia ya inicializada y directamente podremos usar este parámetro para obtener los datos de entrada.

Algunos ejemplos:
```php
class UserController extends Controller
{
    public function store(Request $request){

       
        $name=$request->nombre;
        if ($request->has('nombre')) // para comprobar que existe
        {
            //operaciones dentro del if
        }
        //...
    }
    public function edit(Request $request, $id){
        // hago operaciones
    }
}
```
## Ficheros de entrada

Laravel facilita una serie de clases para trabajar con los ficheros de entrada. 
Por ejemplo: para obtener un fichero que se ha enviado en el campo con nombre photo y guardarlo en  una variable, tenemos que hacer:
```php
$file = $request->photo;
```
Si queremos podemos comprobar si un determinado campo tiene un fichero asignado:
```php
if ($request->hasFile('photo')) {
     … 
}
```
Laravel dispone de una nueva librería que nos permite gestionar el acceso y escritura de ficheros en un almacenamiento. Simplemente tenemos que configurar el fichero __config/filesystems.php__ y posteriormente los podremos usar.

Por ejemplo, para almacenar un fichero subido mediante un formulario tenemos que usar el __método store__ indicando como parámetro la ruta donde queremos almacenar el fichero (sin el nombre del fichero):
```php
$path = $request->photo->store('images');
$path = $request->photo->store('images’, ‘miAlmacenamiento'); // Especificar un almacenamiento
```
Estos métodos devolverán el path hasta el fichero almacenado de forma relativa a la raíz de disco configurada. Para el nombre del fichero se generará automáticamente un UUID (identificador único universal).
Si queremos especificar nosotros el nombre tendríamos que usar __el método storeAs__:
```php
$path = $request->photo->storeAs('images', 'filename.jpg');
$path = $request->photo->storeAs('images', 'filename.jpg', ' miAlmacenamiento ');
```
:computer: Hoja06_MVC_09

## Control de usuarios : Laravel Jetstream

Laravel incluye una serie de métodos y clases que harán que la implementación del control de usuarios sea muy rápida y sencilla.
Casi todo el trabajo ya está hecho, sólo tendremos que indicar dónde queremos utilizarlo y algunos pequeños detalles de configuración.
Por defecto, al crear un nuevo proyecto de Laravel, ya se incluye  todo lo necesario:
* La __configuración__ predeterminada en __config/auth.php__.
* La __migración__ para la base de datos de la __tabla de usuarios__ con todos los campos necesarios.
* El __modelo de datos de usuario__ (User.php) dentro de la carpeta  App\Models con toda la implementación necesaria.

La __configuración__ del sistema de autenticación se puede encontrar en el fichero __config/auth.php__. Podremos:

* cambiar el sistema de autenticación (que por defecto es a través de Eloquent)
* cambiar el modelo de datos usado para los usuarios (por defecto será User)
* cambiar la tabla de usuarios (que por defecto será users).
  
La __migración__ de la tabla de usuarios (llamada users) también está incluida  (ver carpeta __database/migrations__). Por defecto incluye todos los campos necesarios, pero si necesitamos alguno más lo podemos añadir para guardar por ejemplo, la dirección o el teléfono del usuario.

En la carpeta __App\Models__ se encuentra el __modelo de datos__ (llamado  User.php) para trabajar con los usuarios. Esta clase ya incluye toda la  implementación necesaria y por defecto no tendremos que modificar nada. Pero si queremos podemos modificar esta clase para añadirle más métodos o relaciones con otras tablas, etc.

Laravel Jetstream desde laravel 8, contiene las siguientes funcionalidades:
* Verificación por correo electrónico
* Autenticación de dos pasos
* Administrador de sesiones
* Soporte de API
* Gestión de equipos
* etc...
  
Para instalarlo hacemos lo siguiente:

```php
composer require laravel/jetstream
php artisan jetstream:install livewire
npm install 
npm run dev
php artisan migrate
```
### Rutas
Cuando ejecutamos los comandos nos añadirá las rutas necesarias en el fichero routes/web.php

### Vistas
Al ejecutar los comandos anteriores también se generarán todas las vistas necesarias para realizar el login, registro y para recuperar la contraseña.
Todas estas	vistas las	podremos encontrar en la carpeta __resources/views/auth__
Si lo deseamos podemos modificar el contenido y diseño de cualquier vista, así como del layout, lo único que tenemos que mantener igual es la URL a la que se envía el formulario y los nombres de cada uno de los inputs del formulario.

### Autenticación de un usuario
Si accedemos a la ruta login, introducimos unos datos y éstos son correctos, se creará la sesión del usuario y se le redirigirá a la ruta "/dashboard".

Si queremos cambiar esta ruta tenemos que definir la constante HOME en  el controlador RouteServiceProvider, por ejemplo:
```php
public const HOME='/';
```
### Configuraciones

En el archivo de configuración situado en __config/jetstream.php__ podremos añadir ciertas características:
* Features::profilePhotos() //Para que se muestre foto en el perfil del usuario
* Features::api() //Crear una API para crear, leer, actualizer y eliminar usuarios
* Features::teams() //Creación de equipos. Un usuario puede pertenecer a uno o varios [equipos](https://jetstream.laravel.com/1.x/features/teams.html)

 Si usamos __Livewire__ podemos publicar los componentes de Blade

```php
php artisan vendor:publish --tag=jetstream-views
```
Esto hará que se publique en __resources/views/vendor/jetstream/components__ todos los componentes ya desarrollados. Luego podremos modificar toda la “vista” de Jetstream.

### Registro de un usuario
Si accedemos a la ruta register	nos	aparecerá la vista con el formulario de registro.

### Registro manual de un usuario
Si queremos añadir un usuario manualmente lo podemos hacer de forma normal usando el modelo User de Eloquent. La única  precaución es cifrar la contraseña que se va a almacenar.
Un ejemplo es el siguiente:
```php
public function store(Request $request) {
    $user = new User();
    $user->name  = $request->name;
    $user->email = $request->email;
    $user->password = bcrypt( $request->password );
    $user->save();
}
```
### Acceder a los datos del usuario autenticado
Una vez que el usuario está autenticado podemos acceder a los datos del
mismo a través del método Auth::user():
```php
$user = Auth::user();
```
Este método nos devolverá null en caso de que no esté autenticado. Si estamos seguros de que el usuario está autenticado (porque estamos en una ruta protegida) podremos acceder directamente a sus propiedades:
```php
$email = Auth::user()->email;
```
Para utilizar la clase Auth tenemos que añadir el espacio de nombres use Illuminate\Support\Facades\Auth;, de otra forma nos aparecerá un error indicando que no puede encontrar la clase.

### Cerrar la sesión
Si accedemos a la ruta logout por POST se cerrará la sesión.
Para  cerrar manualmente la  sesión	del usuario actualmente	autenticado tenemos que utilizar el método:
```php
Auth::logout();
```
Posteriormente podremos	hacer una redirección a	una	página principal para usuarios no autenticados.

### Comprobar si un usuario está autenticado
Para comprobar si el usuario actual se ha autenticado en la aplicación  podemos utilizar el método Auth::check() de la forma:
```php
if (Auth::check()) {
// El usuario está correctamente autenticado
}
```
Sin embargo, lo recomendable es utilizar __Middleware__ para realizar esta comprobación antes de permitir el acceso a determinadas rutas.

## Middleware o filtros
Los Middleware son un mecanismo proporcionado por Laravel para __filtrar las peticiones HTTP__ que se realizan a una aplicación.

Un filtro o middleware se define como una clase PHP almacenada en un fichero dentro de la carpeta __App/Http/Middleware__.

Cada middleware se encargará de aplicar un tipo concreto de filtro y de decidir qué realizar con la petición realizada: permitir su ejecución, dar un error o redireccionar a otra página en caso de no permitirla.

Laravel incluye varios filtros por defecto.Uno de ellos es el encargado de realizar la autenticación de los usuarios. Este  filtro lo podemos aplicar sobre una ruta, un conjunto de rutas o sobre un  controlador en concreto. Este middleware se encargará de filtrar las peticiones  a dichas rutas: en caso de estar logueado y tener permisos de acceso le permitirá continuar con la petición, y en caso de no estar autenticado lo  redireccionará al formulario de login.

Laravel incluye middleware para gestionar la autenticación, el modo mantenimiento, la protección contra CSRF (Cross-site request forgery o falsificación de petición en sitios cruzados), y algunos más.

Además de éstos podemos crear nuestros propios Middleware
### Definir un nuevo Middleware
Para crear un nuevo Middleware podemos utilizar el comando de Artisan:
```php
php artisan make:middleware MyMiddleware
```
Este comando creará la clase MyMiddleware dentro de la carpeta __App/Http/Middleware__
```php
class MyMiddleware
{
    /**
    Handle an incoming request.
    @param \Illuminate\Http\Request $request
    @param \Closure $next
    @return mixed
    */
    public function handle(Request $request, Closure $next)
    {
        return $next($request);
    }
}
```
El código generado por Artisan ya viene preparado para que podamos escribir  directamente la implementación del filtro a realizar dentro de la función handle.

Esta función sólo incluye el valor de retorno con una llamada a	return $next($request); que lo que hace es continuar con la petición y ejecutar el método que tiene que procesarla.

Como entrada el método handle recibe dos parámetros:
* $request: en la cuál nos vienen todos los parámetros de entrada de la petición.
* $next: el método o función que tiene que procesar la petición.

Por ejemplo podríamos crear un filtro que redirija al home si el usuario tiene menos de 18 años y en otro caso que le permita acceder a la ruta:
```php

    public function handle(Request $request, Closure $next)
    {
        if ($request->input('age') < 18) { 
            return redirect('home’)
        };

        return $next($request);
    }
```
Como hemos dicho antes, podemos hacer tres cosas con una petición:
* Si todo es correcto permito que la petición continúe:
```php  
return $next($request);
```
* Realizar una redirección a otra ruta para no permitir el acceso:
```php  
return redirect('home');
```
* Lanzar una excepción o llamar al método abort para mostrar una página de  error:
```php  
abort(403, 'Unauthorized action.');
```

Laravel permite la utilización de Middleware de tres formas distintas:
* global
* asociado a rutas o grupos de rutas
* asociado a un controlador o a un método de un controlador.
En los tres casos será necesario registrar primero el Middleware en la __clase App/Http/Kernel.php__.

### Middleware global

Para hacer que un Middleware se ejecute con todas las peticiones HTTP realizadas a una aplicación  simplemente lo tenemos que registrar en el __array $middleware__ definido en la __clase App/Http/Kernel.php__. 
```php  
protected $middleware = [
\App\Http\Middleware\TrustProxies::class,
\App\Http\Middleware\CheckForMaintenanceMode::class,
\Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
\App\Http\Middleware\TrimStrings::class,
\Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
\App\Http\Middleware\MyMiddleware::class,
];
```
En este ejemplo hemos registrado la clase MyMiddleware al final del array. Si queremos que nuestro middleware se ejecute antes que otro filtro simplemente tendremos que colocarlo antes en la posición del array.

### Middleware asociado a rutas

También tendremos que registrarlo en el fichero App/Http/Kernel.php, pero en el array $routeMiddleware. Al añadirlo a este array además tendremos que asignarle un nombre o clave, que será el que después utilizaremos asociarlo con una ruta.
En primer lugar añadimos nuestro filtro al array y le asignamos el nombre "es_mayor_de_edad":
```php
protected $routeMiddleware = [
'auth' => \App\Http\Middleware\Authenticate::class,  'auth.basic' =>
\Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,  'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
'es_mayor_de_edad' => \App\Http\Middleware\MyMiddleware::class,
];
```
Una vez registrado ya lo podemos utilizar en el fichero de  rutas mediante la clave o nombre asignado, por ejemplo:
```php
Route::get('/', function () {
//
})->middleware(‘es_mayor_de_edad’);
```
En el ejemplo anterior hemos asignado el middleware con clave es_mayor_de_edad a la ruta /. Si la petición supera el filtro entonces se ejecutara la función asociada.
Laravel también permite asignar múltiples filtros a una  determinada ruta:
```php
Route::get('/', function () {
//
})->middleware(['first', 'second']);
```
#### Proteger rutas
El sistema de autenticación de Laravel incorpora una serie de filtros o para comprobar que el usuario que accede a una determinada ruta o grupo de rutas esté autenticado.
Para proteger el acceso a rutas y solo permitir su visualización por usuarios correctamente autenticados usaremos el middleware
\Illuminate\Auth\Middleware\Authenticate.php cuyo alias es auth.
Para utilizar este middleware tenemos que editar el fichero routes/web.php y modificar las rutas que queramos proteger:
```php
// Para proteger una cláusula:  
Route::get('admin/catalog', function(){
 ...
})->middleware('auth’);

// Para proteger una acción de un controlador:
Route::get('profile’, [ProfileController::class,’show’])->middleware('auth');
```
:computer: Hoja06_MVC_10

