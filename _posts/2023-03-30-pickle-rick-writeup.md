---
layout: post
title: Pickle Rick Writeup
subtitle: Pickle Rick writeup
cover-img: /assets/img/pickle-rick-writeup/portada.PNG
thumbnail-img: /assets/img/pickle-rick-writeup/portada.PNG
tags: [linux, fácil, tryhackme, writeup]
excerpt: "En este post vamos a resolver la máquina Pickle de la plataforma tryhackme. Esta es una máquina de dificultad fácil bastante sencilla. Está ambientada en la serie Rick y Morty Espero que la disfrutéis."
---

Hecho por boyras200
<br>
<br>


# En este artículo vamos a resolver la máquina Pickle Rick de la plataforma tryhackme. Esta es una máquina de dificultad fácil. Vamos con su resolución.

<br>
<br>
<p align="center">
     <img src="/assets/img/pickle-rick-writeup/miniatura.PNG">
</p>

<center> Pickle Rick </center>

<br>

Empezamos haciendo un escaneo de puertos:

![](/assets/img/pickle-rick-writeup/nmap.PNG)

```
sudo nmap {IP} -sC -sV -sS -A -O
```

Vemos que la máquina contiene un servicio SSH y un servicio web. Vamos a inspeccionar la web:

![](/assets/img/pickle-rick-writeup/web.PNG)

Rick nos dice que debemos iniciar sesión en su ordenador para poder convertirlo en humano. Pero hay un problema, no tenemos su usuario ni su contraseña. Vamos a mirar en el código fuente a ver si encontramos algo:

![](/assets/img/pickle-rick-writeup/web2.PNG)

Hemos encontrado una nota que contiene el nombre de usuario de Rick. Ahora vamos a intentar descubrir su contraseña. La mejor opción ahora mismo es hacer enumeración web a ver que encontramos:

![](/assets/img/pickle-rick-writeup/web3.PNG)

```
gobuster -u http://{IP} -w /usr/share/wordlists/dir/common.txt dir
```

Vamos a inspeccionar la carpeta "assets":

![](/assets/img/pickle-rick-writeup/web4.PNG)


{: .box-note}
**Nota:** Bootstrap es un conjunto de herramientas de código abierto muy popular entre los expertos en desarrollo web, ya que está ideado para un desarrollo responsive. Es decir, gracias a Bootstrap, los desarrolladores web pueden crear páginas web visibles en diferentes formatos y pantallas.

Encontramos dos bootstrap y varias imágenes que no nos aportan ninguna información. Vamos a revisar el robots.txt:

![](/assets/img/pickle-rick-writeup/web5.PNG)

(Wubbalubbadubdub es una frase que el personaje Rick de Rick y Morty suele decir en la serie). El contenido del archivo no parece tener ninguna utilidad. No nos quedan archivos por inspeccionar, así que vamos a hacer una enumeración web más intensa. Esto lo haremos  buscando archivos con extensiones html, php y txt:

![](/assets/img/pickle-rick-writeup/web6.PNG)

```
gobuster -u http://{IP} -w /usr/share/wordlists/dir/common.txt dir -x php,txt,html
```

Hemos encontrado un panel de inicio de sesión (login.php). Vamos a inspeccionarlo:

![](/assets/img/pickle-rick-writeup/web7.PNG)

Vemos que es todo normal. No encontramos nada en el código fuente. Como no funciona nada de lo anterior vamos a recapitular. Tenemos el usuario, ¿Qué podría ser la contraseña? Solo nos queda probar el contenido del robots.txt como contraseña. Vemos que esto funciona:

![](/assets/img/pickle-rick-writeup/web8.PNG)

Nos encontramos con un panel de comandos.Vamos a probar a conectarnos por SSH con estas credenciales:

![](/assets/img/pickle-rick-writeup/web19.PNG)

```
ssh R1ckRul3s@{IP}
```

Vemos que no funciona, vamos a inspeccionar el panel de comandos:

![](/assets/img/pickle-rick-writeup/web9.PNG)

```
ls -al
```

Con un ***ls -al*** vemos unos cuantos archivos, algunos los pudimos listar con ***gobuster***, esto significa que lo que estamos viendo son los archivos de la raíz de la web. Vamos a ver el archivo "SuperSecretPickIngredient" con el comando ***cat***:

![](/assets/img/pickle-rick-writeup/web1.PNG)

```
cat SuperSecretPickIngredient.txt
```

Vemos que el comando está deshabilitado, así que vamos a ver el contenido del archivo a través de la url:

![](/assets/img/pickle-rick-writeup/web11.PNG)

Ya tenemos el primer ingrediente. Podríamos seguir investigando a través del panel de comandos, pero al ver que tiene restricciones, lo más recomendable es hacer es una reverse shell a nuestra máquina de atacante.

{: .box-note}
**Nota:** Una reverse shell hace que el ordenador vulnerado se conecte al del atacante. Las shells inversas o reverse shells son preferibles a la hora de hacer ciberataques, ya que permiten evadir la presencia de firewalls y otras medidas de protección.

En la página web revshells tenemos reverse shells en la mayoría de lenguajes de programación:

![](/assets/img/pickle-rick-writeup/web12.PNG)

Introducimos nuestra IP y el puerto 4444, ahora copiamos el código y lo ponemos en la línea de comandos, pero primero nos ponemos a la escucha  con el comando ***nc -lvnp 4444***:

![](/assets/img/pickle-rick-writeup/web13.PNG)

```
nc -lvnp 4444
```

Ejecutamos el comando y no pasa nada, puede que este bloqueado o simplemente no funcione. Entonces vamos a usar mi reverse shell favorita, la cual no se suele regular su uso, la reverse shell en perl:

![](/assets/img/pickle-rick-writeup/web14.PNG)

Ejecutamos el comando y ya tenemos acceso a la máquina víctima:

![](/assets/img/pickle-rick-writeup/web15.PNG)

```
nc -lvnp 4444
```
Hacemos un tratamiento de la tty para poder ejecutar comandos en la máquina víctima con más comodidad. Los comandos son los siguientes:

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

Una vez tratada la tty vamos a buscar el segundo ingrediente:

![](/assets/img/pickle-rick-writeup/web16.PNG)

Ya tenemos el segundo ingrediente, ahora vamos a ejecutar un ***sudo -l*** para ver los permisos que tenemos en esta máquina:

![](/assets/img/pickle-rick-writeup/web17.PNG)

```
sudo -l
```

Vemos que podemos escalar privilegos a usuario root sin contraseña, así que hacemos un ***sudo su*** para ponernos como root:

![](/assets/img/pickle-rick-writeup/web18.PNG)

```
sudo su
```

En el directorio de root se encuentra el ingrediente número 3.


# Hemos resuelto la máquina Pickle Rick de la plataforma tryhackme. Espero que os haya gustado y hayáis comprendido su resolución. Hasta la próxima!!!

<p align="center">
     <img src="/assets/img/pickle-rick-writeup/portada.PNG">
</p>