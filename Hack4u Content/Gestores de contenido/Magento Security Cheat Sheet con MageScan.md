-- -
#### 🚀 **¿Por qué usar MageScan en Magento?**  
Magento es un CMS popular para comercio electrónico, pero puede ser un objetivo atractivo para los atacantes debido a su complejidad y la gran cantidad de extensiones disponibles.  
**MageScan** es una herramienta especialmente diseñada para auditar sitios web basados en **Magento**, permitiendo:  
- 🔍 **Detectar la versión de Magento**  
- 📝 **Identificar extensiones instaladas**  
- 💥 **Detectar vulnerabilidades conocidas**  
- 🔑 **Verificar configuraciones de seguridad**  

---

### 💡 **Instalación de MageScan**
MageScan se distribuye como un archivo `.phar` (PHP Archive), por lo que necesitas tener PHP instalado.  

#### 🔧 **Descarga y Configuración**
```bash
wget https://github.com/steverobbins/magescan/releases/download/2.5.0/magescan.phar
chmod +x magescan.phar
```
Para comprobar si funciona:
```bash
php magescan.phar scan:all http://localhost:8080
```

---

### 🔎 **Comandos Específicos para Magento**

#### 🌐 **Escaneo Completo**
Este comando realiza un escaneo exhaustivo del sitio web, incluyendo información sobre la versión, extensiones y vulnerabilidades conocidas:
```bash
php magescan.phar scan:all http://localhost:8080
```
- **scan:all:** Realiza un escaneo completo del sitio web.  

---

### 💻 **Detección de la Versión de Magento**
Identificar la versión es crucial para evaluar las vulnerabilidades específicas:
```bash
php magescan.phar scan:version http://localhost:8080
```
- ✅ **scan:version:** Detecta la versión del CMS Magento.  

---

### 📝 **Enumeración de Extensiones**
Algunas extensiones pueden ser vulnerables si no se mantienen actualizadas:
```bash
php magescan.phar scan:modules http://localhost:8080
```
- 📂 **scan:modules:** Enumera todas las extensiones y módulos activos.  

---

### 🔐 **Verificación de Seguridad**
Revisa la configuración de seguridad para identificar malas prácticas:
```bash
php magescan.phar scan:security http://localhost:8080
```
- 🔑 **scan:security:** Realiza una verificación de seguridad en la configuración de Magento.  

---

### 📂 **Verificación de Archivos Sensibles**
MageScan puede detectar archivos que están expuestos públicamente:
```bash
php magescan.phar scan:files http://localhost:8080
```
- 🗃️ **scan:files:** Busca archivos expuestos que puedan contener información sensible.  

---

### 💾 **Guardar Resultados en un Archivo**
Para mantener un registro de los hallazgos, puedes guardar los resultados en un archivo:
```bash
php magescan.phar scan:all http://localhost:8080 > magento_scan.txt
```
- 💾 **> magento_scan.txt:** Guarda la salida en un archivo de texto.  

---

### 🚩 **Consejos de Seguridad en Magento**
1. **Actualizar Magento Regularmente:**  
   - Mantén el núcleo y los módulos actualizados para evitar vulnerabilidades.  

2. **Eliminar Archivos de Instalación:**  
   - Elimina archivos como `INSTALL.txt` y directorios de instalación una vez completada la configuración.  

3. **Restringir el Acceso al Backend:**  
   - Configura un firewall o utiliza VPN para acceder al área de administración.  

4. **Desactivar la Información de Versión:**  
   - Evita mostrar públicamente la versión de Magento en el frontend.  

---

💾 **Guarda esta guía en Obsidian para tener siempre a mano las mejores prácticas de seguridad en Magento con MageScan!** 🚀