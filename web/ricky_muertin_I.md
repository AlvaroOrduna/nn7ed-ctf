# RickyMuertin I

En esta prueba web de 50 puntos nos dan este simple enunciado y un enlace:

    Space System for Research and Fun! Come and visit your favourite planet ;)

    http://nn7ed.ka0labs.org:6789/

## Introducción

Visitando el [enlace](http://nn7ed.ka0labs.org:6789/) del enunciado, podemos ver un simple formulario con un `input`, un botón `Visit!` y una imagen de nuestros queridos Ricky y Morty.

![Formulario RickyMuertin I](../assets/ricky_muertin_I_1.jpeg)

Haciendo unas pequeñas pruebas, se puede averiguar el funcionamiento de la web. Si introducimos una `url` válida (ej: `www.example.com`), podemos ver como la web se recarga con un texto un tanto extraño.

![Resultado RickyMuertin I](../assets/ricky_muertin_I_2.jpeg)

El texto resultante no es más que el código fuente de la página introducida en `base64` y con una fuente llamada `wubbalubba`.

```html
<html>

<head>
 <title>RickyMuertin</title>
 <style>
  @font-face {
      font-family:'wubbalubba';
      src: url('ANGERTH.ttf') format('truetype');
  }
  input[type="text"] {
      width:70%;
  }
  #container {
      width:80%;
      padding:20px;
      margin:40px;
      background-color:#fff;
  }
  body {
      background-color:#BF6BFF;
  }
 </style>
</head>

<body>
  <center>
    <div id='container'>
      <h1>RickyMuertin</h1><br><br>
      <div style="font-family:'wubbalubba';width:60%;border:1px solid black;padding:10px 10px;word-wrap:break-word;">
        <!-- CÓDIGO FUENTE DE LA PÁGINA DEL FORMULARIO CODIFICADO EN BASE64 -->
      </div>
    </div>
  </center>
</body>

</html>
```

Para ver el resutlado _en claro_ basta con abrir el código fuente de la página y [decodificar](https://www.base64decode.org/) el contenido del `div`:

```html
<!doctype html>
<html>

<head>
    <title>Example Domain</title>

    <meta charset="utf-8" />
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <style type="text/css">
    body {
        background-color: #f0f0f2;
        margin: 0;
        padding: 0;
        font-family: "Open Sans", "Helvetica Neue", Helvetica, Arial, sans-serif;

    }
    div {
        width: 600px;
        margin: 5em auto;
        padding: 50px;
        background-color: #fff;
        border-radius: 1em;
    }
    a:link, a:visited {
        color: #38488f;
        text-decoration: none;
    }
    @media (max-width: 700px) {
        body {
            background-color: #fff;
        }
        div {
            width: auto;
            margin: 0 auto;
            border-radius: 0;
            padding: 1em;
        }
    }
    </style>
</head>

<body>
  <div>
    <h1>Example Domain</h1>
    <p>This domain is established to be used for illustrative examples in documents. You may use this
    domain in examples without prior coordination or asking for permission.</p>
    <p><a href="http://www.iana.org/domains/example">More information...</a></p>
  </div>
</body>

</html>
```

## Investigación

Sabiendo el funcionamiento de la web, vamos a averiguar alguna cosa más que pueda orientarnos en la forma de resolver esta prueba.

Lo primero es ver si existe un archivo `robots.txt`. En este caso, su contenido es:

```
Disallow: /.bash_history
```

Esto nos indica que puede existir un archivo llamado `.bash_history`, que como sabemos, contendrá los últimos comandos introducidos por el usuario.

```bash
ls
ls -la
clear
lftp -e 'put /home/morty/flag_1.txt;put /home/morty/muertin.php.bckp; bye' -u rick,8===D localhost
curl localhost:8080/muertin.php
rm -rf /
exit
```

En el contenido podemos ver varias lineas relevantes:

La cuarta línea, `lftp -e 'put /home/morty/flag_1.txt;put /home/morty/muertin.php.bckp; bye' -u rick,8===D localhost`, nos indica la posible existencia de dos ficheros, `flag_1.txt` y `muertin.php.bckp`, en un servidor ftp local.

La quinta, `curl localhost:8080/muertin.php`, nos indica la posible existencia de otro fichero más, `muertin.php`, en un servidor web local.

Sabiendo todo lo que hemos averiguado, el siguiente paso es ver si estos archivos pueden ser accedidos de forma normal. Como se puede comprobar, el puerto 8080 del servidor esta cerrado, por tanto no podemos acceder a `muertin.php`. Además, el servidor ftp tampoco está abierto, al menos en el puerto normalmente utilizado para ello.

Así pues, podemos suponer que estos dos servidores (web y ftp) están configurados para acceder únicamente desde el interior del servidor y no desde el exterior del mismo.

Por último, parece bastante sospochoso que uno de estos ficheros se llame `flag_1.txt`. No se a vosotros, pero a mi me pica mucho la nariz a estas alturas... ;)

## Primer intento

Recapitulando, sabemos que podemos obtener el contenido de una dirección introduciendola en el formulario de la web. Además, existe un archivo altamente sospechoso en un servidor web interno.

Así pues, parece probable que se introducimos la dirección del archivo (`file:///home/morty/flag_1.txt`) en el formulario web, ésta nos devolverá su contenido:

![Resultado RickyMuertin I](../assets/ricky_muertin_I_3.jpeg)

    Vaya Ricky, parece que nos hemos confundido de portal!!!

Evidentemente, este no es el resultado que esperabamos. Parece que la página web ha evitado que intentemos acceder a una archivo.

Realizando una serie de pruebas, podemos ver que el servicio bloquea cualquier dirección con el esquema `file://`. Así, debemos pensar en otra forma de acceder a este archivo, porque, recordemos, nos pica mucho la nariz.

## Segundo y _¿definitivo?_ intento

¿Qué otra forma tenemos de acceder al archivo? Recordemos que existe un servidor web y otro ftp.

Ya que tenemos el usuario y contraseña del servidor ftp local, vamos a tirar por ahí que tiene mejor pinta.

De este modo, probamos con la dirección ftp del fichero: `ftp://rick:8===D@localhost/flag_1.txt`.

![Resultado RickyMuertin I](../assets/ricky_muertin_I_3.jpeg)

    Morty, como te vuelvas a equivocar, nos matamos!!!

Parece que esta vez tampoco será. Probando como en el caso de antes, podemos observar que el esquema `ftp://` no es el problema.

Si seguimos probando, podemos ver que el filtro se aplica cuando la dirección introducida contiene `localhost`.

La pregunta ahora es, ¿cómo podemos introducir una dirección local sin utilizar `localhost`? Aquí es donde viene _IPv6_ al rescate. Como sabemos, en _IPv4_, la dirección interna de un sistema es `localhost`. En _IPv6_, por suerte, también existe esta dirección interna, pero tiene otra pinta: `[::]`.

Sabiendo esto, vamos aprobar la siguiente dirección: `ftp://rick:8===D@[::]/flag_1.txt`.

![Resultado RickyMuertin I](../assets/ricky_muertin_I_4.jpeg)

Esto ya tiene otra pinta...

Vamos, de nuevo, al código fuente de la web y decodificamos el `base64` y _et voilà_, flag al canto:

    nn7ed{g00d_j0b_but_pr1nc3ss_1s_1n_an0th3r_c4stl3!}

## Conclusiones

Lo que hemos visto aquí es un tipo de vulnerabilidad llamada [`SSRF` (_Server Side Request Forgery_)](https://www.owasp.org/index.php/Server_Side_Request_Forgery), haciendo que el propio servidor que aloja la página del enunciado nos devuelva el contenido codificado en `base64` de un fichero presente en un servidor ftp local de la misma máquina.

## Agradecimientos

Por ultimo, agradecer a mis amigos y compañeros [DaniLabs](https://github.com/DaniLabs) e [iechavarri](https://github.com/iechavarri) la ayuda en este, mi primer CTF :smirk:
