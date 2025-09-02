-- -
## 1. **Checkear el header `X-Forwarded-Host`**
- Revisar si el valor de ese header se refleja en la respuesta.

## 2. **Checkear el valor de las cookies**
- Revisar si el valor de ese header se refleja en la respuesta.

## 3. **Probar cambiar el protocolo o esquema `X-Forwarded-Scheme`**
- Si lo acepta hay que ver podemos modificar el location con **`X-Forwarded-Host`**.

## 3. **Ver si la respuesta del servidor utilizada el header `Vary`**
- Este header indica a que cliente con que **`User-Agent`** les va a enviar la respuesta cacheada, por lo que se necesita que el **``User-Agent``** coincida con el de la victima.