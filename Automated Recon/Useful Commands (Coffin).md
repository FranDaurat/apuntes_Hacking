-- - 
### Delfox (XSS)
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