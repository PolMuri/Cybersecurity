
Arrel de veure el video d'en s4vitar fent aquest challenge, jo també m'he animat a fer-lo.

Primer de tot despleguem el challenge, això ens mostra el següent, com si s'estigués escrivint per pantalla en directe:

![image](https://github.com/user-attachments/assets/760c0908-05a4-46ae-9769-f3e673fd5d2c)


A sota de tot veiem com tenim una espècie de shell, provo de posar comandes a veure si ens les deixa executar:

![image](https://github.com/user-attachments/assets/02364038-edb3-4673-b654-caf9a16dc11b)


No ens deixa executar cap comanda però diu que si posem help veurem una llista de les comandes que podem executar:

![image](https://github.com/user-attachments/assets/7adf686e-8bce-4566-9e9a-e44d08d3df8a)

Sembla que estem davant d'un joc. Posarem Start i començarem a jugar, a veure fins on arribem:

![image](https://github.com/user-attachments/assets/aabdeb6d-0307-453a-8a08-963798e914be)

Veig que és indiferent el que posi, ja que la finalitat del challenge no és completar el joc. Vaig a mirar el codi font a veure si veig alguna cosa interessant, ja que la idea és aconseguir una flag:

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Flag Command</title>
  <link rel="stylesheet" href="[/static/terminal/css/terminal.css](view-source:http://83.136.251.68:36799/static/terminal/css/terminal.css)" />
  <link rel="stylesheet" href="[/static/terminal/css/commands.css](view-source:http://83.136.251.68:36799/static/terminal/css/commands.css)" />
</head>

<body style="color: #94ffaa !important; position: fixed; height: 100vh; overflow: scroll; font-size: 28px;font-weight: 700;">
  <div id="terminal-container" style="overflow: auto;height: 90%;">
    <a id="before-div"></a>
  </div>
  <div id="command">
    <textarea id="user-text-input" autofocus></textarea>
    <div id="current-command-line">
      <span id="commad-written-text"></span><b id="cursor">█</b>
    </div>
  </div>
  <audio id="typing-sound" src="[/static/terminal/audio/typing_sound.mp3](view-source:http://83.136.251.68:36799/static/terminal/audio/typing_sound.mp3)" preload="auto"></audio>
  <script src="[/static/terminal/js/commands.js](view-source:http://83.136.251.68:36799/static/terminal/js/commands.js)" type="module"></script>
  <script src="[/static/terminal/js/main.js](view-source:http://83.136.251.68:36799/static/terminal/js/main.js)" type="module"></script>
  <script src="[/static/terminal/js/game.js](view-source:http://83.136.251.68:36799/static/terminal/js/game.js)" type="module"></script>

  <script type="module">
    import { startCommander, enterKey, userTextInput } from "/static/terminal/js/main.js";
    startCommander();
    window.addEventListener("keyup", enterKey);
    // event listener for clicking on the terminal
    document.addEventListener("click", function () {
      userTextInput.focus();
    });


  </script>
</body>

</html>
```

No hi ha res interessant, anem a mirar els recursos i fitxers que carrega a veure si veiem alguna cosa que ens pugui ser més útil. Per exemple hi ha un fitxer anomenat options on podem veure les diferents opcions del joc:

![image](https://github.com/user-attachments/assets/605164d0-3d18-4118-bf5d-575471b52cff)


Molt interessant, hi ha un secret:

![image](https://github.com/user-attachments/assets/ab4aa9ee-e45a-4942-b9f8-56fc141d46a3)


Això, està al fitxer options i és un llistat de comandes que podem posar a la consola, per tant, ara tirem aquesta comanda a la consola, la copiem i l'enganxem:

![image](https://github.com/user-attachments/assets/30f386a7-fe6d-44ab-a99c-ded2f64ff8fb)


I així completem i guanyem el joc, aconseguim escapar del bosc i tenim la flag per el challenge de Hack The Box:

![image](https://github.com/user-attachments/assets/0e7cfd46-695f-4bf2-a5a7-ce17f8b6602f)

I ja tenim el challenge Flag Command complert !!


