# PermX

Tags: [[_webFuzzing]],[[_infoLeakage]],[[_sudoersAbusing]]

## Primeros pasos
(Reconocimiento).

<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Activamos</span> <span style="color:#ff2dc0">VPN</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Linux</span>.

<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>. <span style="color:#07b4f2">Tratar</span> de <span style="color:#07b4f2">hacer</span>, <span style="color:#07b4f2">después</span> del <span style="color:#07b4f2">primer</span> <span style="color:#ff2dc0">escaneo</span>, <span style="color:#07b4f2">otro</span> <span style="color:#ff2dc0">escaneo</span> de los <span style="color:#ff669c">puertos</span> más <span style="color:#07b4f2">comunes</span>.
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Jammy</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.9p1</span>
<span style="color:#ff669c">80</span> - HTTP <span style="color:#379075">Apache 2.4.52</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. Veo que me <span style="color:#07b4f2">aplica</span> un <span style="color:#ff2dc0">redirect</span>, pero <span style="color:#07b4f2">no conoce</span> el <span style="color:#ff2dc0">dominio</span> <span style="color:#ecacb6">permx.htb</span>, <span style="color:#07b4f2">lo incorporo en</span> el <span style="color:#ecacb6">/etc/hosts</span>
Ahora me <span style="color:#ff2dc0">redirige</span>, y veo <span style="color:#07b4f2">lo normal</span>, y un <span style="color:#ff2dc0">correo</span> <span style="color:#ecacb6">permx@htb.com</span>

<span style="color:#ff2dc0">Desde la web</span>.
<span style="color:#07b4f2">Página</span> de <span style="color:#07b4f2">aprendizaje</span> en línea. Hay <span style="color:#07b4f2">varios</span> <span style="color:#ff2dc0">recursos</span>, hay que <span style="color:#ff2dc0">enumerarlos</span>.

<span style="color:#ff2dc0">Código fuente</span>, <span style="color:#379075">de la web</span>.
<span style="color:#07b4f2">No</span> hay <span style="color:#07b4f2">información importante</span>, solo de <span style="color:#07b4f2">dónde toma </span>los <span style="color:#ff2dc0">recursos</span>.

<span style="color:#ff2dc0">Recursos</span>.
<span style="color:#ecacb6">/lib/</span> :    <span style="color:#ecacb6">/lib/easing/easing.min.js</span> <span style="color:#ff2dc0">analizar</span>, <span style="color:#07b4f2">no</span> es <span style="color:#07b4f2">mucho</span>. <span style="color:#ecacb6">/lib/waypoints/links.php</span> (<span style="color:#379075">de cara a un LFI</span>).

<span style="color:#ecacb6">/js/</span> :    está el <span style="color:#ecacb6">main.js</span>
<span style="color:#ecacb6">/img/</span>

<span style="color:#ff2dc0">Posible Users</span>.
<span style="color:#ecacb6">Noah</span>
<span style="color:#ecacb6">Elsie</span>
<span style="color:#ecacb6">Ralph</span>
<span style="color:#ecacb6">Mia</span>
<span style="color:#ecacb6">Emma</span>
<span style="color:#ecacb6">Sarah</span>
<span style="color:#ecacb6">Johny</span>
<span style="color:#ecacb6">James</span>
<span style="color:#07b4f2">Podemos probar</span> <span style="color:#ff2dc0">ataque</span> de<span style="color:#ff2dc0"> brute force</span> en el <span style="color:#ff669c">22</span>.

<span style="color:#ff2dc0">Fuzz URIs</span>.
<span style="color:#07b4f2">No</span> hay <span style="color:#07b4f2">mucho</span>, <span style="color:#07b4f2">lo</span> mismo <span style="color:#07b4f2">que conocíamos</span>.

<span style="color:#ff2dc0">Fuzz Subdomains</span>.
<span style="color:#ecacb6">www</span>
<span style="color:#ecacb6">lms</span>
<span style="color:#07b4f2">Los incorporamos</span> en el <span style="color:#ecacb6">/etc/hosts</span> 

<span style="color:#bef202">www.permx.htb</span>
Me lleva a <span style="color:#07b4f2">lo mismo</span>.

<span style="color:#bef202">lms.permx.htb</span>
Me lleva a un <span style="color:#07b4f2">panel</span> de <span style="color:#ff2dc0">autenticación</span>.
<span style="color:#ff2dc0">Chamilo</span>. <span style="color:#ff2dc0">Sistema</span> de <span style="color:#07b4f2">gestión</span> de <span style="color:#07b4f2">aprendizaje</span>. En <span style="color:#ff2dc0">PHP</span>.
<span style="color:#07b4f2">Administrador</span>: <span style="color:#ecacb6">Davis Miller</span>

Como puedo <span style="color:#07b4f2">poner</span> un <span style="color:#07b4f2">idioma</span> con <span style="color:#ecacb6">?language</span>, <span style="color:#07b4f2">intento</span> un <span style="color:#ff2dc0">LFI</span>, pero vemos que <span style="color:#07b4f2">nada</span>.

En <span style="color:#ff2dc0">searchsploit</span> tenemos <span style="color:#07b4f2">cosas interesantes</span>.
<span style="color:#07b4f2">Buscamos</span> en internet, "<span style="color:#ecacb6">chamilo exploit unauthenticated</span>", y encontramos <span style="color:#07b4f2">cosas</span>, entre ellas
https://starlabs.sg/advisories/23/23-4220/
<span style="color:#ecacb6">http://lms.permx.htb/main/inc/lib/javascript/bigupload/inc/</span> :    una <span style="color:#ff2dc0">ruta expuesta</span>, donde <span style="color:#07b4f2">hay</span> un <span style="color:#ecacb6">bigUpload.php</span>

La <span style="color:#07b4f2">idea</span> es la siguiente.
1.- <span style="color:#07b4f2">Creamos</span> una <span style="color:#ff2dc0">web shell</span>.
2.- <span style="color:#ff2dc0">Subir</span> el <span style="color:#ff2dc0">archivo</span> <span style="color:#ecacb6">cmd.php</span> (<span style="color:#379075">web shell</span>).
<span style="color:#88c425">curl -F 'bigUploadFile=@cmd.php' 'http://lms.permx.htb/main/inc/lib/javascript/bigupload/inc/bigUpload.php?action=post-unsupported'</span>
Nos <span style="color:#07b4f2">debe decir</span> que <span style="color:#07b4f2">se subió bien</span> (<span style="color:#379075">no vemos la ruta, pero la conocemos por el PoC</span>).
3.- Ya podríamos <span style="color:#ff2dc0">ejecutar comandos</span>. <span style="color:#07b4f2">Ganamos acceso</span> a la <span style="color:#ff2dc0">máquina vícitma</span>.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Jammy.

<span style="color:#07b4f2">Usuarios válidos</span> a nivel de <span style="color:#ff2dc0">sistema</span>
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">mtz</span>

<span style="color:#ff669c">Puertos internos</span>.
<span style="color:#ecacb6">22</span>
<span style="color:#ecacb6">53</span>
<span style="color:#ecacb6">3306</span>
<span style="color:#ecacb6">39644</span>
<span style="color:#ecacb6">41024</span>
<span style="color:#ecacb6">44302</span>

<span style="color:#07b4f2">Encontramos</span> una <span style="color:#ff2dc0">Public Key</span> en <span style="color:#ecacb6">vendor/robrichards/xmlseclibs/src/XMLSecurityKey.php</span>
Así que<span style="color:#07b4f2"> buscando por</span> una <span style="color:#ff2dc0">Private Key</span>, <span style="color:#07b4f2">pero</span> encontramos <span style="color:#ff2dc0">claves falsas</span>.

<span style="color:#07b4f2">Buscamos</span> por <span style="color:#07b4f2">archivos</span> de <span style="color:#ff2dc0">configuración</span>, encontramos <span style="color:#ecacb6">app/config/configuration.php</span>
<span style="color:#ff2dc0">Credenciales</span> de <span style="color:#ff2dc0">acceso</span> <span style="color:#07b4f2">a</span> la<span style="color:#ff2dc0"> Base de Datos</span>.
<span style="color:#ecacb6">db_host</span> = <span style="color:#ecacb6">localhost</span>
<span style="color:#ecacb6">db_port</span> = <span style="color:#ecacb6">3306</span>
<span style="color:#ecacb6">main_database</span> = <span style="color:#ecacb6">chamilo</span>
<span style="color:#ecacb6">db_user</span> = <span style="color:#ecacb6">chamilo</span>
<span style="color:#ecacb6">db_password</span> = <span style="color:#ecacb6">03F6lY3uXAP2bkW8</span>

<span style="color:#ff2dc0">Enumeramos</span> <span style="color:#07b4f2">la</span> <span style="color:#ff2dc0">Base de Datos</span>.
En la <span style="color:#ff2dc0">data base</span> <span style="color:#ecacb6">chamilo</span>, hay una <span style="color:#ff2dc0">tabla</span> <span style="color:#ecacb6">user</span>.
Tenemos el <span style="color:#ff2dc0">usuario</span> <span style="color:#ecacb6">admin</span> y <span style="color:#ecacb6">anon</span>, ambos <span style="color:#07b4f2">con</span> <span style="color:#ff2dc0">hashes</span> como <span style="color:#ff2dc0">contraseñas</span> que <span style="color:#07b4f2">intentaremos</span> <span style="color:#ff2dc0">romper</span>.
<span style="color:#ff2dc0">Cracking</span>.
Por la <span style="color:#07b4f2">información</span> que <span style="color:#07b4f2">había</span>, podemos <span style="color:#07b4f2">deducir</span> que se trata de <span style="color:#ff2dc0">Bcrypt</span>, pero <span style="color:#07b4f2">igual</span> hay que <span style="color:#07b4f2">validarlo</span>.

<span style="color:#07b4f2">Mientras</span> tanto, podemos <span style="color:#07b4f2">probar</span> si se <span style="color:#ff2dc0">reutilizan contraseñas</span>.
Y efectivamente
<span style="color:#ecacb6">mtz:03F6lY3uXAP2bkW8</span> 

Hacemos<span style="color:#88c425"> sudo -l</span>, <span style="color:#07b4f2">proporcionamos</span> la <span style="color:#ff2dc0">contraseña</span>, y podemos <span style="color:#ff2dc0">ejecutar</span> lo siguiente
<span style="color:#ecacb6">(ALL : ALL) NOPASSWD: /opt/acl.sh</span> 
<span style="color:#07b4f2">Ejecutarlo</span>
<span style="color:#88c425">sudo /opt/acl.sh mtz rwx /home/mtz/exploit.sh</span>
<span style="color:#07b4f2">Pasamos</span> un <span style="color:#ecacb6">usuario</span>, <span style="color:#ecacb6">permisos</span> y el <span style="color:#ecacb6">archivo</span> a <span style="color:#07b4f2">modificar</span>, ya que el <span style="color:#ff2dc0">script</span> <span style="color:#07b4f2">hace</span> al final
<span style="color:#ecacb6">/usr/bin/sudo /usr/bin/setfacl -m u:"$user":"$perm" "$target"</span>  :    se ponen los <span style="color:#ff2dc0">argumentos</span> que <span style="color:#07b4f2">pasamos</span> al <span style="color:#ff2dc0">script</span>.

<span style="color:#bef202">Puedo hacer</span>.
<span style="color:#88c425">ln -s /etc/passwd /home/mtz/fakePasswd</span> :    parece que <span style="color:#07b4f2">hay</span> una <span style="color:#ff2dc0">tarea cron</span> que <span style="color:#07b4f2">elimina</span> los <span style="color:#ff2dc0">enlaces simbólicos</span>, seamos rápidos. <span style="color:#379075">Podríamos modificar el shadow o el sudoers</span>.
<span style="color:#88c425">sudo /opt/acl.sh mtz rwx /home/mtz/fakePasswd</span>
<span style="color:#88c425">nano /home/mtx/fakePasswd</span> :   para <span style="color:#07b4f2">modificarlo</span>, le <span style="color:#07b4f2">añadimos</span> una <span style="color:#ff2dc0">contraseña Hardcodeada</span> en <span style="color:#07b4f2">formato</span> <span style="color:#ff2dc0">DES</span>(<span style="color:#ff2dc0">Unix</span>) en <span style="color:#07b4f2">donde está</span> la '<span style="color:#ecacb6">x</span>' en <span style="color:#ff2dc0">root</span>.

<span style="color:#88c425">su root</span> :    colocamos la <span style="color:#ff2dc0">contraseña</span> <span style="color:#07b4f2">que creamos</span> y pa deeeentro.
FIN.
