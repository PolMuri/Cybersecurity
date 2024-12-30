La màquina víctima té oberts els següents ports:

![image](https://github.com/user-attachments/assets/97469e09-7532-4ce6-bbb2-4bfe38cf742f)

Al port 8080 hi trobem el contingut d'una pàgina web que està under construction:

![image](https://github.com/user-attachments/assets/cc42a2b3-213c-4d4c-982c-7813609c9d82)

Si provem alguna url de les més comunes, a veure si ens topem amb algun domini del qual podem extreure alguna cosa ens trobem amb el següent: 

![image](https://github.com/user-attachments/assets/d2a28320-34dc-4a6b-b294-a45743ff7ca5)

I podem treure aquestes 3 url’s: robots.txt, index, mercuryfacts 

Veiem també que la web està feta amb Django.

Accedim a la url de mercuryfacts, i veiem el següent:

![image](https://github.com/user-attachments/assets/ba3e7407-2aba-4000-880e-64c28cbb6e7a)

Si fem alguna prova i hi afegim un número a la url de forma aleatòria ens retorna “facts” sobre el planeta (entenc que busca a la BD per ID que és el que passem de número “facts” que hi té guardats):

![image](https://github.com/user-attachments/assets/0274801b-7361-427f-9fe2-ff3485ffbd5d)

![image](https://github.com/user-attachments/assets/c1d95201-e6a5-4540-8a32-340b9d7f42b2)

Ara utilitzarem sqlmap a veure si trobem alguna BD darrere de la url mercuryfacts:

![image](https://github.com/user-attachments/assets/ad8ccece-5c3e-401a-993c-07edadbf26e7)

Pel que sembla la BD que hi ha al darrere és un MySQL:

![image](https://github.com/user-attachments/assets/90990e46-1532-4146-aa24-7f395bb27dc0)

Ara trobem els noms d’usuaris i contrasenyes de la BD mercury:

![image](https://github.com/user-attachments/assets/aa787451-4f4f-4a3f-bf3c-f2e27a446b19)

Ara amb la informació que tenim ja podem entrar al servidor:

![image](https://github.com/user-attachments/assets/070da7a0-bd13-43a3-b213-c62878ff0ecf)

I obtenim les credencials d'un segon usuari amb més privilegis:

![image](https://github.com/user-attachments/assets/ae131e79-7624-4d56-80dd-b2804ba97c49)

![image](https://github.com/user-attachments/assets/0e08622f-7dae-4ed6-ba41-ca9e186ef064)

Veig que hi ha root, mercury o també hi podria haver linuxmaster. Ara obtindrem les credencials de l’usuari linuxmaster ja que hi ha la seva password guardada en format Base64:

![image](https://github.com/user-attachments/assets/46fccc2b-21a8-49f3-8b4b-1272d184c998)

I obtenim la seva password:

![image](https://github.com/user-attachments/assets/a680f00d-5140-4913-9bbc-bde8bfdc585e)

I ara ens loguejem amb l’usuari linuxmaster:

![image](https://github.com/user-attachments/assets/66a2b4d8-d38a-4e94-bc5f-3bff1627c85a)

Escalada de privilegis per ser root:

``find / -type f -perm -u=s 2>/dev/null``

![image](https://github.com/user-attachments/assets/a0c39910-5f76-46f6-9fa8-5e7ca1939927)

sudo -l 

![image](https://github.com/user-attachments/assets/487851bb-cf12-49cf-9105-accb819ffac6)


On veiem que l'usuari té privilegis a l'script check_syslog.sh que l'utilitzem per obtenir privilegis i accedir amb l'usuari root.
