# Infovore 1

Tags: [[_webFuzzing]],[[_lfi]],[[_raceCondition]],[[_cracking]],[[_permissionAbuse]]

## Primeros pasos
(Reconocimiento).

Primero le <span style="color:#07b4f2">acomodamos</span> bien<span style="color:#07b4f2"> para que agarre</span> una <span style="color:#ff2dc0">IP</span> (<span style="color:#379075">poner modo Bridged</span>).
Hacemos un <span style="color:#07b4f2">escaneo</span> en la <span style="color:#ff2dc0">red</span> para <span style="color:#07b4f2">determinar</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Mediante</span> el <span style="color:#ff2dc0">OUI</span> <span style="color:#07b4f2">reconocemos</span> la <span style="color:#ff2dc0">máquina</span> que vamos<span style="color:#07b4f2"> a atacar</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Linux</span>.

<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>.
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Debian Buster</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">80</span> - HTTP Apache.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#07b4f2">Trabaja</span> con <span style="color:#ff2dc0">PHP</span>, y poco más. Título: "Include me ..."
Desde la <span style="color:#ff2dc0">web</span>. <span style="color:#07b4f2">Analizar</span> a fondo las <span style="color:#07b4f2">funcionalidades</span>. Pero no son muchas.
<span style="color:#ff2dc0">Código fuente</span>. No dice mucho.

Fuzzeemos.
<span style="color:#ecacb6">/index.php</span> :    es el principal.
<span style="color:#ecacb6">/info.php</span> :    es el <span style="color:#07b4f2">info</span> de <span style="color:#ff2dc0">PHP</span>.

El Título me hace pensar en un <span style="color:#ff2dc0">File Inclusion</span>, ya sea <span style="color:#ff2dc0">Remote</span> or <span style="color:#ff2dc0">Local</span>.
Podría haber un parámetro configurado en <span style="color:#ecacb6">/info.php?param=/etc/passwd</span> :    tratemos de fuzzearlo.
Lo encontramos -> <span style="color:#ecacb6">http://192.168.0.24/index.php?filename=/etc/passwd</span>

Tratemos de <span style="color:#07b4f2">enumerar</span> y ver si lo podemos <span style="color:#07b4f2">derivar a</span> un <span style="color:#ff2dc0">RCE</span>.
El <span style="color:#ff2dc0">LFI</span> está <span style="color:#07b4f2">limitado</span>. <span style="color:#07b4f2">Tratemos</span> de hacer un <span style="color:#ff2dc0">Log Poisoning</span>. de <span style="color:#ff2dc0">Apache</span>, pero no los veo.


En el <span style="color:#ecacb6">info.php</span> vemos <span style="color:#ecacb6">file_uploads</span> que <span style="color:#07b4f2">está</span> en "<span style="color:#07b4f2">On</span>". No es algo bueno, porque podemos <span style="color:#07b4f2">intentar forzar por</span> <span style="color:#ff2dc0">POST</span> una <span style="color:#07b4f2">petición</span> al <span style="color:#ecacb6">info.php</span> donde intentemos <span style="color:#07b4f2">crear</span> una <span style="color:#07b4f2">estructura</span> de <span style="color:#07b4f2">archivo</span> que me apetezca.

<span style="color:#07b4f2">Interceptamos</span> la <span style="color:#07b4f2">petición</span> del <span style="color:#ecacb6">info.php</span>
Tengamos en cuenta que <span style="color:#07b4f2">debe acontecerse</span> una <span style="color:#ff2dc0">Race Condition</span>.

<span style="color:#bef202">Primeramente</span>.
1.- Cambiamos la <span style="color:#07b4f2">petición</span> a <span style="color:#ff2dc0">POST</span>.
2.- Intentar <span style="color:#07b4f2">crear</span> un <span style="color:#07b4f2">archivo</span>. Buscamos "file upload boundary". https://stackoverflow.com/questions/3508338/what-is-the-boundary-in-multipart-form-data
<span style="color:#07b4f2">Quitamos</span> <span style="color:#ecacb6">Content-Length</span> y <span style="color:#07b4f2">ponemos</span> <span style="color:#ecacb6">Content-Type: multipart/form-data; boundary=???</span>, etc. Queda así
![[Pasted image 20240809215434.png]]
<span style="color:#379075">Content-Length se puso al enviar la petición</span>.

Cuando <span style="color:#07b4f2">filtramos</span> por "<span style="color:#ecacb6">tmp_name</span>" podemos ver <span style="color:#07b4f2">cosas</span>.
![[Pasted image 20240809215511.png]]

Si, <span style="color:#07b4f2">intercepto</span>, <span style="color:#07b4f2">cambio</span> a <span style="color:#ff2dc0">POST</span>, <span style="color:#07b4f2">copio</span> y <span style="color:#07b4f2">pego</span> la <span style="color:#07b4f2">estructura</span> anterior, le <span style="color:#07b4f2">doy</span> a <span style="color:#ff2dc0">forward</span> y <span style="color:#07b4f2">dejo interceptar</span>, podemos <span style="color:#07b4f2">verlo</span> en la <span style="color:#ff2dc0">web</span>.
![[Pasted image 20240809215802.png]]

Lo malo es que <span style="color:#07b4f2">son archivos temporales</span>, no es tan fácil hacerlo rápido e ir a verlo.
Así que <span style="color:#07b4f2">debemos hacer </span>una <span style="color:#ff2dc0">Race Condition</span> para <span style="color:#07b4f2">ejecutarlo muchas veces</span>, hasta que <span style="color:#07b4f2">se de</span> la <span style="color:#ff2dc0">condición</span>, y lo <span style="color:#07b4f2">ejecutemos</span> casi al <span style="color:#07b4f2">instante</span> de <span style="color:#07b4f2">subirlo</span>.

En <span style="color:#ff2dc0">HackTricks</span>, https://book.hacktricks.xyz/pentesting-web/file-inclusion/lfi2rce-via-phpinfo 
Vamos al <span style="color:#07b4f2">primer link</span>, es un <span style="color:#ff2dc0">script</span> de <span style="color:#ff2dc0">python</span> el cual descargamos (<span style="color:#379075">noa automatizará la Race Condition; apuntar y ejecutar</span>).
Hacemos cambios
![[Pasted image 20240809221520.png]]

Ponemos <span style="color:#07b4f2">dónde</span> está el <span style="color:#ff2dc0">info PHP</span>
![[Pasted image 20240809221552.png]]

Ponemos <span style="color:#07b4f2">dónde</span> está el <span style="color:#ff2dc0">LFI</span>
![[Pasted image 20240809221707.png]]

Nos ponemos <span style="color:#07b4f2">en escucha</span> y lo <span style="color:#07b4f2">ejecutamos</span>.
<span style="color:#88c425">python2.7 phpinfolfi.py 192.168.0.24 80</span> 
De primeras da un <span style="color:#07b4f2">error</span>. Debemos arreglarlo, nos lo <span style="color:#07b4f2">explican</span> en <span style="color:#ff2dc0">HackTricks</span>.

--- 
<span style="color:#bef202">Veamos cuál es el problema</span>.
En <span style="color:#ff2dc0">BurpSuite</span>.
Todo <span style="color:#07b4f2">lo que me llegue</span> por el <span style="color:#ff669c">4646</span>
![[Pasted image 20240809223130.png]]

Quiero <span style="color:#07b4f2">redirigirlo</span> a la <span style="color:#ff2dc0">máquina víctima</span> por el <span style="color:#ff669c">puerto 80</span>.
![[Pasted image 20240809223212.png]]

Así nos <span style="color:#07b4f2">lanzamos</span> el <span style="color:#ff2dc0">script</span> a <span style="color:#07b4f2">nosotros</span> mientras <span style="color:#07b4f2">interceptamos</span>.
<span style="color:#88c425">python2.7 phpinfolfi.py 127.0.0.1 4646</span> 
Podemos ver que <span style="color:#07b4f2">mete</span> muchas '<span style="color:#ecacb6">A</span>' en todos lados.

Pero el "<span style="color:#ecacb6">tmp_name</span>" sí <span style="color:#07b4f2">está</span>, a pesar de que el <span style="color:#ff2dc0">script</span> <span style="color:#07b4f2">diga que no</span>. Si lo revisamos, lo representa así
![[Pasted image 20240809224022.png]]
Pero no hay un "<span style="color:#ecacb6">=></span>" en la <span style="color:#07b4f2">respuesta</span> del <span style="color:#ff2dc0">servidor</span>.
Está como "=&gt" (<span style="color:#379075">que es otra forma de representarlo</span>), así que <span style="color:#07b4f2">lo sustituimos</span> ahí y en donde aparezca de nuevo, solo son 2 veces.

Lo <span style="color:#07b4f2">ejecutamos</span> de nuevo, y pa deeentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Debian Buster.

Parece que <span style="color:#07b4f2">estamos</span> en un <span style="color:#ff2dc0">contenedor</span>. Tratemos de <span style="color:#07b4f2">escapar</span>, quiero pensar que la <span style="color:#ecacb6">192.168.150.1 </span>es la <span style="color:#ff2dc0">máquina real</span>.

Ya visto de todo,  desde procesos, puertos internos, etc. Usemos <span style="color:#ff2dc0">linPEAS</span>, está en <span style="color:#ff2dc0">github</span>.
El <span style="color:#07b4f2">contenedor tiene conexión</span> a <span style="color:#ff2dc0">internet</span>, usamos curl. (<span style="color:#379075">No funcionó</span>).

En la <span style="color:#07b4f2">raíz</span> del <span style="color:#07b4f2">sistema</span> se ve un <span style="color:#07b4f2">archivo</span> <span style="color:#ecacb6">.tgz</span> oculto.
Lo <span style="color:#07b4f2">copiamos</span> en <span style="color:#ecacb6">/tmp</span> y <span style="color:#07b4f2">descomprimimos</span>.
Nos da una <span style="color:#ff2dc0">clave privada</span> y <span style="color:#ff2dc0">púbica</span> de <span style="color:#ff2dc0">root</span> para <span style="color:#ff2dc0">SSH</span>, pero la <span style="color:#ff2dc0">privada</span> <span style="color:#07b4f2">está</span> <span style="color:#ff2dc0">cifrada</span>.
De igual manera, <span style="color:#07b4f2">no</span> está <span style="color:#07b4f2">activo</span> el <span style="color:#ff2dc0">servicio</span> de <span style="color:#ff2dc0">SSH</span> en la <span style="color:#ff2dc0">máquina víctima</span>.

Pero la <span style="color:#ff2dc0">máquina real</span> (<span style="color:#379075">.1</span>), <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">segmento</span> en que <span style="color:#07b4f2">estamos</span>, <span style="color:#07b4f2">puede que sí</span> tenga el <span style="color:#ff2dc0">SSH activado</span>, así que desde el contenedor, <span style="color:#07b4f2">enumeremos enviando</span> una<span style="color:#07b4f2"> cadena vacía </span>al <span style="color:#ecacb6">/dev/tcp/192.168.150.1/22</span> y está abierto.

Entonces si logro <span style="color:#07b4f2">romper</span> la<span style="color:#ff2dc0"> clave privada</span>, la puedo <span style="color:#07b4f2">utilizar</span> para <span style="color:#07b4f2">conectarme</span> por ahí. Primero <span style="color:#07b4f2">obtenemos</span> el <span style="color:#ff2dc0">hash</span> con <span style="color:#ff2dc0">ssh2john</span>, luego lo <span style="color:#07b4f2">rompemos</span> con <span style="color:#ff2dc0">john</span>. La contraseña de la clave es <span style="color:#ecacb6">choclate93</span>.

Primero <span style="color:#07b4f2">intentamos migrar</span> a <span style="color:#ff2dc0">root</span> con esa <span style="color:#07b4f2">contraseña</span> para <span style="color:#07b4f2">ver</span> si se <span style="color:#07b4f2">reutiliza</span>, y <span style="color:#07b4f2">sí</span>.

<span style="color:#07b4f2">En</span> el <span style="color:#ecacb6">.ssh</span> vemos que <span style="color:#07b4f2">hay</span> una <span style="color:#ecacb6">id_rsa</span> <span style="color:#ff2dc0">cifrada</span> y una <span style="color:#ecacb6">.pub</span>, <span style="color:#07b4f2">esta última</span> nos dice que podemos <span style="color:#07b4f2">conectarnos</span> como <span style="color:#ecacb6">admin@192.168.150.1</span>
Así que hay que <span style="color:#07b4f2">usar</span> la <span style="color:#ecacb6">id_rsa</span> como <span style="color:#ff2dc0">fichero de identidad</span> y entrar <span style="color:#07b4f2">como</span> <span style="color:#ecacb6">admin</span> a la <span style="color:#ff2dc0">máquina real</span> <span style="color:#07b4f2">por</span> dicha <span style="color:#ff2dc0">interfaz</span>.
La <span style="color:#07b4f2">rompemos</span> para <span style="color:#07b4f2">conseguir</span> la <span style="color:#07b4f2">contraseña</span> (<span style="color:#379075">es la misma que la de la anterior clave</span>).

Nos intentamos <span style="color:#07b4f2">conectar con</span> la<span style="color:#ecacb6"> id_rsa</span>, <span style="color:#07b4f2">pegamos</span> la <span style="color:#07b4f2">contraseña</span> y nos <span style="color:#07b4f2">deja</span>.


<span style="color:#bef202">En la máquina real</span>.
Ahora toca <span style="color:#07b4f2">elevar a</span> <span style="color:#ff2dc0">root</span>.
Estamos <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">grupo Docker</span>, tratemos de <span style="color:#07b4f2">crear</span> un nuevo <span style="color:#ff2dc0">contenedor</span> para <span style="color:#07b4f2">volvernos</span> <span style="color:#ff2dc0">root</span>.
Lo <span style="color:#07b4f2">creamos</span>, <span style="color:#07b4f2">haciendo</span> una <span style="color:#ff2dc0">montura</span> <span style="color:#07b4f2">de</span> la <span style="color:#07b4f2">raíz</span> de la <span style="color:#ff2dc0">máquina real</span> <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">contenedor</span>, para que al <span style="color:#07b4f2">entrar</span> pueda <span style="color:#07b4f2">ir a </span>la <span style="color:#ff2dc0">montura</span> y <span style="color:#07b4f2">cambiarle</span> el <span style="color:#07b4f2">privilegio</span> a la <span style="color:#ff2dc0">bash</span> (<span style="color:#379075">aunque ya estemos como root</span>).

Después <span style="color:#07b4f2">salgo</span> hago un <span style="color:#88c425">bash -p</span> y pa deeeeentro.

FIN.
