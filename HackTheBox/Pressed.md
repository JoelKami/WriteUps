# Pressed

Tags: [[_cms]],[[_infoLeakage]],[[_webExploitation]],[[_suidAbusing]],[[_cve]]

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

| Port                                  | Service             |
| ------------------------------------- | ------------------- |
| <span style="color:#ff669c">80</span> | Apache httpd 2.4.41 |


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">NMAP</span>. <span style="color:#ff2dc0">WordPress 5.9</span>
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#ff2dc0">MetaGenerator WordPress 5.9</span>, poco más.
<span style="color:#ff2dc0">Desde la web</span>.
Vemos <span style="color:#ff2dc0">rutas</span>.
<span style="color:#ecacb6">/index.php/2022/01/28/hello-world/</span> :    <span style="color:#07b4f2">apartado</span> donde <span style="color:#07b4f2">se ven</span> las <span style="color:#ff2dc0">peticiones</span> al <span style="color:#ff2dc0">servidor</span>, <span style="color:#07b4f2">solo se ve</span> el <span style="color:#ff2dc0">User-Agent</span> de la <span style="color:#ff2dc0">petición</span> que se hacen.
En una petición <span style="color:#07b4f2">vemos</span> el <span style="color:#ff2dc0">domino</span><span style="color:#ecacb6"> http://pressed.htb</span>, lo metemos en el <span style="color:#ecacb6">/etc/hosts</span>
<span style="color:#07b4f2">Probamos cosas</span>, pero <span style="color:#07b4f2">nada</span>.

<span style="color:#ff2dc0">Enumerando</span> el WordPress.
Lo hacemos <span style="color:#07b4f2">manual</span>, pero <span style="color:#07b4f2">nos apoyamos</span> de <span style="color:#ff2dc0">wpscan</span> para analizar el WordPress.
<span style="color:#88c425">wpsan --url http://pressed.htb</span>

<span style="color:#ecacb6">/wp-content/uploads </span>:    nos lo <span style="color:#07b4f2">descargamos</span> de <span style="color:#07b4f2">forma</span> <span style="color:#ff2dc0">recursiva</span>. Pero <span style="color:#07b4f2">no</span> hay <span style="color:#07b4f2">mucho</span>.
<span style="color:#ecacb6">/wp-config.php.bak</span> :    un <span style="color:#ff2dc0">backup</span> del <span style="color:#ff2dc0">archivo</span> de <span style="color:#ff2dc0">configuración</span> <span style="color:#07b4f2">de</span> la <span style="color:#ff2dc0">DB</span>. Vemos
<span style="color:#ecacb6">admin:uhc-jan-finals-2021</span>, pero <span style="color:#ff9a57">no es válida</span> en WordPress, pero como <span style="color:#07b4f2">va por años</span>, <span style="color:#07b4f2">probamos</span> <span style="color:#ecacb6">2022</span> y sí, <span style="color:#ecacb6">admin:uhc-jan-finals-2022</span>


<span style="color:#07b4f2">Vemos</span> que el <span style="color:#ecacb6">/xmlrpc.php</span> se ve, y ya sabemos que podemos <span style="color:#07b4f2">tratar</span> de <span style="color:#07b4f2">cambiar</span> el <span style="color:#ff2dc0">post</span> <span style="color:#07b4f2">actual</span> para <span style="color:#07b4f2">agregar</span> una <span style="color:#ff2dc0">PHP Web Shell</span>, pero <span style="color:#07b4f2">no se pueden establecer</span> <span style="color:#ff2dc0">conexiones</span>.
<span style="color:#379075">Véase /Vulnerabilidades y Temas/Hacking Web/ Enumerar CMS´s/WordPress/xmlrpc.php Abusing</span>


## Escalada

Enumerar todo el sistema.
Efectivamente, un Ubuntu Focal.
Estamos <span style="color:#07b4f2">desde</span> una <span style="color:#ff2dc0">Web Shell</span>.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel de sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">htb</span>

<span style="color:#ecacb6">www-data</span> -> como el que estamos.

Buscando por <span style="color:#ff2dc0">archivos</span> con privilegios <span style="color:#ff2dc0">SUID</span> <span style="color:#07b4f2">encontramos</span> el <span style="color:#ecacb6">/usr/bin/pkexec</span>

Como <span style="color:#07b4f2">no nos podemos</span> <span style="color:#ff2dc0">conectar</span> a la <span style="color:#ff2dc0">máquina</span>, utilizamos el <span style="color:#ecacb6">xmlrpc.php</span> para <span style="color:#07b4f2">introducir</span> un nuevo <span style="color:#ff2dc0">archivo</span>.
Utilizamos https://github.com/kimusan/pkwner/blob/main/pkwner.sh :    está bueno porque <span style="color:#07b4f2">podemos controlar</span> el <span style="color:#ff2dc0">comando</span> a <span style="color:#ff2dc0">ejecutar</span> <span style="color:#07b4f2">una vez se</span> <span style="color:#ff2dc0">explota</span> la vulnerabilidad, <span style="color:#07b4f2">en lugar</span> de <span style="color:#ecacb6">"/bin/bash"</span> que sea <span style="color:#ecacb6">"id"</span>.

<span style="color:#bef202">Proceder</span>.
+<span style="color:#07b4f2">Modificamos</span> el <span style="color:#ff2dc0">script</span> para <span style="color:#ff2dc0">ejecutar</span> un <span style="color:#ff2dc0">comando</span> que <span style="color:#07b4f2">queramos</span> (<span style="color:#379075">como un id</span>).
+<span style="color:#07b4f2">Utilizamos</span> el <span style="color:#ecacb6">xmlrpc.php</span> para <span style="color:#ff2dc0">subir</span> un <span style="color:#ff2dc0">archivo multimedia</span> <span style="color:#07b4f2">con</span> el <span style="color:#ff2dc0">código</span> en <span style="color:#ff2dc0">bash</span>.
Al subirlo <span style="color:#07b4f2">nos da</span> la <span style="color:#ff2dc0">ruta</span> <span style="color:#07b4f2">donde se almacenó</span>.
+<span style="color:#ff2dc0">Ejecutamos</span> el <span style="color:#ff2dc0">script</span> <span style="color:#07b4f2">con</span> <span style="color:#ff2dc0">bash</span> y se ejecuta de <span style="color:#07b4f2">forma</span> <span style="color:#ff2dc0">privilegiada</span>.
Hoy no hubo pa dentro.
Fin.
