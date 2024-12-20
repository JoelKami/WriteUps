# PowerGrid 1.0.1

Tags: [[_scripting]],[[_bruteForce]],[[_webExploitation]],[[_cracking]],[[_pivoting]]

Primero le acomodamos bien para que agarre una IP (poner modo Bridged).

Organizamos TMUX.
Hacemos un escaneo en la red para determinar la IP de la máquina.
Mediante el OUI reconocemos la máquina que vamos a atacar.

Creamos un directorio como la IP o nombre de la máquina.
Ponemos la IP de la máquina en un lugar visible.
Creamos los directorios de trabajo.
Le hacemos un ping a la máquina.
Posible máquina Linux.

Empezamos el escaneo con NMAP.

En base a las versiones, determinarnos el codename, se trata supuestamente de un Debian Buster.

Puertos abiertos.
80 - HTTP
143 - IMAP (correo, de mensajes)
993 - SSL/IMAP

Analizaremos un poco la web.

80.
Whatweb,  no dice mucho.
<span style="color:#88c425">nmap --script=http-enum -p80 192.168.0.14</span> :     solo muestra /images/ y tiene Directory Listing.

<span style="color:#ff2dc0">Desde la web</span>.
Vemos <span style="color:#07b4f2">usuarios</span>
<span style="color:#ecacb6">deez1</span>
<span style="color:#ecacb6">p48</span>
<span style="color:#ecacb6">all2</span>

Bueno, toca fuzzear uris
/zmail :    es para autenticarse. Parece que es de mail o algo de eso.


Fuerza Bruta en panel de Autenticación.
<span style="color:#ecacb6">1.-</span> Hydra.
<span style="color:#ecacb6">2.-</span> python:
Interceptar antes en BurpSuite, para ver cómo se envía.
Las <span style="color:#ff2dc0">credenciales</span> <span style="color:#07b4f2">van</span> en la <span style="color:#ff2dc0">cabecera</span> <span style="color:#ecacb6">Authorization</span> en <span style="color:#ff2dc0">base64</span> (<span style="color:#379075">admin:admin</span>).
Cuando las credenciales son erróneas, el código de estado es 401.
<span style="color:#ff2dc0">Script</span>
![[Pasted image 20240530022925.png]]

Con barras de Progreso:
No se ven las librerías, pero son las misma de antes.
![[Pasted image 20240530030024.png]]

Nota. Meter Hilos de forma rápida:
<span style="color:#ecacb6">import concurrent.futures</span>
![[Pasted image 20240530030404.png]]


Pudimos entrar.
Hay otro panel, pero se reutilizan credenciales.
Es un roundcube, es como de correos.
Tiene vulnerabilidades.
Estamos ante un RoundCube 1.2.2

Tenemos un mensaje de root.
Ojo, un mensaje PGP encriptado.
La nota dice que hay un servidor de respaldo en la misma red.

Provechemos el RCE de searchsploit para la versión en la que estamos.
Vamos a <span style="color:#ecacb6">Compose</span> (<span style="color:#379075">crear mensaje</span>) -> Interceptamos con <span style="color:#ff2dc0">BurpSuite</span> -> <span style="color:#07b4f2">Rellenamos</span> los <span style="color:#ff2dc0">campos</span> del <span style="color:#07b4f2">mensaje</span> (<span style="color:#ecacb6">From</span> (<span style="color:#379075">guardar el archivo</span>), <span style="color:#ecacb6">Subject</span> (<span style="color:#379075">contenido PHP</span>))

Ya con todo configurado podemos hacer de todo, incluso vemos que hay dos segmentos de red porque hay dos IP´s . Pero ganamos mejor, acceso al sistema.


143.


993.


Una vez dentro.
Como ya habíamos visto, hacemos 
<span style="color:#88c425">ip a</span>  :    y vemos que puede haber Docker de por medio.

Se reutilizan las credenciales para el usuario p48, parece que vamos a hacer User Pivoting porque están los otros dos usuarios.

Pero en el /home/p48
Tenemos el privkey.gpg, que es un <span style="color:#ff2dc0">PGP Private Key Block</span>.
Lo que parece ser la clave privada que necesitábamos de antes para descifrar la otra.

Analizamos nuevamente el correo de root/admin.
Buscamos <span style="color:#ff2dc0">PGP Decrypt</span> en Internet.
https://8gwifi.org/pgpencdec.jsp :    en <span style="color:#ff2dc0">Decrypt</span>.
Copiamos y <span style="color:#07b4f2">pegamos</span> el<span style="color:#ff2dc0"> PGP Message</span> y <span style="color:#ff2dc0">PGP Private Key Block</span>.
Nos <span style="color:#07b4f2">pide</span> una <span style="color:#ff2dc0">Passphrase</span>, proporcionamos <span style="color:#ecacb6">electrico</span> nuevamente.
Nos da una <span style="color:#ff2dc0">clave privada SSH</span>.

Pero, ¿<span style="color:#07b4f2">para qué</span>?, si <span style="color:#07b4f2">no</span> está <span style="color:#ff2dc0">abierto SSH</span>.
Pues como habíamos <span style="color:#07b4f2">visto</span>, habla de que <span style="color:#07b4f2">se configuró</span> un <span style="color:#ff2dc0">server</span> de <span style="color:#ff2dc0">backup</span> en la <span style="color:#07b4f2">misma</span> <span style="color:#ff2dc0">red</span>.
En <span style="color:#ff2dc0">Docker</span>, <span style="color:#07b4f2">pueden</span> haber más <span style="color:#ff2dc0">máquinas desplegadas</span>, <span style="color:#07b4f2">tenemos</span> la <span style="color:#ecacb6">172.17.0.1</span>, hacemos <span style="color:#ff2dc0">ping</span> a la <span style="color:#ecacb6">172.17.0.2</span> y responde.
<span style="color:#88c425">ssh -i id_rsa p48@172.17.0.2</span> :    nos <span style="color:#07b4f2">deja</span> entrar.

Podemos <span style="color:#ff2dc0">ejecutar</span> <span style="color:#ecacb6">/usr/bin/rsync</span> como <span style="color:#ff2dc0">root</span>. En <span style="color:#ff2dc0">Gtfobins</span> vemos <span style="color:#07b4f2">cómo</span> <span style="color:#ff2dc0">abusarlo</span>.
Ahora <span style="color:#07b4f2">como</span> <span style="color:#ff2dc0">root</span>, tratemos de <span style="color:#ff2dc0">conectarnos</span> a la <span style="color:#ecacb6">172.17.0.1</span> (<span style="color:#379075">máquina real</span>), ya que <span style="color:#07b4f2">podemos</span> ser un <span style="color:#ecacb6">usuario</span> <span style="color:#07b4f2">autorizado</span> para conectarnos (la <span style="color:#ff2dc0">pub_rsa</span> (<span style="color:#379075">root del contenedor</span>) estará <span style="color:#07b4f2">metida</span> en el <span style="color:#ecacb6">.ssh</span> de <span style="color:#ff2dc0">root</span>), y pa deeentro.
FIN.
