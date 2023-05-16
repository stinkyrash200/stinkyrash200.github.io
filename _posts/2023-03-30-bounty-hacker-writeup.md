---
layout: post
title: Bounty Hacker Writeup
subtitle: Bounty Hacker writeup
cover-img: /assets/img/bounty-hacker-writeup/portada.png
thumbnail-img: /assets/img/bounty-hacker-writeup/portada.png
tags: [linux, fácil, tryhackme, writeup]
excerpt: "En este post vamos a resolver la máquina Bounty Hacker de la plataforma tryhackme. Esta es una máquina de dificultad fácil bastante sencilla. Espero que la disfrutéis."
---

Hecho por boyras200
<br>
<br>


# En este artículo vamos a realizar la máquina Bounty Hacker de la plataforma tryhackme. Esta es una máquina de dificultad fácil. Vamos con su resolución.

<br>
<br>
<p align="center">
     <img src="/assets/img/bounty-hacker-writeup/icono.png">
</p>

<center> Bounty Hacker </center>

<br>

Empezamos haciendo un escaneo de puertos:

![](/assets/img/bounty-hacker-writeup/nmap.PNG)

```
sudo nmap {IP} -sC -sV -sS -A -O
```

En el puerto 21 hay un servicio FTP. Este servicio tiene la característica de poder conectarnos de forma anónima. Esto lo sabemos debido al mensaje “Anonymous FTP login allowed”. Nos vamos a conectar a FTP con el usuario anonymous y sin contraseña:

![](/assets/img/bounty-hacker-writeup/ftp.png)

```
ftp {IP}
```

Como no me conectaba desde mí máquina de atacante, utilicé la attackbox que nos proporciona tryhackme. Vemos que hay dos archivos, locks.txt y task.txt , nos los vamos a pasar a nuestra attackbox:

![](/assets/img/bounty-hacker-writeup/ftp2.png)

```
mget *
```

Ahora que ya tenemos los archivos de FTP en nuestra attackbox, estos los voy a pasar a mi máquina de atacante con los siguientes comandos:

```bash
python3 -m http.server                    #En nuestra attackbox
wget http://{IP-Attackbox}:8000/task.txt  #En nuestra máquina de atacante
wget http://{IP-Attackbox}:8000/locks.txt #En nuestra máquina de atacante
```

Seguidamente vamos a ver su contenido:

![](/assets/img/bounty-hacker-writeup/ftp3.PNG)

![](/assets/img/bounty-hacker-writeup/ftp4.png)

Parece que el archivo locks.txt es una lista de usuarios o contraseñas, nos podría servir para un ataque de fuerza bruta. El archivo task.txt es simplemente un archivo que contiene tareas, pero ya sabemos quien lo escribió. Ahora vamos a inspeccionar la página web:

![](/assets/img/bounty-hacker-writeup/web.png)

No encontramos nada interesante en la web, vamos a mirar el código fuente a ver si encontramos algo:

![](/assets/img/bounty-hacker-writeup/web2.png)

Tampoco encontramos nada útil, vamos a mirar las preguntas de tryhackme a ver si nos dan alguna pista:

![](/assets/img/bounty-hacker-writeup/web3.png)

Nos pregunta a qué servicio podemos aplicar fuerza bruta, así que vamos a intentar aplicar un ataque de fuerza bruta al servicio SSH. Para esto vamos a utilizar como usuario el único nombre de usuario que hemos conseguido, lin, y como diccionario usamos el archivo locks.txt. Vamos con el ataque:

![](/assets/img/bounty-hacker-writeup/hydra.png)

![](/assets/img/bounty-hacker-writeup/hydra2.png)

```
hydra -l lin -P {Ruta-De-Locks.txt} -f -vV IP ssh
```

Hemos obtenido la contraseña de lin, ahora nos vamos a conectar por SSH:

![](/assets/img/bounty-hacker-writeup/ssh.png)

Ya tenemos la flag del user, ahora vamos a buscar una forma de escalar privilegios:

![](/assets/img/bounty-hacker-writeup/ssh2.png)

Vemos que podemos ejecutar el comando tar como usuario root, así que vamos a la web de GFTOBins a buscar como podemos escalar privilegios con el comando tar y encontramos esto:

![](/assets/img/bounty-hacker-writeup/ssh3.png)

Lo ejecutamos y ya tenemos la flag de root:

![](/assets/img/bounty-hacker-writeup/ssh4.png)

```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

# Hemos completado la máquina Bounty Hacker de la plataforma tryhackme. Espero que os haya gustado y hayáis comprendido su resolución. Hasta la próxima!!!

![](/assets/img/bounty-hacker-writeup/portada.png)
