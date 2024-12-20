# Cap

Tags: [[_infoLeakage]],[[_webExploitation]],[[_capabilities]]

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

<span style="color:#ff669c">21</span> - FTP. <span style="color:#379075">vsftpd 3.0.3</span>
<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.2p1</span>
<span style="color:#ff669c">80</span> - HTTP. <span style="color:#379075">gunicorn</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">21</span>.
<span style="color:#ff2dc0">Anonymous</span> <span style="color:#07b4f2">no</span> está <span style="color:#07b4f2">habilitado</span>.

<span style="color:#07b4f2">Probamos</span> <span style="color:#ecacb6">nathan:Buck3tH4TF0RM3!</span>
Y nos <span style="color:#07b4f2">podemos</span> <span style="color:#ff2dc0">conectar</span>. Pero <span style="color:#07b4f2">no</span> hay <span style="color:#07b4f2">mucho</span>.
<span style="color:#bef202">Vamos al 22</span>.

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.

Con <span style="color:#ecacb6">nathan:Buck3tH4TF0RM3!</span> nos <span style="color:#07b4f2">podemos</span> <span style="color:#ff2dc0">conectar</span> y pa deeentro.

<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#ff2dc0">HTTPServer</span> <span style="color:#ecacb6">gunicorn</span>, tiene que ver con <span style="color:#ff2dc0">python</span>.
<span style="color:#ff2dc0">Wappalizer</span>. <span style="color:#ff2dc0">Python</span>, <span style="color:#ecacb6">Gunicorn</span>.

<span style="color:#ff2dc0">Desde la web</span>.
Estamos en un <span style="color:#07b4f2">panel</span>, hay <span style="color:#07b4f2">cosas</span>.
<span style="color:#ff2dc0">Rutas</span>.
<span style="color:#ecacb6">/capture</span> :    <span style="color:#ff2dc0">redirige</span> a <span style="color:#ecacb6">/data/4</span> (<span style="color:#379075">varía</span>). Puedo descargar seguramente una captura <span style="color:#ff2dc0">pcap</span>.
Como es <span style="color:#ecacb6">/data/4</span>, se acontece un <span style="color:#07b4f2">tipo</span> de <span style="color:#ff2dc0">IDOR</span>, si <span style="color:#07b4f2">pongo 0 se ven otros valores</span>.

El <span style="color:#ecacb6">.pcap</span> tiene varias <span style="color:#ff2dc0">peticiones</span> (<span style="color:#379075">esto porque descargamos el de /data/0</span>), entre ellas al <span style="color:#ff2dc0">FTP</span>, donde <span style="color:#07b4f2">vemos</span> una <span style="color:#ff2dc0">autenticación</span>, obtenemos <span style="color:#ff2dc0">credenciales</span>.
<span style="color:#ecacb6">nathan:Buck3tH4TF0RM3!</span>
<span style="color:#bef202">Vamos al 21</span>.

<span style="color:#ecacb6">/ip</span> :    <span style="color:#07b4f2">como</span> un <span style="color:#88c425">ipconfig</span>.
<span style="color:#ecacb6">/netsats</span>  :    <span style="color:#ff669c">puertos</span> y <span style="color:#ff2dc0">servicios</span> abiertos.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Focal.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">nathan</span>

Al <span style="color:#07b4f2">hacer</span> 
<span style="color:#88c425">getcap -r /</span> :    vemos<span style="color:#ecacb6"> /usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip</span>
<span style="color:#07b4f2">Hacemos</span>
<span style="color:#88c425">python3 -c 'import os; os.setuid(0); os.system("/bin/bash");'</span>
Y pa deeeentro.

FIN.
