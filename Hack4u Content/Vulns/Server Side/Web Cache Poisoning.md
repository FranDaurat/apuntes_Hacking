-- -
## 1. **Checkear el header `X-Forwarded-Host`**
- Revisar si el valor de ese header se refleja en la respuesta.

## 2. **Checkear el valor de las cookies**
- Revisar si el valor de ese header se refleja en la respuesta.

## 3. **Probar cambiar el protocolo o esquema `X-Forwarded-Scheme`**
- Si lo acepta hay que ver podemos modificar el location con **`X-Forwarded-Host`**.

## 4. **Ver si la respuesta del servidor utilizada el header `Vary`**
- Este header indica a que cliente con que **`User-Agent`** les va a enviar la respuesta cacheada, por lo que se necesita que el **``User-Agent``** coincida con el de la victima.

## **5. Probar si un parametro se refleja en la respuesta** 
- Probar tambien con el header **`Origin`** para refrescar la cache.

## **6. Probar parameter cloaking para inyectar codigo js.** 

```javascript
/js/geolocate.js?callback=setCountryCookie&utm_content=12;callback=alert(1)
```

## **7. Intentar Fat GET request**
- Tambien se me ocurre que la pena probar con POST.
```http
GET /js/geolocate.js?callback=setCountryCookie HTTP/2
Host: 0a4d00c703e03729804603b200c6008e.web-security-academy.net
Cookie: country=[object Object]
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:142.0) Gecko/20100101 Firefox/142.0
Accept: */*
Accept-Language: es-AR,es;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate, br
Referer: https://0a4d00c703e03729804603b200c6008e.web-security-academy.net/
Sec-Fetch-Dest: script
Sec-Fetch-Mode: no-cors
Sec-Fetch-Site: same-origin
X-Pwnfox-Color: orange
Te: trailers

callback=alert(1)
```
