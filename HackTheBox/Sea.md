# Sea

Tags: [[_cms]],[[_xss]],[[_webExploitation]],[[_cracking]],[[_internalPortAbusing]],[[_commandInjection]]

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

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.2p1</span>
<span style="color:#ff669c">80</span> - HTTP. <span style="color:#379075">Apache httpd 2.4.41</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.

<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. Solo el Title, <span style="color:#ecacb6">Sea - Home</span>.
<span style="color:#ff2dc0">Desde la web</span>. Algo de Bicis.
<span style="color:#ecacb6">/how-to-participate</span> :    me dice que puedo <span style="color:#07b4f2">enviar</span> mis <span style="color:#ff2dc0">datos</span>, hay un <span style="color:#ff2dc0">dominio</span> <span style="color:#ecacb6">sea.htb</span>, lo ponemos en el <span style="color:#ecacb6">/etc/hosts</span>

<span style="color:#ff2dc0">Código fuente</span>.
Se ve <span style="color:#ecacb6">/themes/bike/</span>, <span style="color:#ff2dc0">fuzzeamos</span> dentro y encontramos
<span style="color:#ecacb6">/img</span>
<span style="color:#ecacb6">/home</span>
<span style="color:#ecacb6">/version</span>
<span style="color:#ecacb6">/css</span>
<span style="color:#ecacb6">/summary</span>
<span style="color:#ecacb6">/404</span>
<span style="color:#ecacb6">/LICENSE</span>
Hay que <span style="color:#07b4f2">buscar más</span> cosas <span style="color:#07b4f2">aquí</span>, por alguna <span style="color:#ff2dc0">extensión</span> que pueda <span style="color:#07b4f2">contener información</span>.
<span style="color:#ecacb6">/README.md</span> :    se descarga. En los de arriba se ven cositas, aquí también. En <span style="color:#ecacb6">/summary</span> y <span style="color:#ecacb6">/version</span> se ve <span style="color:#ff2dc0">turboblack</span> (<span style="color:#379075">author</span>) <span style="color:#ff2dc0">3.2.0</span> y si buscamos <span style="color:#ff2dc0">exploits</span> de eso, <span style="color:#07b4f2">encontramos</span> que<span style="color:#07b4f2"> se relaciona con</span> <span style="color:#ff2dc0">WonderCMS</span>.
Pues en <span style="color:#ecacb6">/README.md</span> se ve bien que se <span style="color:#07b4f2">trata de eso</span>. Se ve una ruta <span style="color:#ecacb6">/preview.jpg</span>, que es una captura de la página principal, pero se está como <span style="color:#ff2dc0">admin</span> y hay más opciones.

<span style="color:#07b4f2">Buscando</span> por <span style="color:#ff2dc0">exploits</span> para <span style="color:#ff2dc0">WonderCMS 3.2.0</span>, encontramos un <span style="color:#ff2dc0">artículo</span> https://shivamaharjan.medium.com/the-why-and-how-of-cve-2023-41425-wondercms-vulnerability-7ebffbff37d2 :    <span style="color:#379075">autenticado o no</span>.
Debemos hacerlo en la ruta <span style="color:#07b4f2">donde se instaló</span> el <span style="color:#ff2dc0">WonderCMS</span>, en <span style="color:#ecacb6">/themes</span> <span style="color:#07b4f2">o</span> en <span style="color:#ecacb6">/loginURL</span> y lo debemos <span style="color:#ff2dc0">ejecutar</span> estando <span style="color:#ff2dc0">autenticados</span> en la ruta <span style="color:#ecacb6">/loginURL</span> o que un <span style="color:#ecacb6">usuario</span> <span style="color:#ff2dc0">autenticado</span> lo <span style="color:#07b4f2">haga</span> (<span style="color:#379075">esta segunda es nuestro caso</span>)

<span style="color:#bef202">Pasos</span>.
1.- <span style="color:#07b4f2">Saber</span> si estoy <span style="color:#ff2dc0">autenticado</span>.
Si accedo a 
<span style="color:#ecacb6">sea.htb/wondercms/index.php?page=home</span>
Si se ve, lo estoy.

O <span style="color:#ff2dc0">autenticarme</span> en <span style="color:#ecacb6">/loginURL</span> :    si <span style="color:#07b4f2">no</span> tengo <span style="color:#ff2dc0">credenciales</span>, <span style="color:#07b4f2">no pasa nada</span>.

2.- Se <span style="color:#ff2dc0">descarga</span> el <span style="color:#ecacb6">.zip</span>
Esto por la <span style="color:#ff2dc0">vulnerabilidad</span> en <span style="color:#ecacb6">installModule</span> (<span style="color:#379075">el exploit lo hace</span>).
Se logra a <span style="color:#07b4f2">través</span> del <span style="color:#ff2dc0">XSS</span>, para <span style="color:#07b4f2">llamar</span> a <span style="color:#ecacb6">xss.js</span> que <span style="color:#07b4f2">almacena</span> su <span style="color:#ff2dc0">contenido</span> (<span style="color:#379075">shell/shell.php</span>) <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">server</span>.

3.- Se le hace un <span style="color:#ff2dc0">GET</span> al archivo<span style="color:#ecacb6"> .php</span>
Esto lo hacemos <span style="color:#07b4f2">manual</span>, <span style="color:#07b4f2">pero</span> el <span style="color:#ff2dc0">script</span> ya lo <span style="color:#07b4f2">hace</span>.


<span style="color:#bef202">Importante</span>.
Utilizamos https://github.com/thefizzyfish/CVE-2023-41425-wonderCMS_RCE

+Si <span style="color:#07b4f2">no</span> estamos <span style="color:#ff2dc0">autenticados</span>, usamos el <span style="color:#ff2dc0">exploit</span> y simplemente <span style="color:#07b4f2">copiamos</span> el <span style="color:#ff2dc0">XSS</span> y <span style="color:#07b4f2">lo mandamos en</span> el <span style="color:#07b4f2">apartado</span> de <span style="color:#ecacb6">web site</span> en <span style="color:#ecacb6">contact.php</span> (<span style="color:#379075">paso 2</span>).
+<span style="color:#07b4f2">Probamos</span> también a hacerle un <span style="color:#ff2dc0">curl</span> al <span style="color:#ff2dc0">recurso</span> que <span style="color:#07b4f2">alojamos</span>. Esto porque <span style="color:#ecacb6">shell.zip</span> <span style="color:#07b4f2">contiene</span> <span style="color:#ecacb6">shell/shell.php</span>, donde le <span style="color:#07b4f2">puedo</span> concatenarle <span style="color:#ecacb6">?0whoami</span> para ejecutar <span style="color:#ff2dc0">comandos</span>.
+El <span style="color:#ecacb6">xss.js</span> que es el <span style="color:#ff2dc0">recurso</span> al que<span style="color:#07b4f2"> se hace</span> la <span style="color:#ff2dc0">petición</span> mediante el <span style="color:#ff2dc0">XSS</span>, te <span style="color:#07b4f2">lo crea</span> el <span style="color:#ff2dc0">script</span>. El cual hace que <span style="color:#07b4f2">almacene</span> lo que hay en <span style="color:#ecacb6">shell.zip</span> (<span style="color:#379075">shell/shell.php</span>) en el <span style="color:#ff2dc0">server</span> web.
+El <span style="color:#ff2dc0">script monta</span> el <span style="color:#ff2dc0">server HTTP</span> para <span style="color:#ff2dc0">servir</span> el <span style="color:#ecacb6">xss.js</span>

![[Pasted image 20241012005127.png]]
<span style="color:#07b4f2">Copiamos</span> el <span style="color:#ff2dc0">XSS</span> y lo <span style="color:#07b4f2">mandamos</span> para que un <span style="color:#ecacb6">usuario</span> <span style="color:#ff2dc0">autenticado</span> le de <span style="color:#89d3ec">click</span>.

![[Pasted image 20241012005209.png]]


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Focal.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">amay</span>
<span style="color:#ecacb6">geo</span>

Al <span style="color:#ff2dc0">enumerar</span> archivos con permiso <span style="color:#ff2dc0">SUID</span> vemos
<span style="color:#ecacb6">/opt/google/chrome/chrome-sandbox</span> :    podemos <span style="color:#07b4f2">pensar</span> en otros <span style="color:#ff669c">puertos internos</span>.
<span style="color:#ff669c">22</span>
<span style="color:#ff669c">53</span>
<span style="color:#ff669c">8080</span>
<span style="color:#ff669c">34564</span>
<span style="color:#ff669c">35332</span>
<span style="color:#ff669c">41926</span>
<span style="color:#ff669c">42481</span>
<span style="color:#ff669c">43128</span>
<span style="color:#ff669c">44430</span>
<span style="color:#ff669c">51954</span>
<span style="color:#ff669c">52550</span>
<span style="color:#ff669c">55418</span>
<span style="color:#ff669c">56620</span>
<span style="color:#ff669c">56676</span>
<span style="color:#ff669c">58526</span>

El <span style="color:#ff669c">8080</span> llama la <span style="color:#07b4f2">atención</span>, pero si le hago un <span style="color:#ff2dc0">curl</span>, me pone <span style="color:#ff2dc0">Unauthorized</span>, lo que me hace <span style="color:#07b4f2">pensar</span> que <span style="color:#07b4f2">necesito</span> <span style="color:#ff2dc0">credenciales</span>.

En <span style="color:#ecacb6">/var/www/sea/data/database.js</span>
<span style="color:#07b4f2">Encontramos</span> un <span style="color:#ff2dc0">hash</span>
<span style="color:#ecacb6">$2y$10$iOrk210RQSAzNCx6Vyq2X.aJ/D.GuE4jRIikYiWrD3TM/PjDnXm4q</span> :    lo rompemos con <span style="color:#ff2dc0">john</span>. La probamos y <span style="color:#07b4f2">es de</span> <span style="color:#ecacb6">amay</span>.
<span style="color:#ecacb6">amay:mychemicalromance</span> 

Hacemos el <span style="color:#ff2dc0">curl</span> <span style="color:#07b4f2">de antes al</span> <span style="color:#ff669c">8080</span> pero <span style="color:#07b4f2">proporcionando</span> las <span style="color:#ff2dc0">credenciales</span>, y me <span style="color:#07b4f2">carga</span> la <span style="color:#ff2dc0">web</span>.
Juguemos con <span style="color:#ff2dc0">ssh</span> para un <span style="color:#ff2dc0">Local Port Forwarding</span>.

En la web <span style="color:#ff669c">8080</span> podemos ver <span style="color:#ff2dc0">logs</span>, de <span style="color:#ff2dc0">apache</span> y <span style="color:#ff2dc0">ssh</span>.
Pasamos por <span style="color:#ff2dc0">BurpSuite</span> (<span style="color:#379075">el port forwarding lo hacemos en otro puerto</span>) para ver <span style="color:#07b4f2">qué se envía</span> para cargar los logs.
<span style="color:#07b4f2">Se envía</span> el <span style="color:#ff2dc0">archivo</span> a <span style="color:#07b4f2">leer</span>, pero <span style="color:#07b4f2">no</span> funciona un <span style="color:#ff2dc0">LFI</span>, <span style="color:#07b4f2">aunque</span> podemos hacer un<span style="color:#ff2dc0"> HTML Injection</span> y un <span style="color:#ff2dc0">XSS Injection</span>.

En los <span style="color:#ff2dc0">access.log</span> de <span style="color:#ff2dc0">apache</span> vemos que al <span style="color:#07b4f2">final</span> aparece algo de "<span style="color:#ecacb6">Suspicious traffic...</span>", donde se <span style="color:#07b4f2">registra</span> un <span style="color:#ff2dc0">log</span> aparte, que parece <span style="color:#ff2dc0">malicioso</span>, lo curioso es que va cambiando, solo aparece uno.
Esto me hace <span style="color:#07b4f2">pensar</span>, que <span style="color:#07b4f2">por detrás</span> está haciendo un <span style="color:#ff2dc0">comando</span> para <span style="color:#07b4f2">determinar patrones</span> dentro de los <span style="color:#ff2dc0">logs</span>.
Porque cuando pongo <span style="color:#07b4f2">otro</span> <span style="color:#ff2dc0">archivo</span>, me sale "<span style="color:#ecacb6">No suspicious traffic...</span>", lo que determina que <span style="color:#07b4f2">sí le hace algo</span>, a <span style="color:#07b4f2">pesar</span> de <span style="color:#07b4f2">no mostrarlo.</span>

Entonces <span style="color:#07b4f2">tratemos</span> de <span style="color:#ff2dc0">escapar</span> del <span style="color:#ff2dc0">comando</span> para <span style="color:#ff2dc0">ejecutar</span> <span style="color:#07b4f2">otro</span>, <span style="color:#07b4f2">al pasar</span> el <span style="color:#ff2dc0">archivo</span> de log para ver, hacemos
<span style="color:#ecacb6">/var/log/apache2/access.log; whoami</span> :    esto me da <span style="color:#07b4f2">mucho</span> <span style="color:#ff2dc0">output</span>, pensando en que <span style="color:#07b4f2">no</span> puedo <span style="color:#ff2dc0">cargar</span> <span style="color:#07b4f2">otros</span> <span style="color:#ff2dc0">archivos</span> de <span style="color:#07b4f2">primeras</span>, pruebo esto
<span style="color:#ecacb6">/root/root.txt; whoami</span> :    y me <span style="color:#07b4f2">muestra</span> la flag.
Curioso porque<span style="color:#07b4f2"> tengo que poner</span> <span style="color:#ecacb6">whoami</span> (<span style="color:#379075">u otra cosa, no solo un comando</span>), <span style="color:#07b4f2">sino</span>, <span style="color:#07b4f2">no</span> determina que <span style="color:#07b4f2">contiene</span> <span style="color:#ecacb6">Patrones Maliciosos</span>, <span style="color:#07b4f2">por lo tanto no</span> lo <span style="color:#07b4f2">muestra</span>. Por el contrario, <span style="color:#07b4f2">si ve</span> que ponemos <span style="color:#07b4f2">cosas</span> <span style="color:#ff2dc0">maliciosas</span> para ejecutar comandos, nos <span style="color:#07b4f2">muestra</span> el <span style="color:#ff2dc0">contenido</span>.

<span style="color:#bef202">Extra</span>, podemos hacer
<span style="color:#ecacb6">/root/root.txt;id;whoami</span> :    <span style="color:#07b4f2">vemos</span> todo, a <span style="color:#07b4f2">excepción</span> del <span style="color:#ecacb6">whoami</span>, <span style="color:#07b4f2">el último no</span> lo <span style="color:#ff2dc0">ejecuta</span> al parecer.
<span style="color:#379075">Nota</span>. Si <span style="color:#07b4f2">ganamos acceso</span> al sistema, <span style="color:#07b4f2">nos expulsa</span>, así que no está diseñada para ganar acceso como tal, <span style="color:#379075">así que no hubo pa dentro</span>.

FIN.
