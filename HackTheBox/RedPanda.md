# RedPanda

Tags: [[_ssti]],[[_xxe]],[[_webExploitation]],[[_scripting]]

## Primeros pasos
(Reconocimiento).

<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Activamos</span> <span style="color:#ff2dc0">VPN</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Linux</span>.

<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>. <span style="color:#379075">Otro escaneo de puertos comunes por posibles falsos negativos</span>.
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Focal</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH.
<span style="color:#ff669c">8080</span> - HTTP.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> válidas.


<span style="color:#ff669c">8080</span>.
<span style="color:#ff2dc0">whatweb</span>. "<span style="color:#ecacb6">Made With Spring Boot</span>", <span style="color:#07b4f2">lo cuál tiene que ver</span> con <span style="color:#ff2dc0">Java</span>, poco más.

<span style="color:#ff2dc0">Desde la web</span>.
"<span style="color:#ecacb6">RED PANDA SEARCH</span>". Hay como un <span style="color:#07b4f2">buscador</span>, <span style="color:#07b4f2">supongo</span> que <span style="color:#07b4f2">de pandas rojos</span>.
Tengamos en <span style="color:#07b4f2">cuenta</span> un <span style="color:#ff2dc0">SSTI</span> de <span style="color:#ff2dc0">Java</span>.
<span style="color:#07b4f2">Antes</span> hagamos <span style="color:#ff2dc0">fuzzing</span>.

<span style="color:#ff2dc0">Fuzzear URIs</span>.
<span style="color:#ecacb6">/stats</span> :    vemos <span style="color:#07b4f2">usuarios</span> con <span style="color:#07b4f2">links</span>, hay <span style="color:#ff2dc0">rutas</span> de <span style="color:#ecacb6">/img</span> con <span style="color:#07b4f2">imágenes</span> y <span style="color:#07b4f2">nombres</span>.<span style="color:#07b4f2"> Puedo exportar </span>una <span style="color:#07b4f2">tabla</span> en formato <span style="color:#ff2dc0">XML</span>, viene la <span style="color:#07b4f2">misma información</span> pero <span style="color:#07b4f2">en</span> <span style="color:#ff2dc0">XML</span>.

En el <span style="color:#07b4f2">buscador</span> podemos <span style="color:#07b4f2">buscar</span> por <span style="color:#07b4f2">estos nombres </span>y nos <span style="color:#07b4f2">aparecen</span>.

<span style="color:#07b4f2">Investigando</span> por <span style="color:#ff2dc0">SSTI</span> de <span style="color:#ff2dc0">Java</span>, acudimos a https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/Java.md para ver ejemplos.
Al <span style="color:#07b4f2">momento</span> de <span style="color:#07b4f2">añadir</span> un <span style="color:#ff2dc0">payload</span> hay <span style="color:#07b4f2">mensajes</span> de "<span style="color:#ecacb6">banned characters</span>".

Haciendo <span style="color:#07b4f2">pruebas</span> en el <span style="color:#07b4f2">buscador</span>.
<span style="color:#ff2dc0">Bad Characters </span>-> <span style="color:#ecacb6"><span style="color:#ecacb6">$</span></span>, <span style="color:#ecacb6">%</span>, <span style="color:#ecacb6">~</span>
Pero podemos utilizar C.
<span style="color:#ecacb6">*{7*7}</span> :    nos <span style="color:#07b4f2">muestra 49</span>.
<span style="color:#ecacb6">*{T(java.lang.Runtime).getRuntime().exec('ping -c 1 10.10.14.15')}</span> :    <span style="color:#07b4f2">recibo</span> los <span style="color:#ff2dc0">ping</span>. <span style="color:#07b4f2">Funciona</span> porque <span style="color:#07b4f2">es para</span> <span style="color:#ff2dc0">Spring</span>.
Sin embargo, <span style="color:#07b4f2">no vemos</span> el <span style="color:#ff2dc0">output</span> del <span style="color:#ff2dc0">comando</span>, <span style="color:#07b4f2">además</span> de que <span style="color:#07b4f2">no me permite entablar</span> una <span style="color:#ff2dc0">conexión</span>.

Probando, este <span style="color:#ff2dc0">payload</span> (<span style="color:#379075">viene en la página</span>) <span style="color:#07b4f2">sí</span> me <span style="color:#07b4f2">muestra</span> el <span style="color:#07b4f2">contenido</span> del <span style="color:#ecacb6">/etc/passwd</span> 
![[Pasted image 20241211214023.png]]
Vemos que <span style="color:#07b4f2">usa decimales </span>para <span style="color:#07b4f2">representar</span> las <span style="color:#07b4f2">letras</span> del <span style="color:#ff2dc0">comando</span> a <span style="color:#ff2dc0">ejecutar</span>, en este caso <span style="color:#88c425">cat /etc/passwd</span>

Hagamos un <span style="color:#ff2dc0">script</span> que me <span style="color:#07b4f2">automatice</span> la <span style="color:#ff2dc0">ejecución</span> de <span style="color:#ff2dc0">comandos</span>.
![[Pasted image 20241211234455.png]]

Hay que <span style="color:#07b4f2">tener en cuenta</span> la <span style="color:#07b4f2">estructura</span> del <span style="color:#ff2dc0">payload</span>.
<span style="color:#07b4f2">Le pasamos</span> un <span style="color:#ff2dc0">comando</span> y nos <span style="color:#07b4f2">automatiza</span> la <span style="color:#ff2dc0">petición</span>.

<span style="color:#07b4f2">Continuamos</span> con el <span style="color:#07b4f2">conflicto</span> de las <span style="color:#ff2dc0">reglas de firewall</span> que <span style="color:#07b4f2">no permiten</span> <span style="color:#ff2dc0">conexiones</span>.
Así que de momento <span style="color:#ff2dc0">enumeremos</span> <span style="color:#07b4f2">bien</span> la <span style="color:#ff2dc0">máquina</span>.

En <span style="color:#ecacb6">/credits </span>están los <span style="color:#ff2dc0">archivos XML</span> que <span style="color:#07b4f2">veíamos</span> de <span style="color:#07b4f2">antes</span>.
En <span style="color:#ecacb6">/opt/panda_search</span> tenemos <span style="color:#07b4f2">cosas</span>.
<span style="color:#ecacb6">/opt/panda_search/redpanda.log</span> :    se ven como <span style="color:#07b4f2">mis</span> <span style="color:#ff2dc0">solicitudes HTTP</span> que <span style="color:#07b4f2">hago</span> a la <span style="color:#ff2dc0">web</span>.

Si <span style="color:#07b4f2">hacemos</span> <span style="color:#ecacb6">grep -r woodenk /opt/panda_search/</span> :    vemos que <span style="color:#07b4f2">hace match en imágenes</span>, esto<span style="color:#07b4f2"> podría ser</span> porque en los <span style="color:#ff2dc0">metadatos</span> de la imagen puede <span style="color:#07b4f2">haber</span> un <span style="color:#07b4f2">campo</span> que <span style="color:#07b4f2">contemple</span> el <span style="color:#ecacb6">usuario</span>. Y lo hay, si hacemos
<span style="color:#88c425">exiftool grep.jpg</span> :    <span style="color:#07b4f2">descargamos</span> la <span style="color:#ff2dc0">imagen</span>.
Hay un <span style="color:#07b4f2">campo</span> <span style="color:#ecacb6">Artist</span>.

<span style="color:#07b4f2">Fuera de</span> las <span style="color:#07b4f2">imágenes</span>, vemos un <span style="color:#ff2dc0">archivo</span> en <span style="color:#ecacb6">/opt/panda_search/src/main/java/com/panda_search/htb/panda_search/MainController.java</span>Donde <span style="color:#07b4f2">hay</span> una <span style="color:#ff2dc0">conexión</span> a una <span style="color:#ff2dc0">base de datos</span>, y se puede ver <span style="color:#07b4f2">como una</span> <span style="color:#ecacb6">contraseña</span> (<span style="color:#379075">ya la habíamos visto antes al enumerar de todo, pero se nos pasó probarla</span>).
<span style="color:#ecacb6">woodenk:RedPandazRule</span>

Nos <span style="color:#ff2dc0">autenticamos</span> <span style="color:#07b4f2">por</span> <span style="color:#ff2dc0">SSH</span> y pa deeentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Focal.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">woodenk</span> -> como el que estamos.

Intentamos <span style="color:#ff2dc0">conectarnos</span> al <span style="color:#ff2dc0">mysql</span>. Dentro <span style="color:#ff2dc0">enumeramos</span> <span style="color:#07b4f2">todo</span> pero <span style="color:#07b4f2">no</span> hay <span style="color:#07b4f2">mucho</span>.

Nos montamos un <span style="color:#ff2dc0">procmon</span> en <span style="color:#ecacb6">/tmp</span> para <span style="color:#07b4f2">descubrir</span> <span style="color:#ff2dc0">tareas</span> a <span style="color:#ff2dc0">intervalos regulares de tiempo</span>.
Encontramos
<span style="color:#ecacb6">root -> /bin/sh -c /root/run_credits.sh</span>
Y <span style="color:#07b4f2">esperando más</span>, <span style="color:#07b4f2">vemos</span>
<span style="color:#ecacb6">root ->  /bin/sh -c sudo -u woodenk /opt/cleanup.sh</span> 
<span style="color:#ecacb6">woodenk</span> ejecuta una <span style="color:#07b4f2">búsqueda</span> de <span style="color:#07b4f2">nombres</span> de <span style="color:#ff2dc0">archivos</span> que <span style="color:#07b4f2">terminen</span> en varias <span style="color:#ff2dc0">extensiones</span> para <span style="color:#07b4f2">borrarlos</span>.

En <span style="color:#ecacb6">/opt/panda_search </span>está el <span style="color:#ecacb6">redpanda.log</span> y su <span style="color:#ff2dc0">propietario</span> es <span style="color:#ecacb6">root</span>, así que tratemos de <span style="color:#07b4f2">entender cómo lo crea</span> y <span style="color:#07b4f2">cómo</span> está todo <span style="color:#07b4f2">configurado</span>.
<span style="color:#88c425">grep -r "redpanda.log" 2>/dev/null</span> :    vamos viendo <span style="color:#ff2dc0">archivos</span> <span style="color:#07b4f2">que lo crean</span>.
<span style="color:#07b4f2">Vemos</span> en <span style="color:#ecacb6">/opt</span>
<span style="color:#ecacb6">./credit-score/LogParser/final/src/main/java/com/logparser/App.java</span> :    este es el <span style="color:#ff2dc0">programa</span> abre el <span style="color:#ecacb6">redpanda.log</span> y <span style="color:#07b4f2">filtra</span> los <span style="color:#07b4f2">valores</span> y <span style="color:#07b4f2">hace cosas</span> con ellos.
Hace<span style="color:#07b4f2"> cosas cuando</span> se <span style="color:#07b4f2">trata</span> de una <span style="color:#07b4f2">imagen</span>.
<span style="color:#ecacb6">./panda_search/src/main/java/com/panda_search/htb/panda_search/RequestInterceptor.java </span> :    donde se ve cómo <span style="color:#07b4f2">obtiene mis datos</span> y los <span style="color:#07b4f2">separa</span> por<span style="color:#ecacb6"> ||</span> para <span style="color:#07b4f2">crear</span> el <span style="color:#ecacb6">redpanda.log</span>

<span style="color:#bef202">Proceder</span>.
![[Pasted image 20241212114513.png]]

<span style="color:#07b4f2">Alteramos</span> la <span style="color:#07b4f2">imagen</span>.
<span style="color:#88c425">mv greg.jpg pwned.jpg</span>
<span style="color:#88c425">exiftool -Artist=../../../../../../../tmp/pwned pwned.jpg </span> 
Y la <span style="color:#ff2dc0">subimos</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina víctima</span> en <span style="color:#ecacb6">/tmp</span>

En <span style="color:#ecacb6">/tmp</span>
<span style="color:#ff2dc0">Creamos</span> un <span style="color:#ff2dc0">XML</span> <span style="color:#07b4f2">con</span> un <span style="color:#ff2dc0">XXE</span> y <span style="color:#07b4f2">lo metemos ahí</span>.
<span style="color:#88c425">chmod 777 pwned_creds.xml</span> :    para que <span style="color:#07b4f2">los demás puedan</span> <span style="color:#ff2dc0">escribir</span>.
![[Pasted image 20241212112749.png]]

Hacemos una <span style="color:#ff2dc0">petición</span> a la <span style="color:#ff2dc0">web</span> <span style="color:#07b4f2">alterando</span> nuestro <span style="color:#ecacb6">User-Agent</span>.
<span style="color:#88c425">curl -s -X GET "http://10.10.11.170:8080/" -A "test||/../../../../../../../../../../tmp/pwned.jpg"</span> 

Sería cuestión de <span style="color:#07b4f2">esperar</span> a que todo <span style="color:#07b4f2">el flujo se haga</span>, <span style="color:#07b4f2">obtenemos</span> la <span style="color:#ecacb6">id_rsa</span> de <span style="color:#ecacb6">root</span> <span style="color:#07b4f2">en</span> <span style="color:#ecacb6">pwned_creds.xml</span> para <span style="color:#07b4f2">usarla</span> como <span style="color:#ff2dc0">fichero de identidad</span> y <span style="color:#ff2dc0">conectarnos</span> a la máquina víctima y pa deeentro.
Fin.
