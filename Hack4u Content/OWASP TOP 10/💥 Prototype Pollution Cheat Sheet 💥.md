---

### 💥 **Prototype Pollution Cheat Sheet** 💥

---

#### 📝 **¿Qué es el Prototype Pollution?**  
El ataque **Prototype Pollution** explota vulnerabilidades en la implementación de objetos en **JavaScript**.  
Permite al atacante **modificar la propiedad `prototype`** de un objeto, lo que puede llevar a:  
- 🚀 **Ejecución de código malicioso.**  
- 🛠️ **Manipulación de datos de la aplicación.**  
- 🔑 **Control de sesiones de usuario.**  

---

### ⚙️ **¿Cómo funciona?**  
1. Los objetos en JavaScript tienen una propiedad llamada `prototype` que define sus métodos y propiedades.  
2. El atacante modifica la propiedad `prototype` de un objeto base, lo que afecta a todas las instancias del objeto.  
3. La aplicación hereda las propiedades manipuladas, lo que puede dar acceso o control no autorizado.  

---

### 📝 **Impacto del Ataque:**  
- 📊 **Compromiso de datos del usuario.**  
- 💥 **Ejecución remota de código.**  
- 🔓 **Escalada de privilegios.**  
- 🕵️ **Dificultad en detección y corrección.**  

---

## 🚀 **Ejemplo de Explotación:**

### 🔧 **Cambio del Content-Type:**

Algunas aplicaciones envían datos en formato URL-encoded:  
```http
Content-Type: application/x-www-form-urlencoded
email=test@test.com&msg=carlos
```

El truco aquí es cambiar el **Content-Type** a JSON:  
```http
Content-Type: application/json
{
    "email": "test@test.com",
    "msg": "probando"
}
```

---

### 🔎 **¿Cómo detectar si funciona?**  
1. Verifica si el servidor interpreta los datos en formato JSON.  
2. Intenta provocar un error de sintaxis (faltante de comillas, llave incorrecta) para ver si la solicitud falla.  
3. Si el servidor **no rompe** o **redirige correctamente**, indica que el formato JSON es aceptado.  

---

### 💥 **Payload de Prototype Pollution:**  
Una vez confirmado el uso de JSON, intenta inyectar el prototipo:  
```http
Content-Type: application/json
{
    "email": "test@test.com",
    "msg": "probando",
    "__proto__": {
        "admin": true
    }
}
```

---

### 🔑 **¿Cómo descubrir el campo vulnerable?**  
- 🔍 **Intuición:** Campos comunes como `admin`, `isAdmin`, `role`, `superuser`, etc.  
- 🦾 **Fuerza Bruta:** Prueba múltiples nombres de campo.  
- 📝 **Filtración del Código Fuente:** Si el código fuente está accesible, busca objetos con privilegios elevados.  

---

💾 **Guarda esta guía en Obsidian para tener siempre a mano tus técnicas de Prototype Pollution!** 🚀