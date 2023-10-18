# LIVEWIRE

Laravel Livewire es un framework full-stack escrito en PHP para Laravel, que permite a los desarrolladores crear aplicaciones web dinámicas e interactivas sin necesidad de escribir código JavaScript.

## Instalar Livewire

Dentro del proyecto en el que queramos instalar livewire ejecutamos el comando:

```shell
composer require livewire/livewire
```

## Crear un layout
Es para crear la plantilla que va a utilizar livewire por defecto

```shell
php artisan livewire:layout
```

![nuevo layout](img/layout.png)

Genera una plantilla para renderizar las páginas web donde lo más interesante es añadir por ejemplo, que vamos a utilizar bootstrap 
```php
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">

        <title>{{ $title ?? config('app.name') }}</title>

        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-rbsA2VBKQhggwzxH7pPCaAqO46MgnOM80zW1RWuH61DGLwZJEdK2Kadq2F9CUG65" crossorigin="anonymous">
        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css" integrity="sha512-z3gLpd7yknf1YoNbCzqRKc4qyor8gaKU1qmn+CShxbuBusANI9QpRohGBreCFkKxLhei6S9CQXFEbbKuqLg0DA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    </head>
    <body>
        {{ $slot }}
    </body>
</html>
```
## Definir los componentes de tipo filtro 

Sintaxis:
```shell
php artisan make:livewire \\ruta\\nombre_componente
```

Ejemplo
![definir un filtro](img/componente.png)




