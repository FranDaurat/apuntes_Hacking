-- -
#### 🚀 **¿Por qué usar JoomScan en Joomla?**  
Joomla es uno de los CMS más utilizados en la web, pero también es propenso a vulnerabilidades si no se actualiza o configura correctamente.  
**JoomScan** es una herramienta diseñada específicamente para auditar sitios web basados en Joomla y permite:  
- 📝 **Detectar la versión de Joomla**  
- 🛑 **Identificar componentes vulnerables**  
- 🔍 **Buscar archivos sensibles expuestos**  
- 💥 **Detectar vulnerabilidades conocidas**  

---

### 💡 **Instalación de JoomScan**
```bash
sudo apt-get update
sudo apt-get install libwww-perl
git clone https://github.com/OWASP/joomscan.git
cd joomscan
```

---

### 🔎 **Comandos Específicos para Joomla**

#### 🌐 **Escaneo Básico**
Para identificar la versión de Joomla y buscar vulnerabilidades:
```bash
perl joomscan.pl -u http://localhost:8080
```
- **-u:** URL del sitio Joomla.  
- **Versión de Joomla:** Detecta la versión utilizada y verifica si hay exploits conocidos.  

---

### 📂 **Enumeración de Componentes**
Los componentes en Joomla pueden ser vulnerables si no están actualizados:
```bash
perl joomscan.pl -u http://localhost:8080 -ec
```
- **-ec:** Enumera los componentes instalados.  

---

### 🎨 **Enumeración de Templates**
Los templates pueden contener rutas que revelen información sensible:
```bash
perl joomscan.pl -u http://localhost:8080 -et
```
- **-et:** Enumera los templates activos en el sitio.  

---

### 💥 **Detectar Vulnerabilidades Conocidas**
Verifica si la versión de Joomla o algún componente está en la lista de vulnerabilidades:
```bash
perl joomscan.pl -u http://localhost:8080 -vuln
```
- **-vuln:** Realiza un escaneo de vulnerabilidades conocidas.  

---

### 📋 **Guardar Resultados en un Archivo**
Si deseas guardar los resultados para analizarlos posteriormente:
```bash
perl joomscan.pl -u http://localhost:8080 -o resultado.txt
```
- **-o:** Guarda los resultados en un archivo.  

---

### 📝 **Algunos Ejemplos de Uso**

#### 💥 **Escaneo Completo con Reporte**
```bash
perl joomscan.pl -u http://example.com -ec -et -o scan_report.txt
```
- 🔎 **-ec:** Enumera los componentes.  
- 🎨 **-et:** Enumera los templates.  
- 💾 **-o:** Guarda los resultados en un archivo.  

#### 🛑 **Detección de Componentes Vulnerables**
```bash
perl joomscan.pl -u http://example.com -ec -vuln
```
- 📂 **-ec:** Verifica los componentes instalados.  
- 💥 **-vuln:** Busca vulnerabilidades conocidas en la versión de Joomla.  

---

### 🚩 **Consejos de Seguridad en Joomla**
1. **Actualización Constante:**  
   - Siempre mantén la versión de Joomla y los componentes actualizados para mitigar vulnerabilidades conocidas.  

2. **Eliminación de Componentes Innecesarios:**  
   - Cada componente adicional aumenta la superficie de ataque.  

3. **Verificación de Permisos:**  
   - Restringe los permisos de escritura en carpetas críticas como `/administrator` y `/components`.  

4. **Deshabilitar la Información de Versión:**  
   - No mostrar la versión de Joomla en el frontend para evitar facilitar el reconocimiento a los atacantes.  

---
