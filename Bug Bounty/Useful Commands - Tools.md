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

### Installation: 

```bash
#!/bin/bash

companies=(amazon digitalocean google microsoft oracle)

for companie in "${companies[@]}"; do
  wget http://kaeferjaeger.gay/sni-ip-ranges/$companie/ipv4_merged_sni.txt -O ipv4_${companie}_merged_sni.txt
done

cat *.txt | grep "$domain" | cut -d "[" -f2 | cut -d "]" -f1 | sort -u | grep "$domain"
```

## Cloud Recon Tool
https://github.com/g0ldencybersec/CloudRecon

```bash 
CloudRecon scrape -i CIDR 

grep -F ".$domain" "$file" | awk -F '[][]' '{print $2}' | sed 's# #\n#g' | grep ".$domain" | sort -fu | cut -d ',' -f1 | sort -u
cat test.txt | awk -F'[][]' '{print $2}' | tr ',' '\n' | sed 's/^ //g' | sort -u ----> encuentra todos los dominios
grep -F ".$domain" "$file" | awk -F '[][]' '{print $2}' | sed 's# #\n#g' | sort -fu | cut -d ',' -f1 | sort -u

```

--- 
## Extensions Used

- [ ] Wapalyzer
- [ ] PwnFox
- [ ] Shodan
- [ ] Dark Reader
- [ ] Cookie-Editor
- [ ] Hack-Tools
- [ ] Temp-Mail
- [ ] Link-Extractor
- [ ] Open Multiple URLs
- [ ] Retire.js
- [ ] Hunter.io
- [ ] XNL-Reveal

---
## Bookmarks Used

### OSINT
- [ ] www.dateas.com/es/consulta_cuit_cuil
- [ ] start.me/p/0Pqbdg/osint-500-tools
### Pentesting
- [ ] gtfobins.github.io
- [ ] book.hacktricks.xyz
### Bug bounty
- [ ] chaos.projectdiscovery.io
- [ ] bbradar.io
- [ ] xss.report
- [ ] wordlists.assetnote.io
- [ ] lostsec.xyz
- [ ] github.com/swisskyrepo/PayloadsAllTheThings/tree/master
- [ ] github.com/danielmiessler/SecLists/
- [ ] www.vccgenerator.org
- [ ] www.akto.io/tools/credit-card-generator
- [ ] web.archive.org
### ASNS
- [ ] bgp.he.net
- [ ] ipinfo.io
- [ ] asnlookup.com
### Etc
- [ ] whatismyipaddress.com

----
