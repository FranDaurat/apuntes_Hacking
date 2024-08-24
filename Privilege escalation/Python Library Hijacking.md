-- - 
Cuando hablamos de *Python Library Hijacking*, a lo que nos referimos es a una técnica de ataque que aprovecha la forma en la que Python busca y carga bibliotecas para inyectar código malicioso en un script. El ataque se produce cuando un atacante crea o modifica una biblioteca en una ruta accesible por el script de Python, de tal manera que cuando el script la importa, se carga la versión maliciosa en lugar de la legítima.
-- -
## Explotacion

Es basicamente igual que PATH Hijacking solo que no exportamos el path ya que cuando alguien carga una libreria a un script de python, el mismo python comienza a buscar la misma libreria desde el propio directorio de trabajo por lo que podemos crear una archivo con el nombre de la libreria en el directorio de trabajo en el cual reside el script para asi volcarle el sugiente contenido:
```python
import os

os.system("bash -p")
```

### Aclaracion
- Si no tenemos permisos de escirtura en ese mismo directorio podemos ver si en los otros directorios llegamos a tener para luego modificar el archivo vinculado a la libreria y asi elevar privilegios.
