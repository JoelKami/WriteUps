# BoardLight

Tags: [[_webFuzzing]],[[_cve]],[[_infoLeakage]],[[_suidAbusing]]

## Primeros pasos
(Reconocimiento).

<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Activamos</span> <span style="color:#ff2dc0">VPN</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Linux</span>.

<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>.
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Focal</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.2p1</span>
<span style="color:#ff669c">80</span> - HTTP. <span style="color:#379075">Apache HTTPD 2.4.41</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.


<span style="color:#ff669c">80.</span>
<span style="color:#ff2dc0">wahtweb</span>.
<span style="color:#07b4f2">Correo</span> <span style="color:#ecacb6">info@board.htb</span>, nos hace <span style="color:#07b4f2">pensar</span> en un <span style="color:#ff2dc0">dominio</span>, poco más.

<span style="color:#ff2dc0">En la web</span>.
No hay muchas <span style="color:#07b4f2">funcionalidades</span>, pero hay <span style="color:#07b4f2">varias rutas</span>/<span style="color:#07b4f2">recursos</span>.
<span style="color:#ff2dc0">Código fuente</span>.
<span style="color:#07b4f2">Línea</span> comentada con un <span style="color:#07b4f2">recurso</span> <span style="color:#ecacb6">portfolio.php</span>
Nos <span style="color:#07b4f2">dice</span> "<span style="color:#ecacb6">File not found.</span>", me hace <span style="color:#07b4f2">pensar</span> en que pude<span style="color:#07b4f2"> haber más</span>.

<span style="color:#ff2dc0">Fuzzeamos por URIs</span>.
Por <span style="color:#ff2dc0">extensiones PHP</span> también para ver si <span style="color:#07b4f2">encuentro</span> algo <span style="color:#07b4f2">más</span>.
Las que <span style="color:#07b4f2">sabíamos</span>:
<span style="color:#ecacb6">/about.php</span>
<span style="color:#ecacb6">/contact.php</span>
<span style="color:#ecacb6">/index.php</span>
<span style="color:#ecacb6">/do.php</span>


<span style="color:#07b4f2">Metamos</span><span style="color:#ecacb6"> board.htb</span> <span style="color:#07b4f2">en</span> el <span style="color:#ecacb6">/etc/hosts</span>, pero <span style="color:#07b4f2">no cambia</span> la <span style="color:#ff2dc0">web</span>.
<span style="color:#ff2dc0">Fuzzear Subdominios</span>. 
<span style="color:#ff2dc0">SubDominio</span> <span style="color:#ecacb6">crm</span>. En la <span style="color:#ff2dc0">web</span> <span style="color:#07b4f2">hay</span> un <span style="color:#ff2dc0">login</span> de <span style="color:#ff2dc0">Dolibarr</span>. <span style="color:#ecacb6">Dolibarr 17.0.0</span>

<span style="color:#ff2dc0">Dolibarr</span>. <span style="color:#379075">Es software para Planificación de Recursos Empresariales y Gestión de Realciones con los Clientes open source para PyMES</span>. Hecho <span style="color:#07b4f2">en</span> <span style="color:#ff2dc0">PHP</span>.

Usamos <span style="color:#ecacb6">admin:admin</span> y nos <span style="color:#07b4f2">deja</span>.

<span style="color:#07b4f2">Encontrar vulnerabilidades</span> asociadas a su <span style="color:#07b4f2">versión</span>.
En <span style="color:#ff2dc0">github</span> https://github.com/dollarboysushil/Dolibarr-17.0.0-Exploit-CVE-2023-30253 se <span style="color:#07b4f2">habla</span> de <span style="color:#07b4f2">crear</span> un <span style="color:#ff2dc0">website</span> y <span style="color:#07b4f2">entrarle por ahí</span>.

<span style="color:#07b4f2">No</span> me <span style="color:#07b4f2">deja</span> usar <span style="color:#ff2dc0">php</span>, pero <span style="color:#07b4f2">poniendo</span> <<span style="color:#ecacb6">?pHp</span> ... me <span style="color:#07b4f2">deja</span>.
<span style="color:#07b4f2">Ruta</span> donde está el <span style="color:#ff2dc0">website</span>, <span style="color:#07b4f2">abrirlo una vez creamos</span> el <span style="color:#ff2dc0">website</span>, la <span style="color:#ff2dc0">página</span>, y <span style="color:#07b4f2">cuando a esta última</span> le hayamos <span style="color:#07b4f2">metido</span> el <span style="color:#ff2dc0">código</span> en <span style="color:#ff2dc0">PHP</span>.
<span style="color:#ecacb6">http://crm.board.htb/public/website/index.php?website=test&pageref=test</span>
<span style="color:#ecacb6">test</span> es el <span style="color:#07b4f2">nombre</span> del <span style="color:#ff2dc0">website</span>.

Nos ponemos <span style="color:#07b4f2">en escucha</span>, <span style="color:#07b4f2">cargamos</span> el <span style="color:#ff2dc0">website</span> y pa deentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Focal.

<span style="color:#07b4f2">Usuarios válidos</span> a nivel de <span style="color:#ff2dc0">sistema</span>
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">larissa</span>
<span style="color:#ecacb6">www-data</span> 

Cositas en el <span style="color:#ff2dc0">directorio</span> <span style="color:#ecacb6">/tmp</span>
<span style="color:#ecacb6">';'</span>
<span style="color:#ecacb6">exploit</span> 
Son <span style="color:#07b4f2">archivos de</span> <span style="color:#ecacb6">larissa</span>.

<span style="color:#07b4f2">Tratemos</span> de <span style="color:#07b4f2">ver</span><span style="color:#ff2dc0"> tareas cron</span>.

<span style="color:#ff669c">Puertos</span> abiertos <span style="color:#07b4f2">internamente</span>.
<span style="color:#ff669c">22</span>
<span style="color:#ff669c">53</span>
<span style="color:#ff669c">3306</span>
<span style="color:#ff669c">33060</span>
<span style="color:#ff669c">33334</span>
<span style="color:#ff669c">47146</span>
<span style="color:#ff669c">59962</span>
<span style="color:#ff669c">60548</span> 

Se hablan <span style="color:#07b4f2">cositas</span> de <span style="color:#ff2dc0">LDAP</span>.
Debemos <span style="color:#07b4f2">buscar</span> mucho <span style="color:#07b4f2">más</span> en el <span style="color:#ff2dc0">directorio</span> <span style="color:#ecacb6">crm.board.htb</span>
<span style="color:#07b4f2">En</span> el <span style="color:#ecacb6">composer.jsom.disabled</span> hay <span style="color:#07b4f2">cosas</span>.

<span style="color:#07b4f2">En</span> <span style="color:#ecacb6">htdocs/conf</span> hay <span style="color:#07b4f2">cositas</span>
<span style="color:#ecacb6">dolibarrowner:serverfun2$2023!!</span> :    <span style="color:#ff2dc0">credenciales</span> de <span style="color:#07b4f2">acceso</span> a la <span style="color:#ff2dc0">Base de Datos</span>.
Tenemos <span style="color:#ff2dc0">hashes</span> <span style="color:#07b4f2">en</span> la <span style="color:#ff2dc0">DB</span>:
<span style="color:#ecacb6">$2y$10$VevoimSke5Cd1/nX1Ql9Su6RstkTRe7UX1Or.cm8bZo56NjCMJzCm</span> 
<span style="color:#ecacb6">$2y$10$gIEKOl7VZnr5KLbBDzGbL.YuJxwz5Sdl5ji3SEuiUSlULgAhhjH96</span>
Pero <span style="color:#07b4f2">no se</span> <span style="color:#ff2dc0">rompen</span> fácilmente.
<span style="color:#07b4f2">Reutilicemos</span> la <span style="color:#ff2dc0">contraseña</span> <span style="color:#07b4f2">con</span> el usuario <span style="color:#ecacb6">larissa</span>.
Y nos <span style="color:#07b4f2">deja</span>.
<span style="color:#ecacb6">larissa:serverfun2$2023!!</span>

<span style="color:#bef202">Elevar a root</span>.
(
En <span style="color:#ecacb6">/larissa</span> hay un <span style="color:#ff2dc0">exploit</span> que intenta <span style="color:#07b4f2">abusar</span> de una <span style="color:#ff2dc0">vulnerabilidad</span> <span style="color:#07b4f2">de</span> <span style="color:#ecacb6">enlightenment_sys</span> que <span style="color:#07b4f2">tiene</span> <span style="color:#ff2dc0">privilegios SUID</span>.
Lo habíamos <span style="color:#07b4f2">visto</span> al <span style="color:#ff2dc0">enumerar SUID</span> de <span style="color:#ff2dc0">root</span>, de hecho podemos <span style="color:#07b4f2">copiarlo</span>, <span style="color:#07b4f2">buscar</span> un <span style="color:#ff2dc0">exploit</span> en <span style="color:#ff2dc0">internet</span>, y nos sale el mismo (https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit <span style="color:#379075">explica que debe empezar por /dev, etc.</span>).

De ahí los '<span style="color:#ecacb6">;</span>' y <span style="color:#ecacb6">exploit</span> en <span style="color:#ecacb6">/tmp</span>, los <span style="color:#07b4f2">llama</span> y como se <span style="color:#07b4f2">ejecuta</span> el <span style="color:#07b4f2">punto y coma</span>, <span style="color:#07b4f2">separa</span> otro <span style="color:#ff2dc0">comando</span> que <span style="color:#ff2dc0">spawnea</span> una <span style="color:#ff2dc0">shell</span>, como <span style="color:#ff2dc0">root</span> lo hace, <span style="color:#07b4f2">pues es como</span> <span style="color:#ff2dc0">root</span>.
)
¿<span style="color:#379075">Por qué lo de arriba en paréntesis</span>?, porque <span style="color:#07b4f2">no sé si alguien más creó</span> el <span style="color:#07b4f2">archivo</span> del <span style="color:#ff2dc0">exploit</span>, así que <span style="color:#07b4f2">tengamos</span> en <span style="color:#07b4f2">cuenta</span> que <span style="color:#ff2dc0">enumerando</span> <span style="color:#07b4f2">todo</span> habríamos <span style="color:#07b4f2">visto</span> el <span style="color:#ff2dc0">SUID</span>, y tendíamos que haber <span style="color:#07b4f2">investigado</span> sobre <span style="color:#ff2dc0">enlightenment</span> (<span style="color:#379075">se puede usar como comando incluso</span>), haber <span style="color:#07b4f2">conseguido</span> su <span style="color:#07b4f2">versión</span>, etc., y <span style="color:#07b4f2">posterior</span> a ello, <span style="color:#07b4f2">encontrar</span> el <span style="color:#ff2dc0">exploit</span> que <span style="color:#07b4f2">utilizamos</span>.

Lo <span style="color:#07b4f2">ejecuto</span> y pa deeeeeentro.

FIN.
