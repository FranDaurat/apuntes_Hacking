-- -

Server-Side Request Forgery (SSRF) es una vulnerabilidad de seguridad en la que un atacante puede hacer que un servidor haga solicitudes no autorizadas a dominios arbitrarios. Las siguientes técnicas son métodos que los atacantes pueden usar para engañar al servidor y hacer que envíe solicitudes a destinos inesperados.
## Técnicas para Circunventar Filtros SSRF

Algunas aplicaciones bloquean entradas que contienen nombres de host como `127.0.0.1` y `localhost`, o URLs sensibles como `/admin`. En estas situaciones, puedes a menudo eludir el filtro usando las siguientes técnicas:

### 1. Usar una Representación Alternativa de IP

Puedes usar diferentes representaciones numéricas para la IP `127.0.0.1`:

- **2130706433:** Representación decimal.
- **017700000001:** Representación octal.
- **127.1:** Forma abreviada.

### 2. Registrar tu Propio Nombre de Dominio

Puedes registrar tu propio nombre de dominio que resuelva a `127.0.0.1`. Por ejemplo, puedes usar `spoofed.burpcollaborator.net` para este propósito.

### 3. Ofuscar Cadenas Bloqueadas

Ofuscar las cadenas bloqueadas usando codificación URL o variación de mayúsculas y minúsculas puede ayudar a eludir los filtros.

- **URL Encoding:** Convertir caracteres especiales en su representación de porcentaje. Por ejemplo, `/admin` se puede codificar como `%2Fadmin`.
- **Case Variation:** Usar diferentes combinaciones de mayúsculas y minúsculas. Por ejemplo, `Admin` en lugar de `admin`.

### 4. Usar Redirecciones

Proporciona una URL que controlas, la cual redirige a la URL objetivo. Intenta usar diferentes códigos de redirección, así como diferentes protocolos para la URL objetivo. Por ejemplo, cambiar de una URL `http:` a `https:` durante la redirección ha demostrado ser efectivo para evadir algunos filtros anti-SSRF.

**Ejemplo:**

1. **Configura una URL de Redirección Controlada:**
   - **Entrada del Usuario:** `http://malicious-site.com/redirect?url=http://target-site.com/admin`
   
2. **Redirección Controlada:**
   - `http://malicious-site.com/redirect` redirige a `http://target-site.com/admin` utilizando un código de redirección.

3. **Cambio de Protocolo:**
   - Cambia de `http:` a `https:` durante la redirección para evadir el filtro.
   - **Entrada del Usuario:** `http://malicious-site.com/redirect?url=https://target-site.com/admin`

-- - 

### 1. Embedding Credentials in a URL

Los atacantes pueden incrustar credenciales dentro de una URL antes del nombre del host usando el carácter `@`. Esto puede usarse para intentar engañar al servidor sobre el destino real de la solicitud.

**Ejemplo:**
https://expected-host:fakepassword@evil-host

**Desglose:**
- `https://` - El esquema de la URL (HTTP/HTTPS).
- `expected-host:fakepassword@` - Parece ser el nombre de usuario `expected-host` y la contraseña `fakepassword` para la autenticación.
- `evil-host` - El verdadero destino de la solicitud.

**Resultado:**
El servidor intentará hacer una solicitud a `https://evil-host`, ignorando las credenciales proporcionadas en la URL.

### 2. Using the `#` Character for URL Fragment

El carácter `#` en una URL indica el comienzo de un fragmento, que no es enviado al servidor. Los atacantes pueden usarlo para ocultar la parte significativa de la URL al servidor.

**Ejemplo:**
https://evil-host#expected-host

**Desglose:**
- `https://evil-host` - El verdadero destino de la solicitud.
- `#expected-host` - Un fragmento que el servidor no procesa ni envía.

**Resultado:**
El servidor hará una solicitud a `https://evil-host` y el fragmento `#expected-host` será ignorado.

### 3. Leveraging DNS Naming Hierarchy

Los nombres de dominio se resuelven de derecha a izquierda, lo que permite que un atacante pueda incluir el host esperado como un subdominio de un dominio que controlan.

**Ejemplo:**
https://expected-host.evil-host


**Desglose:**
- `https://` - El esquema de la URL.
- `expected-host.evil-host` - Un subdominio bajo el control de `evil-host`.

**Resultado:**
El servidor hará una solicitud a `https://expected-host.evil-host`, controlado por el atacante.

### Ejemplos Prácticos

Vamos a ver cómo podrían usarse estos métodos en un escenario de SSRF:

1. **Embedding Credentials:**
   - **Input controlado por el usuario:** `https://example.com?url=https://internal-service:fakepassword@malicious.com`
   - **Resultado esperado:** El servidor intenta acceder a `https://internal-service`, pero en realidad va a `https://malicious.com`.

2. **Using URL Fragment:**
   - **Input controlado por el usuario:** `https://example.com?url=https://malicious.com#internal-service`
   - **Resultado esperado:** El servidor ignora `#internal-service` y accede a `https://malicious.com`.

3. **Leveraging DNS Hierarchy:**
   - **Input controlado por el usuario:** `https://example.com?url=https://internal-service.malicious.com`
   - **Resultado esperado:** El servidor accede a `https://internal-service.malicious.com`.

### Cómo Funciona en el Contexto de SSRF

- **SSRF Básico:** Si una aplicación permite que el usuario controle la URL a la que se hace una solicitud, un atacante podría proporcionar una URL maliciosa.
- **Evasión de Filtros:** Las técnicas mencionadas pueden ser usadas para evadir filtros o validaciones que intentan restringir las URLs a destinos específicos (por ejemplo, validaciones que sólo permiten solicitudes a `internal-service`).

-- -
## Blind SSRF

### ¿Qué son las vulnerabilidades de Blind SSRF?

Las vulnerabilidades de Blind SSRF ocurren si puedes hacer que una aplicación emita una solicitud HTTP de back-end a una URL proporcionada, pero la respuesta de la solicitud de back-end no se devuelve en la respuesta del front-end de la aplicación.

El Blind SSRF es más difícil de explotar, pero a veces puede llevar a la ejecución remota de código completa en el servidor u otros componentes de back-end.

### ¿Cuál es el impacto de las vulnerabilidades de Blind SSRF?

El impacto de las vulnerabilidades de Blind SSRF suele ser menor que el de las vulnerabilidades de SSRF completamente informadas debido a su naturaleza unidireccional. No se pueden explotar fácilmente para recuperar datos sensibles de los sistemas de back-end, aunque en algunas situaciones se pueden explotar para lograr la ejecución remota de código completa.

### Cómo encontrar y explotar vulnerabilidades de Blind SSRF

Identificar simplemente una vulnerabilidad de Blind SSRF que puede desencadenar solicitudes HTTP fuera de banda no proporciona en sí misma una ruta hacia la explotabilidad. Dado que no puedes ver la respuesta de la solicitud de back-end, el comportamiento no se puede usar para explorar contenido en sistemas a los que el servidor de aplicaciones puede acceder. Sin embargo, todavía se puede aprovechar para sondear otras vulnerabilidades en el propio servidor o en otros sistemas de back-end.

#### Pasos para Explorar Blind SSRF:

1. **Sondeo de Direcciones IP Internas:**
   - Puedes explorar ciegamente el espacio de direcciones IP internas, enviando cargas útiles diseñadas para detectar vulnerabilidades conocidas. 
   - Si esas cargas útiles también emplean técnicas fuera de banda, podrías descubrir una vulnerabilidad crítica en un servidor interno no parcheado.

2. **Conexión a Sistemas Controlados por el Atacante:**
   - Otro camino para explotar vulnerabilidades de Blind SSRF es inducir a la aplicación a conectarse a un sistema bajo el control del atacante y devolver respuestas maliciosas al cliente HTTP que hace la conexión.
   - Si puedes explotar una vulnerabilidad grave del lado del cliente en la implementación HTTP del servidor, podrías lograr la ejecución remota de código dentro de la infraestructura de la aplicación.

-- -  
## Encontrar Superficie de Ataque Oculta para Vulnerabilidades de SSRF

### Descripción

Muchas vulnerabilidades de Server-Side Request Forgery (SSRF) son fáciles de encontrar, ya que el tráfico normal de la aplicación involucra parámetros de solicitud que contienen URLs completas. Sin embargo, otros ejemplos de SSRF son más difíciles de localizar.

### Superficies de Ataque Evidentes

En algunos casos, las aplicaciones tienen parámetros de solicitud que explícitamente incluyen URLs completas. Por ejemplo:

- Parámeteros como `callback_url`, `target_url`, `redirect_url`, etc.
- Formularios o endpoints que permiten a los usuarios proporcionar URLs para obtener contenido externo.

Estos son puntos claros donde podrías inyectar URLs maliciosas para explotar SSRF.

### Superficies de Ataque Ocultas

Existen otros escenarios donde las vulnerabilidades de SSRF no son tan evidentes y requieren una búsqueda más exhaustiva. Estas superficies de ataque ocultas pueden incluir:

- **Cabeceras HTTP:**
  - Algunos servidores pueden procesar cabeceras HTTP como `Referer`, `X-Forwarded-For` o `Host` que podrían ser manipuladas para desencadenar solicitudes SSRF.
- **Cargas Útiles Incrustadas en el Cuerpo de la Solicitud:**
  - Datos en el cuerpo de las solicitudes POST que pueden contener URLs, especialmente en formatos como JSON o XML.
- **Parámetros No Documentados:**
  - Parámetros que no están bien documentados o visibles en el tráfico normal de la aplicación pero que aún aceptan URLs.
- **Interacciones de Servicios Internos:**
  - Servicios internos de la aplicación que toman URLs como entradas para funcionalidades como carga de datos, integraciones de APIs, o notificaciones web.

### Estrategias para Encontrar Blind SSRF

Para descubrir estas superficies de ataque más difíciles de encontrar, puedes usar las siguientes estrategias:

1. **Revisar la Documentación y el Código:**
   - Inspeccionar la documentación y, si es posible, el código fuente para descubrir parámetros y funcionalidades que acepten URLs.

2. **Interceptar y Modificar Tráfico:**
   - Utilizar herramientas como Burp Suite para interceptar y modificar el tráfico de la aplicación, probando diferentes parámetros y cabeceras para ver cómo responde el servidor.

3. **Explorar Funcionalidades Secundarias:**
   - Investigar funcionalidades secundarias de la aplicación que podrían no ser tan obvias pero que podrían procesar URLs, como funciones de importación/exportación de datos, integraciones con otros servicios, o mecanismos de redirección.

4. **Pruebas de Fuzzing:**
   - Realizar fuzzing en diferentes partes de las solicitudes para identificar parámetros que podrían procesar URLs.
-- - 
### Todo tipo de cabeceras
```http
GET /some/resource HTTP/1.1
Host: vulnerable-website.com
X-Forwarded-For: 127.0.0.1
X-Forwarded-Host: attacker.com
X-Forwarded-Server: attacker.com
X-Forwarded-Port: 80
Forwarded: for=127.0.0.1;host=attacker.com;proto=http
Client-IP: 127.0.0.1
True-Client-IP: 127.0.0.1
Cluster-Client-IP: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Real-IP: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-ProxyUser-Ip: 127.0.0.1
Referer: http://attacker.com
Origin: http://attacker.com
X-Forwarded-By: 127.0.0.1
X-Forwarded-Proto: https
Via: 1.1 attacker.com
X-Api-Version: 1.0
Max-Forwards: 10
X-Wap-Profile: http://attacker.com
Proxy-Authorization: Basic Zm9vOmJhcg==
Surrogate-Capability: attacker="Surrogate/1.0"
```



