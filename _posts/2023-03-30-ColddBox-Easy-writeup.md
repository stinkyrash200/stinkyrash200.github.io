---
layout: post
title: ColddBox Easy Writeup
subtitle: ColddBox Easy Writeup
cover-img: /assets/img/basic-pentesting-writeup/portada1.png
thumbnail-img: /assets/img/basic-pentesting-writeup/portada1.png
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

```
wpscan --url http://{IP} -U c0ldd,hugo,philip -P /usr/share/wordlists/rockyou.txt
```

Encontramos la contraseña del usuario c0ldd, así que abortamos el ataque con *control c*. Ahora vamos a iniciar sesión en Wordpress:




