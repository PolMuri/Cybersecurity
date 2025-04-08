És una app feta amb Python i Flask i que no funciona

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/9bda6e76-0a3e-4a93-a734-9dac6bded607)
 

Reddit ha bloquejat la app, però per el challenge ens serveix ja que farem un atac d’URL. Ara hauríem de veure una aplicació sobre gats.

  

Com tots els challenge que hem fet fins ara està amb Docker:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/f77a60df-ea9d-4d07-acfc-9357d1d6150e)
 

Anem a veure el fitxer Dockerfile i busquem info sobre la flag i cap al final del document veiem que la flag està a una variable d’entorn i a més sabem el nom que té aquesta variable:

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/891fa524-d8ff-44d9-b35b-d282fa94d499)
 


Ara analitzarem el codi de la app per veure com funciona. Primer obrim el run.py:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/661d06d3-fc64-46b7-ab3e-ab5debca538f)
 

Veiem com al app fa un run per totes les IP’s de la màquina per aquest port concret, per tant nosaltres hem de fer una petició cap aquí per poder accedir a les variables d’entorn.

``app.run(host='0.0.0.0', port=1337)``

A aquest fitxer no hi ha més informació, anem ara a application → blueprints i anem al fitxer routes.py que és un fitxer de Flask:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/ca1e2418-a6d0-44e5-98f1-3df537247f04)
  


Veiem les 3 redireccions que es fan:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/8ca900cb-3c2b-4362-b17b-5d0903daf564)
 


Però anem a la ruta on hi ha les rutes d’entorn, i veiem com les carrega i les mostra per pantalla, aquest serà l’endpoint que voldrem atacar:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/e09c2941-188c-4211-9272-d08cbd761db1)
 

  

El nostre objectiu és accedir a la IP + port + /debug_environment

  
```
`@debug.route('/environment', methods=['GET'])

@is_from_localhost

def debug_environment():

environment_info = {

'Environment variables': dict(os.environ),

'Request headers': dict(request.headers)

}
````
  

El que fa aquesta funció debug/environment és carregar les variables d’entorn i mostrarles per pantalla a més de carregar els headers.


També veiem que carrega la funció: @is_from_localhost i si aquesta funció dona no no s’executa, si dona sí si que s’executa.


Anem a buscar què fa aquesta funció doncs i la trobem al fitxer util.py:


```
`def is_from_localhost(func):

@functools.wraps(func)

def check_ip(*args, **kwargs):

if request.remote_addr != '127.0.0.1':

return abort(403)

return func(*args, **kwargs)

return check_ip`
```

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/3fbff68a-dd2c-4dab-bc0e-bb4d5389901a)


  
Fa un chek_ip i diu que si el request no rpové del localhost retorni un 403, per tant en teoria haurem d’entrar per 127.0.0.1

  
Si tornem al fitxer routes.py veiem que / carrega la funció proxy que és el que no està funcionant:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/b1578d36-0eee-4ef6-8c37-fc0fd63b6742)
 

  

```
`@proxy_api.route('/', methods=['GET', 'POST'])

def proxy():

url = request.args.get('url')`
```

  

```
`if not url:

cat_meme_subreddits = [

'/r/cats/',

'/r/catpictures',

'/r/catvideos/'

]`
```

  

``random_subreddit = random.choice(cat_meme_subreddits)``

Veiem també que la url que carrega la posa aquí:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/08a77db4-3797-480f-ace4-54ceae3e2c92)


  
``SITE_NAME`` és una variable:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/73b66078-6884-41c2-a37f-8deb6f50cc00)
 

  

No podem modificar la part de reddit.com però sí la següent, per tant haurem de fer un bypass de combinació i farem que l’usuari sigui reddit.com, que es pensarà que és l’usuari que es vol connectar al servidor.

  
Redirigim la connexió de reddit.com al que posem després de @. Ens permet fer el bypass l’@. La nostra arma és posar això:

``83.136.249.57:39803/?url=/127.0.0.1:1337/debug/environment``


Al ser la petició interna seva el 127 es fa al servidor. Però, aquesta IP està restringida:

  

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/3b984b3e-f6b5-4ec7-8389-0701ec1bcf2e)
 


Però si ho provem amb 0.0.0.0 sí que ens funciona:

  

``http://83.136.249.57:39803/?url=@0.0.0.0:1337/debug/environment``



![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/7c6ce383-4e18-4322-9829-2578ee720965)
 

I ja tenim al flag: **HTB{fl4gs_4s_4_S3rv1c3}**

![image](https://github.com/PolMuri/Hack-the-box/assets/109922379/7d74cc76-f8f7-4a97-9e18-1683b1cd2f40)

