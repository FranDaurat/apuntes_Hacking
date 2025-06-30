---

### 🔍 **GraphQL Recon Cheat Sheet**

---

#### **1. Basic Recon**

- **Obtener el nombre del tipo de objeto en la raíz de la API:**
  ```json
  {
      "query": "{__typename}"
  }
  ```

---

#### **2. Comprobación de introspección habilitada**

- **Testear introspección habilitada:**
  ```json
  {
      "query": "{__schema{queryType{name}}}"
  }
  ```

---

#### **3. Introspección completa**

- **Ejecutar una consulta de introspección completa:**
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

---

#### **4. Bypass de introspección**

- **Usar una consulta alternativa para evadir restricciones:**
  ```json
  { 
      "query": "query{__schema {queryType{name}}}" 
  }
  ```

- **Enviar la consulta de introspección como una solicitud GET:**
```http
  # Introspection probe as GET request
  /graphql?query=query%7B__schema%0A%7BqueryType%7Bname%7D%7D%7D
```

- **Usar salto de línea para evadir filtros:**
  ```json
  query IntrospectionQuery {
      __schema # salto de línea intencional
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

---

**Consejo:** Si el servicio tiene medidas de protección robustas, considera usar técnicas de evasión como encabezados falsos o intervalos de tiempo para evitar la detección.

---