## Concepto
Serverless, también conocido como "computación sin servidor", es un modelo de computación en la nube que permite a los desarrolladores ejecutar código sin preocuparse por la gestión directa de servidores. En lugar de provisionar y administrar servidores físicos o virtuales, los desarrolladores pueden enfocarse en escribir código y subirlo a una plataforma de servidor sin servidor.

### Ejemplo utilizando framework Serverless, AWS (Lambda y API gateway) y Node.js
Vamos a realizar un ejemplo utilizando  el framework [Serveless](https://www.serverless.com/), AWS amazon web services (lambda y API gateway) y Node.js

1 Instalar serveless

```shell
npm install -g serverless
```
Una vez instalado podemos ver lo que nos permite hacer

```shell
serverless --help
```
2 Configurar el proveedor de AWS

Para tener las credenciales de AWS. No nos vale la cuenta de academy tenemos que crear una cuenta gratuita por 12 meses.
 
En el perfil del usuario, nos aparece una opción `security credentials` y la opción de crear una `access key` con ambos valores ejecutaremos

```shell
serverless config credentials --provider=aws --key=AKIAYVH4P6GLMRGNFHWM --secret=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
```
Con esto creamos el fichero de credenciales para que serverless se conecte con aws en `~/.aws/credentials`.

3 Crear el ejemplo

Crear un subdirectorio por ejemplo: hola-mundo
Crear un proyecto serverless con la plantilla `aws-nodejs` y el nombre del servicio

```shell
serverless create -t aws-nodejs -n sls-hola-mundo
```
En el subdirectorio nos ha creado
![proyecto](img/proyecto.png)

* El fichero `handler.js` es el que se va utilizar para enviar la lambda (función como un servicio). Esta función tiene un formato de respuesta que hay que cumplir para que funcione bien dentro del `return` que exista el `statusCode` y el `body`. El nombre del handler creado es `hello`

* El fichero `serverless.yml` es el que contiene el servicio y el proveedor del mismo que hemos dado cuando creamos el serverless, además de la función creada donde llama al handler creado, en este caso `handler.hello`

Ahora para que funcione debemos desplegar el proyecto en AWS 

```shell
serverless deploy
```
Si vamos ahora AWS y buscamos Lambda vemos que se ha creado nuestra función lambda

![ver la función en AWS](img/aws1.png)

El siguiente paso será utilizarla:

```shell
serverless invoke -f hello -s dev
```
la respuesta es:

![ver la ejecución de la respuesta](img/respawshello.png)

Hemos obtenido la respuesta por AWS. Ahora vamos a __enviar y procesar datos con una Lambda__

```shell
serverless invoke local -f hello -s dev -d 'Hola Mundo'
```
En la respuesta vemos que ahora en `input` aparece `Hola Mundo`

![ver la ejecución de la respuesta](img/respawshello1.png)

**Utilizar API GATEWAY para obtener un punto de acceso a la función**

Al usuario final no podemos decirle que ejecute una función Lambda, tenemos que darle un punto de acceso final y que al llamarlo se realice la ejecución.
En el fichero **serverless.yml**  hay que indicar el punto de acceso, es importante respetar los espacios
```yml
functions:
  hello:
    handler: handler.hello
    events:
      - httpApi:
          path: /hola-mundo
          method: get
```
Crea automáticamente un punto de acceso utilizando la API GATEWAY de AWS

```shell
serverless deploy

```
La salida es:

![ver la ejecución de la respuesta](img/respawshello2.png)

Donde nos indica `la región` asignada y además de la `url` asignada.

![en amazon ha creado un punto](img/consolaapi.png)


### Ejemplo desarrollando en modo Offline para evitar gastos en AWS

En Github [Serveless plugins](https://github.com/serverless/plugins) y buscar [offline - serverless-offline](https://github.com/dherault/serverless-offline)
Para instalar

```shell
npm init -y 
npm install --save serverless-offline
```
Modificamos el fichero **serveless.yml** y añadimos después de `service`

```yml
plugins:
  - serverless-offline
```
Ejecutamos

```shell
serverless offline
```
Nos abre un puerto por donde probar

![nueva consola de pruebas](img/offline.png)

#### Pasar y recoger parámetros en modo de segmento por la URL

Para pasar parámetros por la url tenemos que realizar los siguientes cambios:
* En el fichero **serveless.yml** añadimos a la url un parámetro

```yml
path: /hola-mundo/{name}
```

* En el fichero **handler.js**  recogemos el parámetro en el mensaje. Fijate en las comillas utilizadas `

```js
 message: `Hola ${event.pathParameters.name}`,
```
Para realizar la prueba primero parar con `ctrl+c` y luego lanzar de nuevo `serveless offline`

#### Peticiones POST y parsear datos con querystring

Para poder pasear los datos requerimos instalar

```shell
npm install --save querystring       

```
y configuramos para que utilice este paquete en el fichero **handler.js** añadiendo después de 'use strict'

```js
const queryString = require('querystring');
```

Un ejemplo de crear peticiones POST

* En el fichero **serveless.yml** añadimos una nueva función con nombre `showUser`

```yml
 showUser:
    handler: handler.showUser
    events:
      - httpApi:
          path: /user
          method: post
```

* En el fichero **handler.js**  creamos la función con nombre `showUser`
  
```js
 module.exports.showUser = async (event) => {
  const body = queryString.parse(event['body']);
  return {
    statusCode: 200,
    body: JSON.stringify(
      {
        message: `Petición POST`,
        input: `Hola ${body.name} ${body.lastname}`,
      },
      null,
      2
    ),
  };
};

```
Para realizar la prueba utilizamos POSTMAN
La salida es:

![con los parámetros](img/salidapost.png)

Cuando nos funciona todo podemos enviar todo lo realizado de nuevo a  AWS volviendo a realizar  el comando `deploy`

```shell
serverless deploy
```
Ahora en AWS tendremos dos funciones con dos API Gateway 

![con los parámetros](img/funciones.png)


Si queremos **eliminar** de  AWS todo lo realizado  simplemente utilizar el comando `remove`

```shell
serverless remove
```
#### Instalar y utilizar Serverless para ahorrar megas en cada subida a AWS

Existe un plugin en github  [serverless-plugin-include-dependencies](https://github.com/dougmoscrop/serverless-plugin-include-dependencies) que nos permite ahorrar megas en cada subida a AWS 

Se instala via npm
```shell
npm install serverless-plugin-include-dependencies --save-dev
```
Ahora en la sección de `plugins` de **serverless.yml** añadimos

```yml
plugins:
  - serverless-plugin-include-dependencies
```

Y también dentro de `package` vamos a excluir el directorio node_modules

```yml
package:
  patterns:
    - '!node_modules/**' # no need to add this, this plugin does it for you
```





