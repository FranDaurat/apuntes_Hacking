- --
#### 🚀 **¿Por qué usar WPScan en WordPress?**  
WordPress es uno de los CMS más populares del mundo, pero también uno de los más atacados.  
WPScan es una herramienta especializada en detectar vulnerabilidades en sitios web basados en **WordPress**, permitiendo:  
- 🔍 **Enumerar usuarios y plugins**  
- 💥 **Detectar vulnerabilidades conocidas**  
- 🔑 **Realizar ataques de fuerza bruta**  
- 📦 **Identificar versiones de plugins y temas**  

---

### 💡 **Instalación de WPScan**
WPScan está escrito en Ruby y se puede instalar fácilmente:  
```bash
sudo apt update
sudo apt install wpscan
```

---

### 🔎 **Comandos Específicos para WordPress**

#### 🌐 **Escaneo Básico**
Detecta la versión de WordPress y busca vulnerabilidades conocidas:  
```bash
wpscan --url http://localhost:31337
```
- **--url:** Especifica la URL del sitio web.  

---

### 🧑‍💻 **Enumeración de Usuarios**
Detecta nombres de usuario válidos para facilitar futuros ataques de fuerza bruta:  
```bash
wpscan --url http://localhost:31337 --enumerate u
```
- **--enumerate u:** Enumera los usuarios registrados.  

---

### 🔌 **Escaneo de Plugins Vulnerables**
Revisa los plugins instalados y busca vulnerabilidades conocidas:  
```bash
wpscan --url http://localhost:31337 -e vp --api-token=U6B2wfRc3asoLdIsHHZekg1ZxU02P5QYaCHm5UCJosw
```
- **-e vp:** Enumera los plugins vulnerables.  
- **--api-token:** Utiliza un token de API para acceder a la base de datos de vulnerabilidades de WPScan.  

---

### 💣 **Ataque por Fuerza Bruta**
Realiza un ataque por diccionario contra un usuario en particular:  
```bash
wpscan --url http://localhost:31337 -U fran -P /usr/share/wordlists/rockyou.txt
```
- **-U:** Usuario objetivo.  
- **-P:** Archivo de diccionario para el ataque de fuerza bruta.  

---

### 💥 **Escaneo Agresivo desde LostSec**
Escaneo agresivo que incluye la detección de plugins, temas y usuarios:  
```bash
wpscan --url https://site.com --disable-tls-checks --api-token=<here> -e at -e ap -e u --enumerate ap --plugins-detection aggressive --force
```
- **--disable-tls-checks:** Desactiva la verificación TLS en sitios con certificados no válidos.  
- **-e at:** Enumera temas activos.  
- **-e ap:** Enumera plugins activos.  
- **-e u:** Enumera usuarios.  
- **--plugins-detection aggressive:** Usa técnicas agresivas para detectar plugins.  
- **--force:** Fuerza el escaneo incluso si hay advertencias.  

---

### 🚩 **Consejos de Seguridad en WordPress**
1. **Mantén WordPress y Plugins Actualizados:**  
   - Las vulnerabilidades son comunes en versiones desactualizadas.  

2. **Usa Contraseñas Fuertes:**  
   - Evita el uso de contraseñas predecibles como `admin` o `password123`.  

3. **Restringe el Acceso al Panel de Administración:**  
   - Utiliza autenticación de dos factores y permite el acceso solo desde IPs de confianza.  

4. **Monitoriza los Plugins y Temas Instalados:**  
   - Desinstala plugins o temas que no estén en uso para reducir la superficie de ataque.  

---

💾 **Guarda esta guía en Obsidian para tener siempre a mano las técnicas de auditoría en WordPress con WPScan!** 🚀