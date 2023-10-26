## Concepto
Serverless, también conocido como "computación sin servidor", es un modelo de computación en la nube que permite a los desarrolladores ejecutar código sin preocuparse por la gestión directa de servidores. En lugar de provisionar y administrar servidores físicos o virtuales, los desarrolladores pueden enfocarse en escribir código y subirlo a una plataforma de servidor sin servidor.

### Ejemplo
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

Para tener las credenciales de AWS, tendremos que abrir un laboratorio y tenemos las credenciales en el detalle de aws

```shell
serverless config credentials --provider=aws --key=ASIA5ITMF3QPJX2ZLA33 --secret=lxG7ozUXBn6xN8N0ElLOwjwSo3O0t5FdyxZ79t24
```
Con esto creamos el fichero de credenciales para que serverless se conecte con aws en `~/.aws/credentials` . Modificar el fichero creado añadiendo también el token.

3 Crear el ejemplo

Crear un subdirectorio por ejemplo: hola-mundo
Crear un proyecto serverless con la plantilla `aws-nodejs` y el nombre del servicio

```shell
serverless create -t aws-nodejs -n sls-hola-mundo
```
En el subdirectorio nos ha creado
![proyecto](img/proyecto.png)

Ahora  desplegamos el proyecto en AWS 

```shell
serverless deploy


