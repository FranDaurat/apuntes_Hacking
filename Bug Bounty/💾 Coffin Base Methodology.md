-- - 
```bash
subfinder -d $1 -all -recursive > subdomains.txt 

httpx -l subdomains.txt -ports 80,8080,8888,8000 -threads 200 > subdomains_Alive.txt

naabu -list subdomains_Alive.txt -c 50 -nmap-cli 'nmap -sV -sC' -o naabu-full.txt

dirsearch -l subdomains_Alive.txt -i 200,204,403,443 -x 500,502,429,501,503 -R 5 --random-agent -t 120 -F -w /home/elgordoponcio/Desktop/all_coffin_things/oneListForall/onelistforallshort.txt -o directory.txt

cat subdomais_Alive.txt | gau --providers wayback,commoncrawl,otx,urlscan --verbose > newparms.txt
cat newparms.txt | uro > filterparm.txt
cat filterparm.txt | httpx -fc 404 | sort -u | sponge filterparm.txt


cat filterparm.txt | grep ".js$" > jsfiles.txt

cat jsfiles.txt | while read url; do python3 /home/elgordoponcio/go/bin/SecretFinder.py -i $url -o cli >> secret.txt; done

cat secret.txt | grep -i aws
cat secret.txt | grep -i google
cat secret.txt | grep -i twilio
cat secret.txt | grep -i heroku

nuclei -list filterparm.txt -t /home/elgordoponcio/.local/nuclei-templates
```
