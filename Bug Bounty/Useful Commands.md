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