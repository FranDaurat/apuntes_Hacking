-- -
#### 🚀 **¿Qué es Droopescan?**  
**Droopescan** es una herramienta diseñada para realizar escaneos de seguridad en sitios web que utilizan CMS populares.  
Es particularmente útil para detectar vulnerabilidades, enumerar versiones y recolectar información sensible.  

---
### 💡 **Instalación**
Droopescan está desarrollado en **Python 3**, por lo que necesitás tenerlo instalado.  
Puedes instalarlo directamente desde **pip**:  
```bash
pip install droopescan
```

También puedes clonarlo desde su repositorio en GitHub:  
```bash
git clone https://github.com/droope/droopescan.git
cd droopescan
pip install -r requirements.txt
```

---

### 🔍 **Comandos Básicos**

#### 🌐 **Escaneo General de CMS**
```bash
python3 droopescan scan -u https://example.com
```

#### 🧩 **Enumeración de Plugins**
```bash
python3 droopescan scan -u https://example.com -p
```
- ✅ **Plugins:** Busca plugins instalados en el CMS.

#### 📂 **Enumeración de Temas**
```bash
python3 droopescan scan -u https://example.com -t
```
- ✅ **Temas:** Detecta los temas que está utilizando el sitio web.

---

### 💥 **Escaneo de Vulnerabilidades**
```bash
python3 droopescan scan -u https://example.com -v
```
- 🔎 **Vulnerabilidades:** Realiza un escaneo rápido en busca de vulnerabilidades conocidas.  

---

### 🧠 **Detección Automática del CMS**
```bash
python3 droopescan scan -u https://example.com --cms
```
- 🔍 **Auto-detección:** Identifica el CMS automáticamente sin necesidad de especificarlo.  

---

### 🌐 **Escaneo Específico por CMS**
Droopescan soporta varios CMS, como:
- **Drupal**
- **SilverStripe**
- **WordPress**
- **Joomla**

Ejemplo para Drupal:
```bash
python3 droopescan scan -u https://example.com -t -e drupal
```

---

### 🚀 **Acelerando el Escaneo (Multi-Threading)**
Podés aumentar la velocidad del escaneo usando múltiples hilos:  
```bash
python3 droopescan scan -u https://example.com -t 50
```
- 🏎️ **-t:** Número de hilos a usar.  

---

### 📝 **Almacenamiento de Resultados**
Para guardar los resultados en un archivo de texto:
```bash
python3 droopescan scan -u https://example.com -o resultado.txt
```

---

### 🧩 **Algunas Opciones Adicionales Útiles**
| Opción          | Descripción                                                                 |
|----------------|----------------------------------------------------------------------------|
| `-e`           | Especifica el CMS que deseas escanear.                                       |
| `-p`           | Detecta los plugins instalados.                                              |
| `-t`           | Detecta los temas activos en el CMS.                                         |
| `-v`           | Realiza un escaneo de vulnerabilidades.                                      |
| `-o`           | Guarda los resultados en un archivo.                                         |
| `--help`       | Muestra todas las opciones disponibles.                                      |

---

### 💡 **Ejemplos de Uso**
1. **Enumeración completa en Drupal:**
   ```bash
   python3 droopescan scan -u https://example.com -e drupal -p -t -v
   ```
2. **Detección de vulnerabilidades en Joomla:**
   ```bash
   python3 droopescan scan -u https://example.com -e joomla -v
   ```
3. **Escaneo con resultados almacenados:**
   ```bash
   python3 droopescan scan -u https://example.com -o resultado.txt
   ```

---

💾 **Guarda esta referencia en Obsidian para tener siempre a mano tus técnicas con Droopescan!** 🚀