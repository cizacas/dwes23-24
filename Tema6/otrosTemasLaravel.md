# Información de Laravel

## Trabajar con sesiones en laravel

[Documentación sobre sesiones en laravel](https://laravel.com/docs/10.x/session)

En Laravel, no se recomienda acceder directamente a la superglobal `$_SESSION` de PHP para manejar las sesiones. En su lugar, Laravel proporciona una forma más segura y conveniente de trabajar con sesiones.

Para almacenar datos en la sesión, puedes usar el método `session()`:

```php
// Almacenar un solo valor en la sesión
session(['key' => 'value']);

// Almacenar múltiples valores en la sesión
session(['key1' => 'value1', 'key2' => 'value2']);
```
Para recuperar datos de la sesión, también puedes usar el método `session()`:

```php
$value = session('key');
```
Si intentas obtener un valor que no existe en la sesión, null será devuelto. Si quieres un valor predeterminado diferente, puedes pasarlo como el segundo argumento:

```php
$value = session('key', 'default');
```
Para verificar si un elemento está presente en la sesión, puedes usar el método `has`:

```php
if (session()->has('key')) {
    //
}
```
Para eliminar un elemento de la sesión, puedes usar el método `forget`:

```php
session()->forget('key');
```
Y para eliminar todos los datos de la sesión, puedes usar el método `flush`:

```php
session()->flush();
```
## Mostrar los mensajes y errores

En Laravel, puedes usar la **clase Session** para almacenar mensajes de error o de éxito que se mostrarán en la vista. Aquí te muestro cómo puedes hacerlo:

Primero, en tu controlador, puedes almacenar un mensaje en la sesión de esta manera:

```php
session()->flash('message', 'Tu mensaje aquí');
session()->flash('error', 'Tu mensaje de error aquí');
```
Luego, en tu archivo de vista Blade, puedes mostrar estos mensajes de la siguiente manera:

```php
@if (session('message'))
    <div class="alert alert-success">
        {{ session('message') }}
    </div>
@endif

@if (session('error'))
    <div class="alert alert-danger">
        {{ session('error') }}
    </div>
@endif

```
Esto mostrará un mensaje de éxito si existe en la sesión, y un mensaje de error si existe en la sesión. Los mensajes se mostrarán solo una vez y luego se eliminarán de la sesión.

En Laravel, cuando validas un formulario, los errores se almacenan automáticamente en una variable de sesión llamada $errors. Puedes acceder a estos errores en tu vista Blade de la siguiente manera:

```php
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif

```
Este código verificará si hay algún error en la sesión. Si los hay, iterará sobre cada error y los mostrará en una lista.

Para validar los datos del formulario en tu controlador, puedes usar el método `validate` de la siguiente manera:

```php
public function store(Request $request)
{
    $validatedData = $request->validate([
        'titulo' => 'required|max:255',
        'descripcion' => 'required',
    ]);

    // El resto de tu código...
}
```
En este ejemplo, `titulo` y `descripcion` son **los nombres de los campos** del formulario. Si la validación falla, el usuario será redirigido automáticamente a su ubicación anterior y todos los errores de validación estarán disponibles en la variable $errors.

En Laravel, puedes usar la directiva **@error** en tus vistas Blade para mostrar los errores de validación en la línea donde recoges el dato del formulario. Aquí te muestro cómo puedes hacerlo:

Supongamos que tienes un campo de formulario para el título:

```php
<label for="title">Título</label>
<input id="title" type="text" name="titulo" value="{{ old('titulo') }}">

@error('titulo')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```
En este ejemplo, si hay un error de validación con el campo `titulo`, se mostrará un mensaje de error justo debajo del campo de entrada. La función **old('titulo')** se utiliza para rellenar el campo de entrada con el valor que el usuario había introducido previamente, para que no tenga que volver a escribir todo si ocurre un error de validación.

La variable $message dentro del bloque @error contiene el mensaje de error de validación para ese campo.

Para mostrar un mensaje de éxito después de enviar un formulario en Laravel, puedes usar la funcionalidad de "flash data" de la sesión. Los datos flash son datos temporales que solo están disponibles durante la próxima solicitud HTTP.

Aquí te muestro cómo puedes hacerlo:

En tu controlador, después de procesar el formulario, puedes almacenar un mensaje en la sesión utilizando el método `with()`:
```php
public function store(Request $request)
{
    // Procesar el formulario...

    return redirect()->route('ruta.deseada')->with('success', 'Formulario enviado correctamente!');
}
```

En tu vista, puedes verificar si existe el mensaje en la sesión y mostrarlo:
```php
@if (session('success'))
    <div class="alert alert-success">
        {{ session('success') }}
    </div>
@endif

<!-- Resto de tu HTML... -->
```
En este código, si existe un mensaje de éxito en la sesión, se mostrará en un div con la clase alert alert-success. Puedes personalizar este código para que se ajuste al diseño de tu aplicación.

Recuerda que los datos flash solo están disponibles durante la próxima solicitud HTTP, por lo que después de que se muestre el mensaje, no estará disponible en futuras solicitudes a menos que lo vuelvas a establecer.