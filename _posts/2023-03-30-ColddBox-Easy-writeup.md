---
layout: post
title: ColddBox Easy Writeup
subtitle: ColddBox Easy Writeup
cover-img: /assets/img/colddbox-easy-writeup/portada1.png
thumbnail-img: /assets/img/colddbox-easy-writeup/portada1.png
share-img: /assets/img/path.jpg
tags: [linux, fácil, tryhackme, writeup]
excerpt: "En este post vamos a resolver la máquina Basic Pentesting de la plataforma tryhackme. Esta es una máquina de dificultad fácil con bastante enumeración. Espero que la disfrutéis."
---

Hecho por boyras200
<br>
<br>

# En este artículo vamos a resolver la máquina ColddBox de la plataforma tryhackme. Esta es una máquina de dificultad fácil. Vamos con su resolución.

<br>
<br>
<p align="center">
     <img src="/assets/img/colddbox-easy-writeup/icono.png">
</p>

<center> ColddBox Easy </center>

<br>

<br>
Empezamos haciendo un escaneo de puertos:

![](/assets/img/colddbox-easy-writeup/nmap.png)

```
sudo nmap {IP} -sC -sV -sS -A -O
```

Solamente encontramos un servicio web, vamos a inspeccionarlo:

![](/assets/img/colddbox-easy-writeup/web.png)

{: .box-note}
**Nota:** WordPress es un sistema de gestión de contenidos que sirve como un software libre que los usuarios pueden utilizar como herramienta para la creación de sitios web de forma básica o profesional.

Vemos que la página utiliza Wordpress, así que vamos a utilizar la herramienta ***wpscan*** para analizar el sitio en busca de vulnerabilidades:

![](/assets/img/colddbox-easy-writeup/wpscan.png)


```
gobuster -u http://{IP} -w /usr/share/wordlists/dirb/common.txt dir
```


Así de primeras no encuentra nada, vamos a hacer un ***wpscan*** -h a ver que opciones podemos poner:

![](/assets/img/colddbox-easy-writeup/wpscan2.png)

Encontramos tres opciones bastante interesantes, vamos a utilizarlas:

![](/assets/img/colddbox-easy-writeup/wpscan3.png)

![](/assets/img/colddbox-easy-writeup/wpscan4.png)

```
wpscan --url http://{IP} -e vp,vt,u
```

{: .box-note}
**Nota:** WordPress es un sistema de gestión de contenidos que sirve como un software libre que los usuarios pueden utilizar como herramienta para la creación de sitios web de forma básica o profesional.

No encontramos vulnerabilidades, pero encontramos tres usuarios a los que podríamos aplicarles un ataque de fuerza bruta:

![](/assets/img/colddbox-easy-writeup/wpscan5.png)

![](/assets/img/colddbox-easy-writeup/wpscan6.png)

```
wpscan --url http://{IP} -U c0ldd,hugo,philip -P /usr/share/wordlists/rockyou.txt
```

Encontramos la contraseña del usuario c0ldd, <br> así que abortamos el ataque con *control c*. Ahora vamos a iniciar sesión en Wordpress:

![](/assets/img/colddbox-easy-writeup/web2.png)

![](/assets/img/colddbox-easy-writeup/web3.png)

Después de poner las credenciales ya tenemos un panel de usuario:

![](/assets/img/colddbox-easy-writeup/web4.png)

Ahora lo que vamos a hacer es la forma de conseguir acceso a una máquina víctima con una reverse shell. En Wordpress hay una manera muy sencilla de hacerlo una vez estas logueado como administrador. Vamos a hacerlo así:

![](/assets/img/colddbox-easy-writeup/web5.png)

Entramos a "Editor" en "Appearance":

![](/assets/img/colddbox-easy-writeup/web6.png)

Ahora vamos a "404 Templates":

![](/assets/img/colddbox-easy-writeup/web12.png)

Lo siguiente que tenemos que hacer ahora es subir la reverse shell. Vamos a la página de [revshells](https://www.revshells.com/):

![](/assets/img/colddbox-easy-writeup/revshells.png)

Ahora seleccionamos la reverse shell php de PentestMonkey. La copiamos sin los signos "<?php" y "?>" del principio y del final:

![](/assets/img/colddbox-easy-writeup/revshells3.png)

![](/assets/img/colddbox-easy-writeup/revshells2.png)

Volvemos a Wordpress y pegamos al reverse shell en la segunda línea. Le damos a "update file":

![](/assets/img/colddbox-easy-writeup/web11.png)

![](/assets/img/colddbox-easy-writeup/web7.png)

Ahora ya tenemos la reverse shell subida. Lo siguiente es forzar el error 404, este error sucede cuando no encuentra un resultado, por lo que vamos a buscar un directorio al azar que no exista para forzarlo:

![](/assets/img/colddbox-easy-writeup/web8.png)

Nos encontramos con  que no nos da un error 404, sino un "NOT FOUND". Pero si pensamos un poco podemos deducir donde se encuentra este haciendo un ***wpscan***:

![](/assets/img/colddbox-easy-writeup/web9.png)

```
wpscan --url http://{IP}
```

Observamos que en "wp-content" tenemos una carpeta con los temas. Entonces vamos a buscar el error 404 en esta carpeta a ver si tenemos éxito:

![](/assets/img/colddbox-easy-writeup/web10.png)

```
wpscan --url http://{IP}
```

Vemos que si, porque no nos devuelve el mensaje de "NOT FOUND". Ahora nos ponemos a la escucha con nc -lvnp 4444 y volvemos a ejecutar el error 404:

![](/assets/img/colddbox-easy-writeup/shell4.png)

```
nc -lvnp 4444
```

Ya tenemos acceso a la máquina víctima. Con los siguientes comandos hacemos un tratamiento de la tty:

```
script /dev/null -c bash
Control Z
stty raw -echo;fg
reset
xterm
export TERM=xterm
export SHELL=bash
stty rows <FILAS> columns <COLUMNAS>   #Para saber que poner hacer un stty -a
```

Ahora que ya tenemos la shell tratada vamos a buscar una forma de escalar privilegios, ya que somos el usuario www-data y este no suele tener ningún privilegio que nos sirva:

![](/assets/img/colddbox-easy-writeup/shell5.png)

![](/assets/img/colddbox-easy-writeup/shell.png)


```
find / -perm -4000 2>/dev/null
```

Observamos que podemos ejecutar el comando find con permisos de root, vamos a ir a [GFTOBins](https://gtfobins.github.io) para ver como escalar privilegios a root con ese comando: 

![](/assets/img/colddbox-easy-writeup/shell3.png)

![](/assets/img/colddbox-easy-writeup/shell2.png)

```
find . -exec /bin/sh -p \; -quit
```

¡Hemos conseguido usuario root!

# Hemos resuelto la máquina Colddbox. Espero que os haya gustado y hayáis comprendido su resolución. Hasta la próxima!!!
