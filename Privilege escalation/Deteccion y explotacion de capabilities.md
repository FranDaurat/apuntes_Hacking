-- - 
En sistemas Linux, las capabilities son una funcionalidad de seguridad que permite a los usuarios realizar acciones que normalmente requieren privilegios de superusuario (root), sin tener que concederles acceso completo de superusuario. Esto se hace para mejorar la seguridad, ya que un proceso que solo necesita ciertos privilegios puede obtenerlos sin tener que ejecutarse como root.
--  -

```bash 
ps -faux # ---> Vemos los procesos activos en el sistema y sus PIDs correspondiente
cat /proc/$PID/status | grep Cap # ---> Filtra y muestra las líneas relacionadas con las capacidades de un proceso desde el archivo
capsh --decode=000000003000 # ---> Hacemos el decode de los codigos para ver la capabilities en texto claro

# O sino:
getpcaps $PID # ---> Eso ya nos muestra directamente las capabilities asociadas a al $PID
```

### Barrido a nivel de sistema para buscar capabilities
``` bash
getcap -r / 2>/dev/null
```

Luego de encontrar los binarios con las capabilities, nos basamos en la pagina https://gtfobins.github.io para su explotacion.
- --
