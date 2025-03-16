-- -
El ataque **Prototype Pollution** es una técnica de ataque que aprovecha las vulnerabilidades en la implementación de objetos en JavaScript. Esta técnica de ataque se utiliza para modificar la propiedad “**prototype**” de un objeto en una aplicación web, lo que puede permitir al atacante ejecutar código malicioso o manipular los datos de la aplicación.

En JavaScript, la propiedad “prototype” se utiliza para definir las propiedades y métodos de un objeto. Los atacantes pueden explotar esta característica de JavaScript para modificar las propiedades y métodos de un objeto y tomar el control de la aplicación.

El ataque de Prototype Pollution se produce cuando un atacante modifica la propiedad “prototype” de un objeto en una aplicación web. Esto se puede lograr mediante la manipulación de datos que se envían a través de formularios o solicitudes AJAX, o mediante la inserción de código malicioso en el código JavaScript de la aplicación.

Una vez que el objeto ha sido manipulado, el atacante puede ejecutar código malicioso en la aplicación, manipular los datos de la aplicación o tomar el control de la sesión de un usuario. Por ejemplo, un atacante podría modificar la propiedad “prototype” de un objeto de autenticación de usuario para permitir el acceso a una cuenta sin la necesidad de una contraseña.

El impacto de la explotación del ataque Prototype Pollution puede ser significativo, ya que los atacantes pueden tomar el control de la aplicación o comprometer los datos de los usuarios. Además, dado que el ataque se basa en vulnerabilidades en la implementación de objetos en JavaScript, puede ser difícil de detectar y corregir.
-- -
## Ejemplo de como explotarlo:

Es importante aclarar que a veces la data se pasa por post con parametros asi nomas.
```http
Content-Type: application/x-www-form-urlencoded
email=test@test.com&msg=carlos
```

En este caso es necesario cambiar el content type y la data tramitada por post como:
```http
Content-Type: application/json
{
"email":"test@test.com",
"msg":"probando",
}
```

Luego de esto habria que verificar si el servidor recibe la data de igual manera. Para corroborarlo podemos chequear que nos redireccione al endpoint de login correctamente o sino podemos probar generando algun error de sintaxis (borrar una comilla, llave, etc) con el fin de ver si se rompe la consulta o no.
Y para terminar, una vez sabemos que interpreta los datos en formato json, podemos probar de agregarle el atributo ```__proto__```:
```http
Content-Type: application/json
{
    "email": "test@test.com",
    "msg": "probando",
    "__proto__": {
        "admin": true
    }
}
```

El campo de admin por lo general se descubre por intuicion, fuerza bruta y/o filtraciones del codigo fuente.