-- - 
#### **¿Qué son los JWT?**  
Los JWT están compuestos por **3 partes**, separadas por puntos:  
1. **Header** → Algoritmo & Tipo de Token  
2. **Payload** → Datos (información del usuario u otros claims)  
3. **Signature** → Verificación de la firma  

#### **Ejemplo de JWT:**  
```
1(eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9).2(eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ).3(SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c)
```

#### **Nota:**  
Los JWT están codificados en **Base64**, lo que permite **decodificarlos fácilmente** usando la web oficial `jwt.io` o directamente desde la terminal.

---

## **Posibles vulnerabilidades en el uso de JWT**

### **1. Bypass de autenticación JWT sin verificar la firma
- Tras iniciar sesión con un usuario normal, interceptamos el JWT y cambiamos el valor del campo sub a administrator, accediendo así al panel de administración sin autenticación real. 

### **2. Algoritmo "None" y Sin Firma**  
Algunas aplicaciones permiten el uso de `alg: "NONE"`, lo que permite **omitir la firma**.  

#### **Generación del Header:**
```bash
echo -n '{"alg":"none","typ":"JWT"}' | base64
# Resultado: eyJhbGciOiJOT05FIiwidHlwIjoiSldUIn0=
```

#### **Generación del Payload:**
```bash
echo -n '{"id":2,"iat":1724327756,"exp":1724331356}' | base64
# Resultado: eyJpZCI6MiwiaWF0IjoxNzI0MzI3NzU2LCJleHAiOjE3MjQzMzEzNTZ9
```

#### **Construcción del JWT:**
```
eyJhbGciOiJOT05FIiwidHlwIjoiSldUIn0.eyJpZCI6MiwiaWF0IjoxNzI0MzI3NzU2LCJleHAiOjE3MjQzMzEzNTZ9.
```

### **3. Bypass de autenticación JWT con clave débil**  
- Es una mala práctica común en la implementación de autenticación basada en JWT: el uso de claves de firma extremadamente débiles. Iniciamos sesión con un usuario estándar, interceptamos el token y lo sometemos a un proceso de fuerza bruta utilizando herramientas como Hashcat para descubrir la clave secreta, que resulta ser una palabra sencilla incluida en diccionarios comunes.

```bash
hashcat -a 0 jwt secrets.txt 
```

- Una vez crackeado y descubierto el secreto generamos una clave simetrica en el jwt editor (siempre y cuando se utilizen algoritmo simetricos como el **`HS256`**).
![[Pasted image 20251008183257.png]]
- En la clave **`k`** ponemos el valor de la clave descubierto en encodeado en base64.
- Luego interceptamos la peticion, cambios el valor del payload, ponemos para no cambiar el header y le damos a **`sign`** para asi firmar el token con la clave descubierta.

### **4. Bypass de autenticación JWT por inyección en jwk**
- Es una vulnerabilidad en la verificación de JWTs relacionada con el uso del parámetro jwk en el encabezado del token. El servidor permite que el propio JWT contenga la clave pública con la que será validado, pero no comprueba si dicha clave proviene de una fuente confiable.

- Aprovechamos esta debilidad generando un par de claves RSA y firmando un token falso que suplantará al usuario administrador. Al incluir nuestra clave pública dentro del jwk del encabezado, el servidor valida la firma sin cuestionar su origen

1. Crear una **``RSA key``** (Al parecer no importa el tamaño). 
2. Apretar attack y usar la opcion de **``Embedded JWK``** y elegir la clave (la anteriormente creada).
3. Cambiar el valor que deseamos suplantar.
4. Firmar el nuevo token (Dont modify header).

### **5. Bypass de autenticación JWT por inyección en jku**
- Se explota una vulnerabilidad relacionada con el parámetro jku del encabezado de un JWT. Este parámetro permite al servidor obtener dinámicamente la clave pública desde una URL externa para verificar la firma del token. El fallo consiste en que el servidor no valida si esa URL pertenece a un dominio confiable.

1. Agregamos el valor **`jku`** en el header:
```json
{  
    "kid": "236b0959-9791-4472-8469-2a181a377727",  
    "alg": "RS256",  
    "jku": "https://malicious.com/exploit"  
}
```
2. Reemplazamos el **`kid`** por el generado con la nueva clave asimetrica.
3. Copiamos la clave publica y la exponemos en nuestro servidor.
```json
{
    "keys": [
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "236b0959-9791-4472-8469-2a181a377727",
            "n": "q92UvAA4GBr2e2-5V9CH_z_sFy9eHExWupD06YZEHQe7X2KNZyuv1ocnupZUKBa-uB4ZZRVm2ONDJHgPuYSnChNh7wraE333MjbLzRXAyk1S3aSmPzXQmLEExIEpgL3XHSfyEo65cyCcEg1p5MhlfDJnY0Jts0Vqfwtg3i0jSzxtv5XxBldeLmxuEzHf0P_npXgjePLAgVJXRzl1nGc1L_LA8eK_lVEpcuxKemxfggjdgeCUe6mt8-uctq0ewlAhbymB-K_2vxSJTHAjlApc2Od9tbiULNyax5KhI-WR_MhqJEkWOKMGps1T1qPcRvSWU0rO4O_8oLkJeVKs70ApdQ"
        }
    ]
}
```

4. Modificamos tambien el payload con los valores que queremos tomar
```json
{  
    "iss": "portswigger",  
    "exp": 1760038576,  
    "sub": "administrator"  
}
```

5. Y por ultimo firmamos el **`JWT`** con la clave que hemos creado.

### **6. Bypass de autenticación JWT con traversal en kid**
- El servidor utiliza el valor del parámetro kid para leer desde el sistema de archivos la clave con la que validar la firma del token. Aprovechamos esto introduciendo una secuencia de path traversal en kid que apunta al archivo /dev/null, el cual tiene contenido predecible.

- Firmamos nuestro JWT con una clave basada en un byte nulo y modificamos el payload para suplantar al usuario administrador. El servidor usa /dev/null como clave de verificación sin realizar validaciones adecuadas, por lo que acepta el token

1. Generamos una firma la cual como clave le ponemos **`AA==`** lo cual es la representacion del byte nul **`\0`** en base 64.
![[Pasted image 20251009162108.png]]
2. Hacemos uso del path traversal para asi "invocar" el dev/null con el fin de que devuelva vacio y se asemeje a la firma de la clave que creamos.
![[Pasted image 20251009162046.png]]
3. Cambiamos los valores que queremos sustituir.
4. Firmamos.

### **7. Bypass de autenticación JWT por confusión de algoritmo (con clave expuesta)**

 - El servidor posee una implementación insegura de JWT que utiliza claves RSA para firmar y verificar tokens. Sin embargo, el servidor es vulnerable a un ataque de confusión de algoritmos: permite cambiar el algoritmo de firma a HS256, el cual utiliza una clave simétrica.

- Aprovechamos que el servidor expone su clave pública en el endpoint ‘/jwks.json‘, y la usamos como si fuera la clave secreta para firmar un nuevo token con HS256. Luego modificamos el payload para suplantar al usuario administrador y firmamos el JWT con la clave pública, que el servidor acepta erróneamente.

1. Descargamos la clave publica expuesta.
```json 
curl -s https://0a0800d2038367f383243eb700350098.web-security-academy.net/jwks.json | jq
{
  "keys": [
    {
      "kty": "RSA",
      "e": "AQAB",
      "use": "sig",
      "kid": "82a5a2fe-0604-4c1e-bf5e-f610974bbdde",
      "alg": "RS256",
      "n": "u573ya8XAMDH1Yk3y09LfLWgUawp7iWmuQo5pR5739FNqfdlDfwbe1N-ikAdTdgdeANzGBklRIFgxlPjQ1i74wIeQAiBndWSwJIMKRxjZpJt1YyiOwg5nVx0BBsDQLolmXHD851G-vBAq0yutxvqgGWVu-2pRZO8H3MIaClfufJefW8fEjnWNgEzebYWoiDfVtY4YRa0vveI4LTqWGuT6Kokp21K31uZT-OearuCxQAzdympvo2xz_n_3yJIrvYwZfGD0_aoxP-mskCgHB0GMv_x2KLa5mKR0gJAdsOgTjbVBNEYC7exYx2zlppECh6YSg913oUS-YThI8f2ffjgUQ"
    }
  ]
}
```

2. Creamos una clave RSA con la clave publica que encontramos.
![[Pasted image 20251013173402.png]]

3. Copiamos la clave como PEM.
![[Pasted image 20251013173528.png]]

4. La codificamos en base64
![[Pasted image 20251013173837.png]]

5. Creamos una nueva clave simetrica y ponemos como valor la clave PEM codificada en base 64 en el valor para **`k`**: 
![[Pasted image 20251013173825.png]]

6.Modificamos el tipo de algoritmo (de RS256 a HS256) junto con el valor que deseamos suplantar.
![[Pasted image 20251013173548.png]]

7. Firmamos con la clave simetrica privada que creamos.
### **8. Bypass de autenticación JWT por confusión de algoritmo (sin clave expuesta)**
- Se produce una confusión de algoritmos en JWT sin que el servidor exponga públicamente su clave. A pesar de ello, logramos deducir la clave pública RSA que utiliza para validar los tokens, a partir de dos JWT generados legítimamente tras iniciar sesión.

- Usamos la herramienta sig2n, que permite reconstruir valores matemáticamente posibles de la clave pública analizando las firmas. Tras identificar la clave válida, la usamos como si fuera una clave secreta con el algoritmo HS256, firmando un token falso 

1. Obtenemos 2 tokens distintos (pueden ser generados para el mismo usuario)
2. Ejectuamos el comando:
```bash 
docker run --rm -it portswigger/sig2n <token1> <token2>
```
3. Probamos si el JWT generado es valido. 
4. Si lo es generamos una nueva clave simetrica con el valor de clave generado.
![[Pasted image 20251014140552.png]]
![[Pasted image 20251013173825.png]]
5. 