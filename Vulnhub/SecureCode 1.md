# SecureCode 1

Tag: [[_webFuzzing]],[[_infoLeakage]],[[_analysis]],[[_scripting]],[[_webExploitation]]

## Primeros pasos
(Reconocimiento).

Primero le <span style="color:#07b4f2">acomodamos</span> bien<span style="color:#07b4f2"> para que agarre</span> una <span style="color:#ff2dc0">IP</span> (<span style="color:#379075">poner modo Bridged</span>).
Hacemos un <span style="color:#07b4f2">escaneo</span> en la <span style="color:#ff2dc0">red</span> para <span style="color:#07b4f2">determinar</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Mediante</span> el <span style="color:#ff2dc0">OUI</span> <span style="color:#07b4f2">reconocemos</span> la <span style="color:#ff2dc0">máquina</span> que vamos<span style="color:#07b4f2"> a atacar</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Linux</span>.

<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>.
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Bionic</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">80</span> - HTTP


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">80.</span>
<span style="color:#ff2dc0">NMAP</span>.
<span style="color:#ecacb6">/login</span> :    <span style="color:#07b4f2">no</span> tengo <span style="color:#07b4f2">credenciales</span>, en el <span style="color:#07b4f2">código no </span>se ve <span style="color:#07b4f2">mucho</span>.

<span style="color:#ff2dc0">whatweb</span>. Título, <span style="color:#07b4f2">poco</span> más.

<span style="color:#ff2dc0">Desde la web</span>.
"<span style="color:#ecacb6">Coming Soon</span>" y un<span style="color:#07b4f2"> tiempo atrás</span>.
<span style="color:#ff2dc0">Código fuente</span>. 
Possible <span style="color:#07b4f2">Email</span> "<span style="color:#ecacb6">ex@abc.xyz</span>", poco más.

<span style="color:#ff2dc0">fuzzWeb.sh</span> reporta <span style="color:#ff2dc0">301</span>. 
<span style="color:#ecacb6">/login</span> 
Está <span style="color:#ecacb6">/resetPassword.php</span> :    me permite <span style="color:#ff2dc0">enumerar</span> <span style="color:#07b4f2">usuarios</span>. 
<span style="color:#07b4f2">Usuarios válidos</span> del <span style="color:#ff2dc0">login</span>.
<span style="color:#ecacb6">admin</span>

<span style="color:#ecacb6">/profile</span> 
<span style="color:#ecacb6">/users</span> :    tratar con <span style="color:#ff2dc0">BurpSuite</span> de <span style="color:#07b4f2">ver</span> el <span style="color:#07b4f2">cuerpo</span> de la <span style="color:#07b4f2">respuesta</span> al aplicar el <span style="color:#ff2dc0">Redirect</span>., pero <span style="color:#07b4f2">no mucho</span>.

<span style="color:#ecacb6">/item </span>
<span style="color:#ecacb6">/include</span> :    <span style="color:#ff2dc0">directory listing</span>. <span style="color:#ecacb6">header.php</span> y <span style="color:#ecacb6">connection.php</span> no se ven, <span style="color:#379075">si llego a tener un LFI, ya sabemos</span>.
<span style="color:#ecacb6">/asset</span> :    <span style="color:#ff2dc0">directory listing</span>. <span style="color:#07b4f2">Mucho</span> <span style="color:#ff2dc0">código</span>, analizar con un<span style="color:#ff2dc0"> formateador online</span>.

<span style="color:#ff2dc0">Fuzzeando por extensiones.</span>
<span style="color:#ecacb6">/index.php/</span> :    se ve lo de "<span style="color:#ecacb6">Coming Soon</span>" pero <span style="color:#07b4f2">sin estilos</span>.
<span style="color:#ecacb6">/source_code.zip</span> :    <span style="color:#07b4f2">ojito</span>, cosas.

<span style="color:#ff2dc0">Analizar proyecto</span>.
En el archivo de base de datos vemos credenciales, y a su vez, usuarios.
<span style="color:#ecacb6">admin:24b97b1ec42a3deace58636148135f7d</span>
<span style="color:#ecacb6">customer:355509442720e7eaa27d4e2fc8abe95a</span> 

En <span style="color:#ecacb6">checkLogin.php</span> se ve que se <span style="color:#ff2dc0">hashea</span> en <span style="color:#ff2dc0">MD5</span> el <span style="color:#07b4f2">input</span> para hacer la <span style="color:#ff2dc0">query</span> a la <span style="color:#ff2dc0">base de datos</span>, deberíamos <span style="color:#07b4f2">tratar</span> de <span style="color:#ff2dc0">brute forcerarlas</span>. No mucho.

Un <span style="color:#ecacb6">connectio.php</span> donde hay <span style="color:#ff2dc0">credenciales</span> de <span style="color:#07b4f2">acceso</span> a la <span style="color:#ff2dc0">base de datos</span>.
<span style="color:#ecacb6">localhost:hackshop</span>

Hay un <span style="color:#ecacb6">resetPassword.php </span>que como <span style="color:#07b4f2">vimos</span> en la <span style="color:#ff2dc0">web</span>, <span style="color:#07b4f2">manda</span> un <span style="color:#07b4f2">email</span> para el <span style="color:#ff2dc0">reseteo</span> de la <span style="color:#07b4f2">password</span> del usuario.

Si abrimos todos los <span style="color:#07b4f2">archivos</span> en <span style="color:#ecacb6">item/</span>, podemos ver que para<span style="color:#07b4f2"> verse en</span> la <span style="color:#ff2dc0">web</span>, se requiere <span style="color:#07b4f2">estar</span> <span style="color:#ff2dc0">autenticados</span>, pero, el <span style="color:#ecacb6">viewItem.php</span> no<span style="color:#ecacb6">.  _.</span>, <span style="color:#07b4f2">aunque</span> hace <span style="color:#07b4f2">validaciones</span>.

![[Pasted image 20240823211534.png]]
Pero pasa que en la<span style="color:#07b4f2"> línea 18</span>, <span style="color:#ff9a57">ya no encasilla $id entre comillas</span>. Y yo puedo <span style="color:#07b4f2">hacer</span> una <span style="color:#ff2dc0">Query</span> que <span style="color:#07b4f2">no contemple caracteres especiales</span>, porque claro, puedo <span style="color:#07b4f2">hacer</span> la <span style="color:#ff2dc0">petición</span> por <span style="color:#ff2dc0">GET</span> <span style="color:#07b4f2">desde</span> la <span style="color:#ff2dc0">URL</span>.

Algo como
<span style="color:#ecacb6">http://192.168.0.15/item/viewItem.php?id=1 and sleep(5)</span> :    y sí, <span style="color:#07b4f2">tarda 5 segundos</span> en <span style="color:#07b4f2">responder</span>.

<span style="color:#07b4f2">No</span> la haremos<span style="color:#ff2dc0"> basada en tiempo</span>, sino <span style="color:#07b4f2">a</span> <span style="color:#ff2dc0">ciegas</span> (<span style="color:#379075">Based Blind</span>).
Lo bueno es que <span style="color:#07b4f2">tenemos información importante</span> de la <span style="color:#ff2dc0">base de datos</span> gracias al <span style="color:#ecacb6">.zip</span>

Nos copiamos la base de datos, vamos a un <span style="color:#ff2dc0">mysql interactivo</span> en <span style="color:#ff2dc0">internet</span>. Podríamos pensar en una <span style="color:#ff2dc0">query</span> <span style="color:#07b4f2">válida</span> para conseguir el token del usuario, y apoyarnos del <span style="color:#ecacb6">resetPassword.php</span> para lograr <span style="color:#07b4f2">cambiarle</span> la <span style="color:#ff2dc0">password</span>.
Pero <span style="color:#07b4f2">antes</span>, teniendo en cuenta que <span style="color:#07b4f2">tenemos</span> un <span style="color:#ff2dc0">backup</span>, trataremos de <span style="color:#07b4f2">obtener</span> la <span style="color:#07b4f2">contraseña</span> de <span style="color:#ecacb6">admin</span>.
<span style="color:#bef202">Query</span>.
La <span style="color:#07b4f2">igualdad</span> de la <span style="color:#ff2dc0">query</span> será <span style="color:#07b4f2">en</span> <span style="color:#ff2dc0">decimal</span>, porque <span style="color:#07b4f2">no</span> podemos <span style="color:#07b4f2">utilizar comillas</span>.
<span style="color:#ecacb6">select (select ascii(substring(username,1,1)) from user limit 0,1)=97;</span>

Así que hagamos un <span style="color:#ff2dc0">script</span> en <span style="color:#ff2dc0">python</span> para <span style="color:#07b4f2">realizar</span> la <span style="color:#ff2dc0">inyección</span>. Tratando de <span style="color:#07b4f2">ir a por</span> <span style="color:#ecacb6">admin</span>.

Conseguimos su <span style="color:#07b4f2">nombre</span>, <span style="color:#07b4f2">ahora</span> su <span style="color:#ecacb6">password</span>.
![[Pasted image 20240823231408.png]]

<span style="color:#ecacb6">unaccessableuntilyouchangeme</span> -> <span style="color:#07b4f2">no</span> me <span style="color:#07b4f2">deja</span> en el <span style="color:#ecacb6">login</span>, pero <span style="color:#07b4f2">refuerza</span> la <span style="color:#07b4f2">idea</span> de que debemos <span style="color:#07b4f2">cambiarla</span>.
<span style="color:#ff2dc0">Token</span> de <span style="color:#ecacb6">admin</span>: <span style="color:#ecacb6">PVW42ic1PSzaER4</span> :    <span style="color:#07b4f2">cambia</span> cuando le <span style="color:#07b4f2">doy</span> en <span style="color:#07b4f2">cambiar</span> <span style="color:#ecacb6">contraseña</span>.

En el archivo veo que <span style="color:#07b4f2">usan</span> la <span style="color:#ff2dc0">función</span> de <span style="color:#ecacb6">gethostname()</span>, pero yo <span style="color:#07b4f2">no sé cuál es</span> el <span style="color:#07b4f2">nombre</span> del <span style="color:#ff2dc0">Host</span>. Lo <span style="color:#07b4f2">consigo en</span> el <span style="color:#ff2dc0">SQLI</span>, pero <span style="color:#07b4f2">no hace falta</span>.
Podemos <span style="color:#07b4f2">entrar</span> al <span style="color:#07b4f2">apartado</span> de <span style="color:#07b4f2">cambiar</span> la <span style="color:#ecacb6">contraseña</span>, se la <span style="color:#07b4f2">cambiamos</span>.
<span style="color:#ecacb6">admin:password123</span>
Y <span style="color:#07b4f2">entramos</span> con la nueva contraseña en el <span style="color:#ff2dc0">Login</span>.

<span style="color:#ff2dc0">Enumerar todo el aplicativo</span>.
<span style="color:#ecacb6">/addItem.php</span> :    puedo<span style="color:#07b4f2"> subir imagen</span>.
Pero se ve que <span style="color:#07b4f2">no acepta</span> <span style="color:#ff2dc0">PHP</span>.
![[Pasted image 20240824011448.png]]

<span style="color:#ecacb6">/newItem.php</span> :    el <span style="color:#ff2dc0">form</span> <span style="color:#07b4f2">se envía </span>a <span style="color:#07b4f2">esto</span>.
![[Pasted image 20240824013902.png]]
<span style="color:#379075">Toca encontrar un vector de ataque</span>.

<span style="color:#07b4f2">También</span> está
<span style="color:#ecacb6">/updateItem.php</span> :    es para cuando se <span style="color:#07b4f2">actualiza</span> un <span style="color:#07b4f2">item</span>.
![[Pasted image 20240824024353.png]]
¿<span style="color:#07b4f2">Diferencia</span>?, <span style="color:#07b4f2">no</span> me <span style="color:#07b4f2">checa</span> por el <span style="color:#ecacb6">Content-Type</span>.

Así que al <span style="color:#07b4f2">hacerle</span> <span style="color:#ff2dc0">Update</span> a un <span style="color:#ff2dc0">item</span> se me <span style="color:#07b4f2">acomoda mejor</span>.  Pudiendo así <span style="color:#07b4f2">subir</span> un <span style="color:#ff2dc0">PHP</span>, pero con la <span style="color:#ff2dc0">extensión .phar</span>
Hago una <span style="color:#ff2dc0">reverse shell</span> y pa deeeeentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Bionic.

<span style="color:#ff9a57">Importante</span>.
<span style="color:#07b4f2">No contempla </span><span style="color:#ff2dc0">escalada</span>, puesto que la <span style="color:#ff2dc0">máquina</span> está <span style="color:#07b4f2">destinada</span> a <span style="color:#07b4f2">practicar</span> para el <span style="color:#ff2dc0">OSWE</span>, el cual se <span style="color:#07b4f2">basa</span> en <span style="color:#ff2dc0">Análisis de Código</span>. Claramente <span style="color:#07b4f2">también</span> <span style="color:#ff2dc0">hacking web</span>.
