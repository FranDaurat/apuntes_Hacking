-- -

## **Subdomains check**
1. Entrar al subdominio. 
2. Hacer Ctrl + u para ver el codigo fuente.
3. Usar search_endpoints.sh para ver que onda los endpoints asociados a ese subdomino
4. Abrir los endpoints con la extension de firefox.
5. Mirar el burpsuite una vez se abrieron todos los endpoints.
6. Buscar por la palabras claves (JS):
   ```\b(main|app|runtime|bundle|polyfills|auth|config|settings|local|dev|data|api|session|user|core|client|server|utils|base|bearer|Bearer|token|Token)\b ```
