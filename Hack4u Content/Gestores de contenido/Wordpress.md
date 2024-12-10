--  - -  

Escanero normal:
``` bash
wpscan --url http://localhost:31337 
``` 

Comando utilizado para enumerar posibles usuarios:
``` bash
wpscan --url http://localhost:31337 --enumerate u
``` 

Escaneo de todos los plugins vulnerables:
``` bash
wpscan --url http://localhost:31337 -e vp --api-token=U6B2wfRc3asoLdIsHHZekg1ZxU02P5QYaCHm5UCJosw
``` 

Ataque por diccionario:
``` bash
wpscan --url http://localhost:31337 -U fran -P /usr/share/wordlists/rockyou.txt
``` 

Wordpress agrressive scanning from lostsec
```bash
wpscan --url https://site.com --disable-tls-checks --api-token <here> -e at -e ap -e u --enumerate ap --plugins-detection aggressive --force
```