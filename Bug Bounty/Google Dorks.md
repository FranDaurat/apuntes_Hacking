-- -
### Open Redirect Mass Hunting
```http
site:.gob inurl:redir | inurl:redirect | inurl:return | inurl:src=http | inurl:r=http | inurl:goto
```

### IIS windows server mass hunting
```http
site:.us intitle:"IIS Windows Server" 
```

### S3 Buckets
``` http
site:s3.amazonaws.com "target.com"
site:*.s3.amazonaws.com "target.com"
site:s3-external-1.amazonaws.com "target.com"
site:s3.dualstack.us-east-1.amazonaws.com "target.com"
site:amazonaws.com inurl:s3.amazonaws.com 
site:s3.amazonaws.com intitle:"index of"  
site:s3.amazonaws.com inurl:".s3.amazonaws.com/"  
site:s3.amazonaws.com intitle:"index of" "bucket"

(site:*.s3.amazonaws.com OR site:*.s3-external-1.amazonaws.com OR site:*.s3.dualstack.us-east-1.amazonaws.com OR site:*.s3.ap-south-1.amazonaws.com) "target.com"
```

### All Purpose Dorks
```http 
filetype:ini "password" site:orgfiletype:txt "credentials" site:gov
filetype:yaml "secret_key" -examples
filetype:key "PRIVATE KEY"
filetype:pem "PRIVATE KEY"
filetype:log "debug" "error"
filetype:log "Stack trace" site:edu
filetype:log "unable to connect"
filetype:log "authentication failed"
filetype:json "db_password" -github
filetype:db "database" site:org
filetype:sql "INSERT INTO" "VALUES" site:edu
filetype:dump "database" site:gov
"index of" "backup.sql"
filetype:conf "db_user" site:org
filetype:config "ftp" site:gov
filetype:xml "web.config" site:edu
"index of" "settings.json"
filetype:env "SECRET_KEY"
"index of" "api_key"
filetype:json "api_token"
filetype:txt "api_secret"
"admin login" filetype:php
"index of" "server-status"
filetype:php "mysql_connect" site:gov
"admin dashboard" "login"
filetype:pdf "not for distribution" site:gov
filetype:xlsx "confidential report" site:edu
filetype:doc "salary" "employee"
filetype:docx "restricted access"
filetype:xlsx | filetype:xls "username" "password" site:gov
filetype:xlsx | filetype:xls "username" site:gov
filetype:xlsx | filetype:xls "database" site:gov
filetype:xlsx | filetype:xls "financial" site:gov
filetype:xlsx | filetype:xls "password" site:gov
site:dropbox.com "password"
site:box.com "confidential"
site:drive.google.com "important"
site:onedrive.live.com "restricted"
site:pastebin.com "password"
site:github.com "SECRET_KEY"
site:gitlab.com "PRIVATE_KEY"
site:bitbucket.org "db_password"
```

### Github
```http
/(?i)(password|passwd|pwd|secret|token|apikey|api_key|access_key|secret_key|access_token|api_secret|apiSecret|app_secret|application_key|app_key|appkey|auth_token|authsecret)\s*=\s*["'][^"']{4,}["']/ AND org:adobe AND NOT language:Markdown NOT is:archived

/(?i)(password|passwd|pwd|secret|token|apikey|api_key|access_key|secret_key|access_token|api_secret|apiSecret|app_secret|application_key|app_key|appkey|auth_token|authsecret)\s*=\s*["'][^"']{4,}["']/ "@org" AND NOT language:Markdown NOT is:archived 

org:${org} "sk_live"
org:${org} "pk_live"
org:${org} path:*.json
org:${org} AWS_ACCESS_SECRET_KEY

```