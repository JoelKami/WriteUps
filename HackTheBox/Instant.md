# Instant

Tags: [[_analysis]],[[_api]],[[_lfi]],[[_webExploitation]],[[_scripting]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Noble</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 9.6p1</span>
<span style="color:#ff669c">80</span> - HTTP. <span style="color:#379075">Apache 2.4.58</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.

<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#ff2dc0">Redirect</span> a <span style="color:#ecacb6">instant.htb</span>, contemplamos en <span style="color:#ecacb6">/etc/hosts</span>
Es de una <span style="color:#07b4f2">billetera instantánea</span>.
Correo <span style="color:#ecacb6">support@instant.htb</span> 

<span style="color:#ff2dc0">Desde la web</span>. 
Me puedo <span style="color:#07b4f2">descargar</span> una <span style="color:#ff2dc0">APK</span>. La tenemos en cuenta, <span style="color:#07b4f2">de momento sigamos</span> haciendo <span style="color:#ff2dc0">reconocimiento</span>.

<span style="color:#bef202">APK Analisys.</span>
Tenemos <span style="color:#ecacb6">instant.apk</span>
La <span style="color:#ff2dc0">descomprimimos</span> y <span style="color:#07b4f2">buscando en</span> los <span style="color:#ff2dc0">archivos</span>.
Al <span style="color:#07b4f2">hacer</span>
<span style="color:#88c425">grep -r "instant.htb"</span> :    vemos otros <span style="color:#07b4f2">dos</span> <span style="color:#ff2dc0">subominios</span> (<span style="color:#379075">contemplarlos en /etc/hosts</span>) y <span style="color:#07b4f2">en</span> el <span style="color:#ecacb6">AdminActivities</span> <span style="color:#07b4f2">vemos</span> un <span style="color:#ff2dc0">JWT</span>.

<span style="color:#ecacb6">mywalletv1.instant.htb</span>
<span style="color:#ecacb6">swagger-ui.instant.htb</span>
"<span style="color:#ecacb6">Authorization</span>", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwicm9sZSI6IkFkbWluIiwid2FsSWQiOiJmMGVjYTZlNS03ODNhLTQ3MWQtOWQ4Zi0wMTYyY2JjOTAwZGIiLCJleHAiOjMzMjU5MzAzNjU2fQ.v0qyyAqDSgyoNFHU7MgRQcDA0Bw99_8AEXKGtWZ6rYA"

<span style="color:#07b4f2">En</span> el <span style="color:#ff2dc0">API</span> (<span style="color:#ff2dc0">swagger</span>) podemos hacer <span style="color:#ff2dc0">peticiones</span> al <span style="color:#ff2dc0">API</span>, no obstante, <span style="color:#07b4f2">hay</span> unos <span style="color:#07b4f2">bloqueados</span>, pero podríamos <span style="color:#07b4f2">usar</span> el <span style="color:#ff2dc0">JWT</span> para <span style="color:#07b4f2">realizarlas</span>, lo mandamos <span style="color:#07b4f2">en</span> la <span style="color:#ff2dc0">cabecera</span> <span style="color:#ecacb6">Authorization</span>.
<span style="color:#07b4f2">Funciona</span>, ahora <span style="color:#ff2dc0">enumeremos</span> todo.

<span style="color:#ff2dc0">LFI</span> en <span style="color:#ff2dc0">endpoint</span>
<span style="color:#ecacb6">/api/v1/admin/read/log?log_file_name=../../../etc/passwd</span>

<span style="color:#ff2dc0">Apuntamos</span> a una <span style="color:#ecacb6">id_rsa</span> en
<span style="color:#ecacb6">/home/shirohige/.ssh/id_rsa</span> :    la <span style="color:#07b4f2">obtenemos</span>.
La <span style="color:#07b4f2">usamos</span> como <span style="color:#ff2dc0">fichero</span> de <span style="color:#ff2dc0">identidad</span> y pa deentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Noble.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">shirohige</span> 

Enumerando de todo <span style="color:#07b4f2">llegamos a</span>
<span style="color:#ecacb6">/opt/backups/Solar-PuTTY</span>
Dentro <span style="color:#07b4f2">tenemos</span> un <span style="color:#ff2dc0">backup</span> de <span style="color:#ff2dc0">sesiones</span>.
Busquemos en <span style="color:#ff2dc0">Internet</span> <span style="color:#07b4f2">algo</span> para <span style="color:#ff2dc0">descifrarlo</span>.
<span style="color:#07b4f2">Buscamos</span> "<span style="color:#ecacb6">decrypt solar putty sessions .dat</span>"

Encontramos
https://github.com/VoidSec/SolarPuttyDecrypt/blob/master/solar_putty.rb 

https://github.com/VoidSec/SolarPuttyDecrypt/releases :    nos <span style="color:#07b4f2">descargamos</span> el <span style="color:#ff2dc0">Decrypt</span> <span style="color:#ecacb6">.zip</span>.

<span style="color:#bef202">Pasos</span>.
<span style="color:#ecacb6">1.- </span><span style="color:#07b4f2">Pasamos</span> el <span style="color:#ff2dc0">comprimido</span> <span style="color:#07b4f2">a</span> un <span style="color:#ff2dc0">Windows</span> (<span style="color:#379075">descomprimimos</span>).
<span style="color:#ecacb6">2.-</span> Me <span style="color:#07b4f2">traigo</span> el <span style="color:#ecacb6">.dat</span> (<span style="color:#379075">estando en base64, el Decrypter hace lo suyo</span>) <span style="color:#07b4f2">a mi</span> máquina <span style="color:#ff2dc0">linux</span> para <span style="color:#07b4f2">pasarlo al</span> <span style="color:#ff2dc0">Windows</span>.
<span style="color:#ecacb6">3.-</span> Automatizamos la <span style="color:#ff2dc0">fuerza bruta</span> de <span style="color:#07b4f2">contraseñas</span>.
El <span style="color:#ff2dc0">Decrypt</span> <span style="color:#07b4f2">lo</span> <span style="color:#ff2dc0">ejecutamos</span> así
<span style="color:#88c425">SolarPuttyDecrypt.exe C:\Users\joele\OneDrive\Documentos\Pruebas\sessions.dat ""</span>
<span style="color:#ecacb6">""</span> es de <span style="color:#ff2dc0">empty password</span>, <span style="color:#07b4f2">ahí</span> es donde <span style="color:#07b4f2">probaremos</span> las <span style="color:#07b4f2">contraseñas</span>, si es <span style="color:#07b4f2">incorrecta</span> nos <span style="color:#07b4f2">sale</span> en <span style="color:#07b4f2">rojo</span> una "<span style="color:#ecacb6">...Exception: Datos incorrectos</span>".

<span style="color:#ff2dc0">Script</span>
![[Pasted image 20241026164423.png]]
<span style="color:#ecacb6">subprocess()</span> se <span style="color:#07b4f2">usa más que</span> <span style="color:#ecacb6">os.system()</span> 
<span style="color:#ecacb6">Password correct: estrella</span>

Podemos ver <span style="color:#07b4f2">información</span> y <span style="color:#ff2dc0">credenciales</span> de <span style="color:#ff2dc0">root</span>
<span style="color:#ecacb6">root:12**24nzC!r0c%q12</span>
<span style="color:#ff2dc0">Migramos</span> <span style="color:#07b4f2">a</span> <span style="color:#ff2dc0">root</span> y pa deeeentro.

FIN.
