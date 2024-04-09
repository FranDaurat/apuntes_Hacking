-- -- 
Comando utilizado para aplicar un barrido en el sistema y detectar binarios con el privilegio SUID.
```bash
find / -type f -perm /4000 -exec ls -l {} \; 2>/dev/null
```

Comando utilizado para aplicar un barrido en el sistema y ver que archivos tienen capacidad de escritura.
```bash
find / -writable 2>/dev/null
```

Comando utilizadopara realizar una buscada en el sistema de capabilities asociadas a binarios.
```bash
getcap -r / 2>/dev/null
```


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