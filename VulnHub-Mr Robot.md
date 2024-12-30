Esls ports oberts de la víctima?
```
PORT    STATE  SERVICE
80/tcp  open   http
443/tcp open   https
```
El que veiem al entrar a la web és que hi ha la sèrie Mr Robot representada.




Cercant possibles noms de carpetes i fitxers del servidor web amb la comanda nmap i l’script http-enum, trobem ña URL des d'on podem fer un login.

/wp-login.php: Wordpress login page.

![image](https://github.com/user-attachments/assets/900a8374-797c-4de0-be38-82f3c95b41bd)


On veiem que el programari per fer la pàgina web és un wordpress:

![image](https://github.com/user-attachments/assets/0d4efaa3-36a2-46f0-bc22-b873d84465c5)

![image](https://github.com/user-attachments/assets/9b224601-cbc1-44dc-9889-6f3b52c59466)

Ara provarem de fer un atac de força bruta amb els següents usuaris:

El nom de l’usuari per defecte és admin, llavors provaria admin, però també es podria provar administrator, administrador, root, etc. 

Si parlem de la sèrie robot, mrobot o elliot.

Per tant amb la eina wpscan + diccionaris farem l'atac de força bruta. Quin és l’usuari i la contrasenya correcta?

![image](https://github.com/user-attachments/assets/eb330fc0-fd5a-48f7-bc33-a865d1d42600)

![image](https://github.com/user-attachments/assets/f2af87fc-4a28-4048-8ae5-d47e6ed9f706)

I amb això trobem que l'usuari és elliot i la contrasenya és qosqomanta

Ara provem d'accedir a la màquina amb les credencials i ens trobem que som el següent usuari:

![image](https://github.com/user-attachments/assets/c31180ff-a42e-4c79-bedc-4b4e2b5fad79)

I si mirem a /etc/passwd els usuaris que hi ha al sistema veiem el següent:

![image](https://github.com/user-attachments/assets/979a183c-11a0-445d-af77-9e0d786d4066)

Si mirem els fitxers del home de l'usuari robot trobem el següent:

![image](https://github.com/user-attachments/assets/45754896-a366-4b2f-a593-5eeb587e60c5)

I la passwd de l'usuari robot la trobem amb el següent script:

``python -c 'import pty; pty.spawn("/bin/bash")'``

![image](https://github.com/user-attachments/assets/97a50322-864c-48ba-97e1-54fea4dca72a)

``abcdefghijklmnopqrstuvwxyz``


Ara mirarem d'escalar privilegis, per fer-ho trobem que podem mirar el SUID i n'hi ha algun que ens pot ser útil:
````
robot@linux:~$ find /* -user root -perm -4000 -print 2> /dev/null
find /* -user root -perm -4000 -print 2> /dev/null
/bin/ping
/bin/umount
/bin/mount
/bin/ping6
/bin/su
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/local/bin/nmap
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/pt_chown
````

La comanda que escriurem amb nmap per obtenir la shell amb permisos de l'usuari root és la següent:

Tenim aquesta versió de nmap:

![image](https://github.com/user-attachments/assets/236f83c6-efba-4c88-9b25-57a33a263f72)

I amb el que se’ns indica anem a https://gtfobins.github.io/gtfobins/nmap/  

I el que haurem de fer per obtenir el shell amb permisos root és això:

![image](https://github.com/user-attachments/assets/ed976481-38a0-4e53-8e6a-aaa9bf3c5e8b)


I ara ho fem:

![image](https://github.com/user-attachments/assets/79609ccb-b035-4b72-8123-4bb4e2942791)

I amb aquestes comandes ja tenim els permisos i estem amb l'usuari root.


