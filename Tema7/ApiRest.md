# API REST

## Que és
Una API REST (Representational State Transfer) es un estilo de arquitectura de software que se utiliza para diseñar sistemas de comunicación en red, especialmente en el contexto de aplicaciones web. Es un enfoque que se basa en el protocolo HTTP y se adhiere a ciertos principios y restricciones que facilitan la interacción entre sistemas de software de una manera simple y eficiente.

Algunas de las características clave de una API REST incluyen:

* __Recursos__: En una API REST, los datos se modelan como recursos, que son objetos o conceptos que pueden ser identificados mediante una URL única. Por ejemplo, en una API de redes sociales, los usuarios, publicaciones y comentarios serían considerados recursos.

* __Operaciones CRUD__: Las operaciones en los recursos se realizan a través de los métodos HTTP estándar, como GET (para recuperar datos), POST (para crear nuevos recursos), PUT (para actualizar recursos) y DELETE (para eliminar recursos). Estas operaciones reflejan las operaciones CRUD (Crear, Leer, Actualizar, Eliminar) en una base de datos.

* __Sin estado__: Cada solicitud a una API REST debe contener toda la información necesaria para entenderla. El servidor no mantiene información sobre el estado de la sesión del cliente entre solicitudes. Esto hace que las aplicaciones sean más escalables y fáciles de mantener.

* __Interfaz uniforme__: Una API REST sigue un conjunto uniforme de reglas y convenciones para la estructura de las URLs, los métodos HTTP y la representación de datos (generalmente en formatos como JSON o XML). Esto hace que las APIs sean predecibles y fáciles de usar.

* __Cliente-Servidor__: La arquitectura REST separa claramente el cliente (quien realiza solicitudes) del servidor (quien proporciona los recursos). Esto permite que ambos evolucionen independientemente.

* __Capas__: Puede haber varias capas intermedias, como servidores proxy o sistemas de almacenamiento en caché, entre el cliente y el servidor, sin afectar la comunicación entre ellos.

Las APIs REST son ampliamente utilizadas en el desarrollo de aplicaciones web y móviles debido a su simplicidad y escalabilidad. Permiten a los desarrolladores acceder y manipular recursos a través de solicitudes HTTP estándar, lo que facilita la integración de servicios y la construcción de aplicaciones distribuidas.

### Ayudas a la mejora del código

__PHPStan__ es una herramienta de análisis estático para el lenguaje de programación PHP. Su principal objetivo es ayudar a los desarrolladores de PHP a encontrar errores en el código fuente antes de que se ejecuten las aplicaciones. PHPStan se basa en la teoría de tipos estáticos y utiliza técnicas avanzadas de análisis estático para detectar problemas en el código PHP sin necesidad de ejecutarlo.

Algunas de las características y funcionalidades de PHPStan incluyen:

* __Detección de errores estáticos__: PHPStan examina el código PHP en busca de errores y problemas potenciales, como tipos incorrectos de variables, llamadas a métodos inexistentes, uso de variables no declaradas y otras situaciones problemáticas.

* __Análisis de tipos__: PHPStan realiza un análisis de tipos estáticos en el código, lo que significa que puede inferir el tipo de datos de las variables y detectar inconsistencias o incompatibilidades de tipos.

* __Niveles de análisis__: PHPStan ofrece varios niveles de análisis, desde el nivel 0 (más permisivo) hasta el nivel 8 (más estricto). Los niveles más altos imponen restricciones más estrictas en el código, lo que permite una detección de errores más precisa.

* __Extensibilidad__: Puedes extender PHPStan con reglas personalizadas para adaptar la herramienta a las necesidades específicas de tu proyecto o equipo de desarrollo.

* __Integración con herramientas de construcción__: PHPStan se puede integrar con sistemas de construcción y automatización, como Composer y herramientas de CI/CD (Integración Continua/Entrega Continua), lo que facilita su incorporación en el flujo de trabajo de desarrollo.

PHPStan es ampliamente utilizado en la comunidad de desarrollo de PHP para mejorar la calidad del código y prevenir errores antes de que lleguen a la producción. Ayuda a los equipos de desarrollo a escribir código más robusto, legible y mantenible, lo que a su vez reduce la cantidad de errores que se manifiestan en tiempo de ejecución y mejora la eficiencia en el desarrollo de aplicaciones PHP.

__PHP CS Fixer__ (PHP Coding Standards Fixer) es una herramienta de línea de comandos utilizada en el entorno de desarrollo de PHP para ayudar a mantener y aplicar estándares de codificación consistentes en proyectos PHP. Su principal función es formatear y corregir automáticamente el código fuente de acuerdo con las convenciones de codificación establecidas, lo que facilita la colaboración en equipos de desarrollo y asegura que el código cumpla con las mejores prácticas de codificación.

Las características y funcionalidades clave de PHP CS Fixer incluyen:

* __detección y corrección de problemas de estilo de codificación__: La herramienta puede identificar y corregir automáticamente problemas de estilo en el código, como indentación incorrecta, uso inconsistente de espacios o tabulaciones, y otros problemas relacionados con la presentación del código.

* __Adherencia a estándares de codificación__: PHP CS Fixer es altamente configurable y se puede personalizar para seguir una variedad de estándares de codificación, como PSR-1, PSR-2 y PSR-12, que son estándares ampliamente aceptados en la comunidad de desarrollo de PHP.

* __Compatibilidad con proyectos existentes__: Puede utilizarse en proyectos PHP ya existentes para aplicar y mantener consistentes las reglas de codificación en todo el código fuente.

* __Integración con herramientas de construcción y flujo de trabajo__: PHP CS Fixer se puede integrar fácilmente en herramientas de construcción como Composer, así como en flujos de trabajo de CI/CD (Integración Continua/Entrega Continua), lo que permite automatizar la corrección de estilo y la verificación de estándares de codificación en el proceso de desarrollo.

* __Configuración personalizada__: La herramienta permite personalizar las reglas de codificación, lo que significa que puedes adaptarla a las necesidades específicas de tu proyecto o equipo.

En resumen, PHP CS Fixer es una herramienta esencial para el desarrollo de PHP, ya que ayuda a garantizar la coherencia en el código, mejora la legibilidad y facilita el mantenimiento del mismo al mantener estándares de codificación consistentes en todo el proyecto.

Estas dos herramientas las instalaremos con Composer, pero sólo en el entorno de desarrollo:

Más información sobre la herramienta [PHPStan](https://phpstan.org/) y de la herramienta [PHP-CS-Fixer](https://github.com/PHP-CS-Fixer/PHP-CS-Fixer)

```shell
composer require --dev phpstan/phpstan

composer require --dev friendsofphp/php-cs-fixer
```


Una configuración de estas herramientas dentro de un proyecto puede ser:
*  `phpstan.neon`

```php
parameters:
    level: 6
    paths:
        - src
        - routes

```

* `.php-cs-fixer.php`

```php
use PhpCsFixer\{
    Config, Finder,
};

$finder = Finder::create()
    ->in(__DIR__ . '/src')
    ->in(__DIR__ . '/routes')
    ->ignoreDotFiles(true)
    ->exclude('vendor');

return (new Config)->setRules([
    '@PSR12' => true,
    '@PSR12:risky' => true,
    '@PHP82Migration' => true,
    '@PHP80Migration:risky' => true,
    'array_syntax' => ['syntax' => 'short'],
    'binary_operator_spaces' => ['operators' => ['=>' => 'align_single_space_minimal']],
    'blank_line_after_opening_tag' => true,
    'blank_line_before_statement' => ['statements' => ['break', 'continue', 'declare', 'return', 'throw', 'try']],
    'braces' => ['allow_single_line_closure' => true, 'position_after_anonymous_constructs' => 'same', 'position_after_control_structures' => 'same', 'position_after_functions_and_oop_constructs' => 'next'],
    'cast_spaces' => ['space' => 'single'],
    'class_attributes_separation' => ['elements' => ['method' => 'one']],
    'class_definition' => ['single_line' => true],
    'concat_space' => ['spacing' => 'one'],
    'declare_equal_normalize' => ['space' => 'single'],
    'function_typehint_space' => true,
    'include' => true,
    'increment_style' => ['style' => 'post'],
    'linebreak_after_opening_tag' => true,
    'lowercase_cast' => true,
    'lowercase_static_reference' => true,
    'magic_constant_casing' => true,
    'magic_method_casing' => true,
    'method_argument_space' => ['on_multiline' => 'ensure_fully_multiline'],
    'native_function_casing' => true,
    'native_function_type_declaration_casing' => true,
    'new_with_braces' => true,
    'no_alternative_syntax' => true,
    'no_blank_lines_after_class_opening' => true,
    'no_blank_lines_after_phpdoc' => true,
    'no_empty_phpdoc' => true,
    'no_empty_statement' => true,
    'no_extra_blank_lines' => ['tokens' => ['break', 'continue', 'curly_brace_block', 'extra', 'parenthesis_brace_block', 'return', 'square_brace_block', 'switch', 'throw', 'use', 'use_trait']],
    'no_leading_import_slash' => true,
])
    ->setRiskyAllowed(true)
    ->setFinder($finder);
``` 
Para ejecutar las herramientas

```shell
## Ejecuta PHPStan
	./vendor/bin/phpstan analyse -c phpstan.neon

## Aplica PHP CS Fixer a nuestro proyecto
	./vendor/bin/php-cs-fixer fix

``` 

La librería externa [phpdotenv](https://github.com/vlucas/phpdotenv) que nos permite definir un fichero `.env` para tener todas las variables de entorno definidas.


### Definir el proyecto

Crear el directorio del proyecto con los siguientes subdirectorios `public` donde irá nuestra página web de inicio,  `routes` donde figura el archivo que contendrá los puntos de acceso de la aplicación y `src` donde almacenaremos el resto de nuestros programas php.
Instalar composer en nuestro proyecto y que solo instale librerias externas estables

```shell
composer init
```
Modificamos el fichero `composer.json ` para deninir el espacio de nombres por ejemplo, `App` para nuestro directorio `src`
```php
 "autoload": {
        "psr-4": {
            "App\\": "src/"
        }
    },
```
Que composer tenga en cuenta el cambio
```shell
composer dump-autoload
```

Realizar las instalaciones de las librerias de ayuda y crear los ficheros de configuración
```shell
composer require --dev phpstan/phpstan

composer require --dev friendsofphp/php-cs-fixer

composer require vlucas/phpdotenv

```
Comprobar que podemos ejecutar las herramientas
```shell
./vendor/bin/phpstan analyse -c phpstan.neon
./vendor/bin/php-cs-fixer fix

```
La  estructura de carpetas para nuestro proyecto es:
![estructura proyecto](img/estructura.png)

### Instalar, configurar las rutas de la aplicación

En PHP, una "ruta" generalmente se refiere a una dirección URL o un camino dentro de una aplicación web que se utiliza para acceder a un recurso o una página específica. Las rutas son una parte fundamental de muchas aplicaciones web, ya que permiten a los usuarios navegar por diferentes secciones del sitio web y acceder a funcionalidades específicas.

Las rutas en PHP suelen estar asociadas a acciones o controladores que procesan las solicitudes del usuario y generan respuestas correspondientes. Estas rutas pueden ser definidas en el código de la aplicación utilizando un enrutador (router) o un sistema de gestión de rutas. Un enrutador es una parte del marco de trabajo (framework) o se implementa manualmente para mapear las URL a las funciones o métodos del controlador que deben ejecutarse.

Para configurar las rutas de la aplicación utilizamos la librería externa [simple-php-router](https://github.com/skipperbent/simple-php-router) que nos proporciona el enrutador
```shell
composer require pecee/simple-router
```
Ejemplo de configuración

Así que en el directorio de routes definimos el fichero **api.php** por ejemplo, con el siguiente contenido
```php
/*directiva PHP modo estricto de tipos, comprobaciones más rigurosas en cuento a tipo de datos 
que utilizan en las funciones y métodos, lo que ayuda a reducir errores con el tipo de datos
y mejorar la calidad y seguridad del código
*/
declare(strict_types=1);
use Pecee\SimpleRouter\SimpleRouter as Router;
//definimos la primera ruta por get en el raiz que muestra hola mundo
Router::get('/',function(){
    return "Hola mundo";
});
//definimos por ejemplo nuestra primera entrada en la api
Router::get('/api',function(){
    return "Esta es la entrada a nuestra API REST";
});
```

y creamos nuestro fichero de arranque dentro del directorio público creado, anteriormente este archivo se configuraba en la raiz del proyecto ahora se recomienda crear un directorio `public` donde crear el archivo `index.php` por seguridad en los servidores.
Al crear `index.php` tenemos que tener cuidado en los direccionamientos para cargar todas las librerias externas.
Un ejemplo del fichero **index.php**
```php

declare(strict_types=1);
use Pecee\SimpleRouter\SimpleRouter as Router;
//cargamos las librerias y el fichero de rutas
require __DIR__.'/../vendor/autoload.php';
require __DIR__.'/../routes/api.php';
// cargamos el fichero .env con la configuración
$dotenv = Dotenv\Dotenv::createImmutable(__DIR__ . '/../');
$dotenv->load();
// cargamos el router
Router::start();

```

Una vez creado para probar que accedemos a la ruta creada. Arrancamos un terminal con el punto de acceso al directorio `public` definido
```shell
php -S localhost:8001 -t=public
```

En el navegador podemos comprobar:

![página de inicio](img/entrada1.png)

y si acedemos a la api

![página de api](img/entradaapi.png)

### Crear el primer controlador utilizando router
En el contexto de PHP y el patrón de diseño de software Modelo-Vista-Controlador (MVC), un "controller" (controlador) es una parte fundamental de la arquitectura que se encarga de manejar las solicitudes del usuario, procesarlas y controlar la lógica de la aplicación. Su principal objetivo es actuar como un intermediario entre la vista (interfaz de usuario) y el modelo (los datos y la lógica de negocio).

Para introducir los controladores en el ejemplo anterior, realizamos los siguientes pasos:

En el fichero __index.php__ añadimos:
```shell
Router::setDefaultNamespace('\App\Controllers\Api');
```
Con esto estamos diciendo que dentro de `src` vamos a tener otro subdirectorio `Controllers\Api` que tenemos que crear y a partir de aquí creamos los controladores en este caso **ApiController.php** Crear un controlador que este mapeado por una ruta. 

Para configurar este controlador tenemos que utilizar una serie de funciones que nos provee la libreria externa Router y es una recomendación que nosotros vamos a utilizar [Funciones Helper](https://github.com/skipperbent/simple-php-router#helper-functions).Creamos un fichero __helpers.php__ que ubicamos dentro de la carpeta `src`, también tenemos que modificar `composer.json` para que realice la carga de este fichero

Un ejemplo de fichero __helpers.php__
```php
declare(strict_types = 1);

use Pecee\SimpleRouter\SimpleRouter as Router;
use Pecee\Http\Url;
use Pecee\Http\Response;
use Pecee\Http\Request;

/**
 * Get url for a route by using either name/alias, class or method name.
 *
 * The name parameter supports the following values:
 * - Route name
 * - Controller/resource name (with or without method)
 * - Controller class name
 *
 * When searching for controller/resource by name, you can use this syntax "route.name@method".
 * You can also use the same syntax when searching for a specific controller-class "MyController@home".
 * If no arguments is specified, it will return the url for the current loaded route.
 *
 * @param string|null $name
 * @param string|array|null $parameters
 * @param array|null $getParams
 * @return \Pecee\Http\Url
 * @throws \InvalidArgumentException
 */
function url(?string $name = null, $parameters = null, ?array $getParams = null): Url
{
    return Router::getUrl($name, $parameters, $getParams);
}

/**
 * @return \Pecee\Http\Response
 */
//retorna el respuesta del router que nos sirve para mostrar las respuestas en formato Json
function response(): Response
{
    return Router::response();
}

/**
 * @return \Pecee\Http\Request
 */
//nos sirve para recuperar los datos que nos van a enviar desde el exterior
function request(): Request
{
    return Router::request();
}

/**
 * Get input class
 * @param string|null $index Parameter index name
 * @param string|mixed|null $defaultValue Default return value
 * @param array ...$methods Default methods
 * @return \Pecee\Http\Input\InputHandler|array|string|null
 */
// recuperar el input que nos envian desde fuera
function input($index = null, $defaultValue = null, ...$methods)
{
    if ($index !== null) {
        return request()->getInputHandler()->value($index, $defaultValue, ...$methods);
    }

    return request()->getInputHandler();
}

/**
 * @param string $url
 * @param int|null $code
 */
function redirect(string $url, ?int $code = null): void
{
    if ($code !== null) {
        response()->httpCode($code);
    }

    response()->redirect($url);
}

/**
 * Get current csrf-token
 * @return string|null
 */
function csrf_token(): ?string
{
    $baseVerifier = Router::router()->getCsrfVerifier();
    if ($baseVerifier !== null) {
        return $baseVerifier->getTokenProvider()->getToken();
    }

    return null;
}

```
Para que Composer incluya este fichero modificamos `composer.json` en el apartado `autoload` 

```shell

 "files": [
            "src/helpers.php"
        ]

```
El fichero **ApiController.php**

```php
declare(strict_types=1);
namespace App\Controllers\Api;

final class ApiController
{
    public function __invoke():void{

        response()->json(
            ['message'=>'Esta es la entrada a nuestra API REST']
        );

    }
}

```
y ahora nos queda modificar la entrada de __api.php__
```php
// definir un grupo de rutas para que todas responda con prefijo /api
// y que llamen al controlador ApiController al método invoke

Router::group(['prefix'=>'api'],function():void{
        Router::get('/',[ApiController::class,'__invoke']);
    }
);
```
Y ahora probamos en el navegador
![página de api json](img/apicontroller.png)



## POSTMAN 

Postman es una plataforma API para crear y utilizar API. Postman simplifica cada paso del ciclo de vida de la API y agiliza la colaboración para que puedas crear mejores API y más rápido. Información [Postman](https://www.postman.com/)
Si es la primera vez que utilizamos la plataforma tendremos que descargar el servicio `desktop agent`
Ejemplo

Crea tu primera colección en el área de trabajo creado, por ejemplo,  con nombre cursoDWES y crea tu primer punto de acceso EntradaApi, y la petición http, el resultado es:

![ejemplo postman](img/postman.png)

### Congigurar un entorno
las variables de entorno en Postman te permiten gestionar y utilizar de manera eficiente valores dinámicos en tus solicitudes, lo que facilita la automatización y la gestión de tus pruebas de API y peticiones.

En la opción __Environments__, nos dá la opción de crear el entorno y posteriormente crear las variables. Por ejemplo, crearemos una variable `API_URL` que contenga el valor de entrada a todas nuestras api's definidas.

![ejemplo activar entorno en postman](img/postman1.png)

Una vez creado el entorno para poder utilizar las variables definidas es necesario __activar el entorno__
Para utilizar la variable en la petición get se encuentra entre dobles llaves

![ejemplo de utilización de variables de entornos](img/postman2.png)








