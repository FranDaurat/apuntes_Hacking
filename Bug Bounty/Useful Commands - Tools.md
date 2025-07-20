-- - 
### Dalfox (XSS)
```bash
cat wayback_cleanParams | dalfox pipe --worker 20 --timeout 10 --delay 100
dalfox file endpoints/filteredParameters.txt --worker 20 --delay 100 >> testing/xss_results.txt 2> testing/dalfox_errors.log
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

### Sqlmap
```bash
sqlmap -m endpoints/filteredParameters.txt --batch --dbs --level 2 --risk 2 --timeout 10 --tamper="space2comment,between,randomcase" > testing/sqli_results.txt
```

### Openredirex
```bash
cat endpoints/parameters.txt | openredirex | grep -vE "ERROR" >> testing/openRedirect_results.txt
```

### trufflehog
```bash
trufflehog git https://github.com/doit/repo --since_commit HEAD~100
```

### Arjun
**Passive parameter discovery**
```bash
arjun -u https://site.com/endpoint.php -oT arjun_output.txt -t 10 --rate-limit 10 --passive -m GET,POST --headers "User-Agent: Mozilla/5.0"
```

**Active parameter discovery with wordlist**
```bash
arjun -u https://site.com/endpoint.php -oT arjun_output.txt -m GET,POST -w /usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt -t 10 --rate-limit 10 --headers "User-Agent: Mozilla/5.0"
```

### ChatGPT nuclei template generator
https://chatgpt.com/g/g-REMZCNZvB
---
## SSL Certificate Parsers (CIDR Recon)

```bash
echo $CIDR | httpx -t 100 | nuclei -t ${USER_HOME}/nuclei-templates/ssl/ssl-dns-names.yaml | cut -d " " -f7|cut -d "]" - f1 | sed 's/[//' | sed 's/,/\n/g'| sort -u 
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

### Installation: 

```bash
#!/bin/bash

companies=(amazon digitalocean google microsoft oracle)

for companie in "${companies[@]}"; do
  wget http://kaeferjaeger.gay/sni-ip-ranges/$companie/ipv4_merged_sni.txt -O ipv4_${companie}_merged_sni.txt
done

cat *.txt | grep "$domain" | cut -d "[" -f2 | cut -d "]" -f1 | sort -u | grep "$domain"
cat *.txt | grep -F ".$domain" | awk -F '--' '{print $2}' | tr ' ' '\n' | tr '[' ' ' | sed 's/ //' | sed 's/\]//' | grep -F ".$domain" | sort -u
```

## Cloud Recon Tool
https://github.com/g0ldencybersec/CloudRecon

```bash 
CloudRecon scrape -i CIDR 

grep -F ".$domain" "$file" | awk -F '[][]' '{print $2}' | sed 's# #\n#g' | grep ".$domain" | sort -fu | cut -d ',' -f1 | sort -u
cat test.txt | awk -F'[][]' '{print $2}' | tr ',' '\n' | sed 's/^ //g' | sort -u ----> encuentra todos los dominios
grep -F ".$domain" "$file" | awk -F '[][]' '{print $2}' | sed 's# #\n#g' | sort -fu | cut -d ',' -f1 | sort -u

```
----
## WAF Bypass

```bash 
	dnsrecon -d "$domain"
	Ssl.cert.subject.CN:'example.com' 200 ----> buscar en shodan
	nmap --script ssl-cert -p 443,80 "$ip" ----> Confirmamos que la ip apunta al nombre de dominio correspondiente sin el waf de por medio
	http://favicons.teamtailor-cdn.com/icons?url=${domain}#result ----> sacamos la url con el favicon
	http://favicon-hash.kmsec.uk/ ----> pegamos la url de favicon aca y obtenemos el hash para luego buscarlo en shodan o censys con el dork correspondiente
	https://viewdns.info/iphistory/?domain=${domain}
	
```
---
