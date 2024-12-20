# Return

Tags: [[_infoLeakage]],[[_permissionAbuse]],[[_dc]]

## Primeros pasos
(Reconocimiento).

<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Activamos</span> <span style="color:#ff2dc0">VPN</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Windows</span>.

<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>. <span style="color:#379075">Otro escaneo de puertos comunes por posibles falsos negativos</span>.
Esta vez <span style="color:#07b4f2">no buscamos</span> <span style="color:#ff2dc0">code name</span> con <span style="color:#ff2dc0">launchpad</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

| Port                                     | Service                   |
| ---------------------------------------- | ------------------------- |
| <span style="color:#ff669c">53</span>    | DNS                       |
| <span style="color:#ff669c">80</span>    | IIS httpd 10.0            |
| <span style="color:#ff669c">88</span>    | Kerberos                  |
| <span style="color:#ff669c">135</span>   | RPC                       |
| <span style="color:#ff669c">139</span>   | NetBIOS                   |
| <span style="color:#ff669c">389</span>   | LDAP                      |
| <span style="color:#ff669c">445</span>   |                           |
| <span style="color:#ff669c">464</span>   | kpasswd5?                 |
| <span style="color:#ff669c">593</span>                                      | RPC over HTTP             |
| <span style="color:#ff669c">3268</span>  | LDAP                      |
| <span style="color:#ff669c">3269</span>  |                           |
| <span style="color:#ff669c">5985</span>  | WinRM                     |
| <span style="color:#ff669c">9389</span>  | mc-nmf                    |
| <span style="color:#ff669c">47001</span> | HTTPAPI httpd 2.0 (WinRM) |
Hay <span style="color:#07b4f2">otros</span> <span style="color:#ff669c">puertos</span> que van <span style="color:#07b4f2">desde</span> <span style="color:#ff669c">49665</span> en <span style="color:#07b4f2">adelante</span>.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff2dc0">NMAP</span>.
<span style="color:#ff2dc0">Domain</span>: <span style="color:#ecacb6">return.local</span>
<span style="color:#ff2dc0">Host</span>: <span style="color:#ecacb6">PRINTER</span>
<span style="color:#ff2dc0">OS</span>: <span style="color:#ecacb6">Windows</span>

<span style="color:#ff669c">53</span>.
<span style="color:#88c425">dig @10.10.11.108 return.local mx</span> :    nos muestra el <span style="color:#ff2dc0">dominio</span> que habíamos <span style="color:#07b4f2">visto</span> (<span style="color:#ecacb6">printer.return.local</span>) y <span style="color:#07b4f2">otro</span> nuevo.
<span style="color:#ecacb6">hostmaster.return.local</span>

Con <span style="color:#ff2dc0">dnsenum</span> <span style="color:#07b4f2">encontramos</span>
<span style="color:#ecacb6">gc._msdcs.return.local</span>
<span style="color:#ecacb6">domaindnszones.return.local</span>
<span style="color:#ecacb6">forestdnszones.return.local</span>
<span style="color:#ecacb6">printer.return.local</span>
<span style="color:#bef202">Volvemos al 80</span>.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#ff2dc0">IIS</span>, <span style="color:#ff2dc0">PHP</span> y dice que es un <span style="color:#07b4f2">panel</span> para <span style="color:#07b4f2">administrar</span> una <span style="color:#07b4f2">impresora</span>.

<span style="color:#ff2dc0">Desde la web</span>.
Vemos <span style="color:#07b4f2">opciones</span> que nos <span style="color:#07b4f2">llevan a otros lados</span>.
<span style="color:#ecacb6">/settings.php</span> :   <span style="color:#07b4f2">información</span> que podemos "<span style="color:#ecacb6">updatear</span>".
<span style="color:#07b4f2">Vemos</span> 
Un <span style="color:#ff2dc0">dominio</span>, <span style="color:#ecacb6">printer.return.local</span>
Usuario <span style="color:#ecacb6">svc-printer</span>
<span style="color:#ff2dc0">Server port 389</span>, <span style="color:#07b4f2">supongo</span> que es para hacer una <span style="color:#ff2dc0">autenticación</span> por <span style="color:#ff669c">389</span>.

Probamos con <span style="color:#ff2dc0">kerbrute</span> y <span style="color:#ecacb6">svc-printer</span> es <span style="color:#07b4f2">válido</span>.
<span style="color:#bef202">Vamos al 53</span>.

Si <span style="color:#07b4f2">intentamos</span> darle a <span style="color:#ecacb6">Update</span>, solo<span style="color:#07b4f2"> se envía</span> por <span style="color:#ff2dc0">POST</span> 
<span style="color:#ecacb6">ip=</span>

En la <span style="color:#ff2dc0">página</span> de <span style="color:#ecacb6">/settings.php</span> <span style="color:#07b4f2">vemos</span> en el <span style="color:#ff2dc0">código fuente</span> al final
<span style="color:#ecacb6">//# sourceURL=pen.js</span>
De momento <span style="color:#07b4f2">no sabemos si sea</span> de <span style="color:#07b4f2">algo</span>.

Tratemos de <span style="color:#07b4f2">aprovechar mejor</span> la <span style="color:#07b4f2">parte</span> del <span style="color:#ecacb6">settings</span>, ponemos <span style="color:#07b4f2">nuestra</span> <span style="color:#ff2dc0">IP</span>.
<span style="color:#ecacb6">Server Address = 10.10.14.15</span>
Nos ponemos en <span style="color:#ff2dc0">escucha</span> por <span style="color:#ff669c">389</span> porque por<span style="color:#07b4f2"> ahí está configurado</span>, <span style="color:#07b4f2">damos</span> a "<span style="color:#ecacb6">Update</span>" y lo que pensamos, <span style="color:#07b4f2">viaja</span> una <span style="color:#ff2dc0">autenticación</span> porque <span style="color:#07b4f2">vemos</span>
![[Pasted image 20241215204248.png]]

<span style="color:#ecacb6">svc-printer:1edFg43012!!</span> 
Las <span style="color:#07b4f2">probamos</span> con <span style="color:#ff2dc0">crackmapexec</span> <span style="color:#07b4f2">sobre</span> el <span style="color:#ff2dc0">smb</span> y son <span style="color:#07b4f2">válidas</span>.
<span style="color:#bef202">Vamos al 445</span>.


<span style="color:#ff669c">445</span>.
Con las <span style="color:#ff2dc0">credenciales</span>
<span style="color:#ecacb6">svc-printer:1edFg43012!!</span>
Tratamos de <span style="color:#07b4f2">listar</span> los <span style="color:#ff2dc0">recursos compartidos a nivel de red</span>, y vemos que <span style="color:#07b4f2">podemos</span> <span style="color:#ff2dc0">leer</span> en <span style="color:#07b4f2">todos</span>, y en <span style="color:#ecacb6">C$</span> podemos <span style="color:#ff2dc0">escribir</span>.
<span style="color:#07b4f2">Interesante</span> que podamos <span style="color:#07b4f2">acceder</span> a <span style="color:#ecacb6">ADMIN$</span> y a <span style="color:#ecacb6">C$</span>, siendo que <span style="color:#07b4f2">estos no son</span> de <span style="color:#ff2dc0">lectura</span> <span style="color:#07b4f2">normalmente</span>. 

Así que probamos a <span style="color:#07b4f2">validar</span> <span style="color:#ff2dc0">credenciales</span> con <span style="color:#ff2dc0">crackmapexec</span> por <span style="color:#ff2dc0">WinRM</span> y nos pone <span style="color:#ff9a57">Pwn3d!</span>, nos podemos <span style="color:#ff2dc0">conectar</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina víctima</span> y pa deeentro.


## Escalada

Enumerar todo el sistema.
<span style="color:#ff2dc0">Host</span>: <span style="color:#ecacb6">10.10.11.108</span>
<span style="color:#ff2dc0">Usuario</span>: <span style="color:#ecacb6">svc-printer</span>

<span style="color:#88c425">whoami /all </span>:    vemos el <span style="color:#ff2dc0">Privilege</span> 
<span style="color:#ecacb6">SeBackupPrivilege</span> 
En <span style="color:#379075">/Vulnerabilidades y Temas/Escalar Privilegios/Windows/Abuso de Permisos/SeBackupPrivilege</span> vemos cómo <span style="color:#07b4f2">aprovecharnos</span>.
Una vez que <span style="color:#07b4f2">tenemos</span> la <span style="color:#ecacb6">sam</span> y el <span style="color:#ecacb6">system</span>, <span style="color:#07b4f2">extraemos</span> los <span style="color:#ff2dc0">secrets</span> y <span style="color:#07b4f2">obtenemos</span> los <span style="color:#ff2dc0">Hashes NTLM</span>, los <span style="color:#07b4f2">validamos</span> con <span style="color:#ff2dc0">crackmapexec</span> pero vemos que <span style="color:#07b4f2">no son válidos</span>.

Al ver nuestra información vemos que <span style="color:#07b4f2">estamos</span> en el <span style="color:#ff2dc0">grupo</span> <span style="color:#ecacb6">Server Operators</span>.
En <span style="color:#379075">/Vulnerabilidades y Temas/Escalar Privilegios/Windows/Abuso de Grupos/Server Operators</span> vemos cómo <span style="color:#07b4f2">aprovecharnos</span>.
<span style="color:#07b4f2">Tratamos</span> de <span style="color:#07b4f2">crear</span> un <span style="color:#ff2dc0">servicio</span> pero <span style="color:#07b4f2">no podemos</span>, así que <span style="color:#07b4f2">configuramos</span> uno <span style="color:#07b4f2">existente alterando</span> su <span style="color:#ff2dc0">path de arranque</span>, <span style="color:#07b4f2">poniendo</span> una <span style="color:#ff2dc0">reverse shell</span>, lo <span style="color:#ff2dc0">detenemos</span> y <span style="color:#07b4f2">volvemos</span> a <span style="color:#ff2dc0">arrancar</span> para <span style="color:#07b4f2">ganar acceso</span> como <span style="color:#ecacb6">NT Authority\System</span> y pa deeentro.
Fin.
