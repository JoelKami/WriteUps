# Trust

Tags: [[_bruteForce]],[[_sudoersAbusing]]

Creamos nuestros directorios de trabajo.
Verificamos que la máquina esté encendida. Responde, ttl 64, posible máquina Linux.

Hacemos un escaneo con NMAP.
Vemos los puertos 22 y 80 abiertos.
Ahora un escaneo exhaustivo sobre ellos.

Identificamos en codename en launchpad con la versión de SSH y HTTP.

Se trataría de un Debian:  Sid o Mantic.

La versión de SSH no es antigua.
La página web en el puerto 80 es la página por defecto de Apache.

Con whatweb no determinamos mucho, solo la versión del propio Apache.
Wappalizer solo nos reporta Apache y Debian.

Podríamos pensar en virtual hosting, directorios, recursos, etc., debemos fuzzear con NMAP de momento.

Bueno fuzzeemos con otra utilidad más robusta.

De momento no hemos encontrado nada interesante, podríamos fuzzear por dominios.
No hemos encontrado mucho.

/server-status :    da Forbiden

Podemos pensar en rutas por defecto de apache. Pero nada

Hay que buscar por extensiones.
En .txt nada, pero en .php me encontró un secret.php, uy se empieza a tensar.
En .html encuentra un inden.html que es la misma página default.

El secret.php nos da un usuario, podríamos usarlo para intentar conectarnos por fuerza bruta por SSH.

Haciendo un ataque de fuerza bruta con hydra sobre el SSH, con el usuario mario y el rockyou.txt
Conseguí las credenciales.

---
Me conecto por SSH.
Empezamos a mirar de todo.

Al hacer 
sudo -l :    proporcionar contraseña, podemos ver que puedo ejecutar vim como root.
Podemos escribir un comando. Como bash -p
En vim
:! /bin/bash -p

Lo que se puede hacer también, es hacer en vim
:! chmod 4755 /bin/bash 
Para después hacer
/bin/bash -p :    y me la lanzo de forma privilegiada.

Más sencillo, ejecutamos vim como sudo, y nos spawnearmos una bash.
sudo vim -c '!bash'

Más sencillo,
sudo vim :    escribimos :shell para spawnearnos una shell.



