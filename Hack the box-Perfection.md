Primer de tot farem un nmap per poder veure quins ports hi ah oberts i els serveis que hi ha darrere aquests ports:

```
`┌──(polkali㉿kali)-[~]
└─$ nmap -sC -sV 10.10.11.253
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-15 22:42 CET
Nmap scan report for 10.10.11.253
Host is up (0.056s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 80:e4:79:e8:59:28:df:95:2d:ad:57:4a:46:04:ea:70 (ECDSA)
|_  256 e9:ea:0c:1d:86:13:ed:95:a9:d0:0b:c8:22:e4:cf:e9 (ED25519)
80/tcp open  http    nginx
|_http-title: Weighted Grade Calculator
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.56 seconds`
```

Ens trobem que té el port 22 amb ssh obert a més a més del port 80 que hosteja una pàgina web sobre un servidor amb nginx.

Si anem a la IP, ens trobem que hi ha allotjada al servidor nginx una web que té la funcionalitat de fer de calculadora de ponderacions perquè els estudiants puguin fer aquesta tasca de forma més simple. A sota de tot de la pàgina trobem que està construït amb WEBrick i també hi apareix la versió:

Copyright © Secure Student Tools. All rights reserved  
**Powered by WEBrick 1.7.0**

Ara tocarà investigar una mica a veure si podem trobar algun exploit o vulnerabilitat entorn aquesta eina (WEBrick és una biblioteca de Ruby que proporciona servidors web HTTP simples. Utilitzeu autenticació d'accés bàsica i autenticació d'accés resumida per a diferents tipus de servidors que podeu crear: servidor basat en HTTP, servidor HTTPS, servidor intermediari i servidor d'amfitrió virtual.).

He buscat i no he trobat cap vulnerabilitat per aquesta versió de la llibreria.

Per curiositat he mirat si tenia el fitxer robots.txt i hi he trobat això:

## Sinatra doesn’t know this ditty.

![](http://127.0.0.1:3000/__sinatra__/404.png)

Try this:

get '/robots.txt' do
  "Hello World"
end

Provearem de fer la petició amb GET al servidor amb curl a veure si ens retorna el Hello World com diu:

```
`┌──(root㉿kaliPol)-[/home/polkali]
└─# curl http://10.10.11.253:80/robots.txt
````
<!DOCTYPE html>
<html>
<head>
  <style type="text/css">
  body { text-align:center;font-family:helvetica,arial;font-size:22px;
    color:#888;margin:20px}
  #c {margin:0 auto;width:500px;text-align:left}
  </style>
</head>
<body>
  <h2>Sinatra doesn’t know this ditty.</h2>
  <img src='http://127.0.0.1:3000/__sinatra__/404.png'>
  <div id="c">
    Try this:
    <pre>get &#x27;&#x2F;robots.txt&#x27; do
  &quot;Hello World&quot;
end
</pre>
  </div>
</body>
</html>

Ara faig un repàs amb dirb a veure si trobo algun directori més:

```
`┌──(root㉿kaliPol)-[/home/polkali]
└─# dirb http://10.10.11.253                                                                     

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sat Mar 16 09:52:13 2024
URL_BASE: http://10.10.11.253/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.10.11.253/ ----
+ http://10.10.11.253/about (CODE:200|SIZE:3827)                                                                                                             
                                                                                                                                                             
-----------------
END_TIME: Sat Mar 16 09:59:00 2024
DOWNLOADED: 4612 - FOUND: 1`
```

Només ha trobat un directori que es diu about que ja és visible des de la pàgina principal. Ara però mirant bé el directori about a l'apartat About our team veiem que qui ha fet l'aplicació web no ha aprofundit gaire en la securització del codi:

# About our team

##### Tina Smith

The web developer of our team, Tina is a Computer Science major at Acme University and a bright mind. She was the one who came up with the entire idea for the vision of Secure Student Tools™. **She is an absolute whiz at web development, but she hasn't delved into secure coding too much.**

----------------------------------------------------------------------

Si anem a http://10.10.11.253/weighted-grade-calc veiem que hi ha la calculadora. Aquí, a la columna Category sembla que es pot injectar mentre que a les altres ens obliga a posar un número. 
