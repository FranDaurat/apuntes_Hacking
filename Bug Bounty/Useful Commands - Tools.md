-- - 
### Dalfox (XSS)
```bash
cat wayback_cleanParams | dalfox pipe --worker 20 --timeout 10 --delay 100
```

### XSStrike
```bash
python3 xsstrike.py -u "URL" -l 4 -t 10
```

### go-dork 
```bash
go-dork -e google -q "QUERY" -p 50
```

### Hidden endpoints on JS files

``` bash
echo "admin.example.com" | waybackurls | grep -P ".*\.js" | wget
grep -RoP "(\/[a-zA-Z0-9_\-]+)+"
```
---
## SSL Certificate Parsers (CIDR Recon)

```bash
echo $CIDR | httpx -t 100 | nucei -t ${USER_HOME}/nuclei-templates/ssl/ssl-dns-names.yaml | cut -d " " -f7|cut -d "]" - f1 | sed 's/[//' | sed 's/,/\n/g'| sort -u 
```

```bash
echo $CIDR | httpx -t 100 -silent -json -tls-grab | jq -r .'"tls-grab".dns_names[]' | sort -u 
```

```bash
cero $CIDR | sort -u 
```

```bash
prips 192.12.32.0/22 | hakip2host -p 100 | sort -u
```

--- 
## Cloud Recon 

### Sources: 
http://kaeferjaeger.gay/sni-ip-ranges
https://www.youtube.com/watch?v=7hKEfF-yR1w
https://github.com/lord-alfred/ipranges.git

```bash
#!/bin/bash

companies=(amazon digitalocean google microsoft oracle)

for companie in "${companies[@]}"; do
  wget http://kaeferjaeger.gay/sni-ip-ranges/$companie/ipv4_merged_sni.txt -O ipv4_${companie}_merged_sni.txt
done

cat *.txt | grep "$domain" | cut -d "[" -f2 | cut -d "]" -f1 | sort -u | grep "$domain"
```

---

