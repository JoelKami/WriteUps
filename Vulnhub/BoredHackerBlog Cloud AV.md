# BoredHackerBlog Cloud AV

Tag: [[_sqli]].[[_scripting]],[[_commandInjection]],[[_suidAbusing]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Bionic</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH 7.6
<span style="color:#ff669c">80</span> - HTTP Apache.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
Versión <span style="color:#07b4f2">vulnerable</span> a <span style="color:#ff2dc0">User Enumeration</span>.
Luego <span style="color:#07b4f2">probamos</span> cuando <span style="color:#07b4f2">tengamos usuarios</span> a probar.


<span style="color:#ff669c">8080</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#ecacb6">Python 2.7</span>, <span style="color:#ecacb6">Werkzeug</span>. 
<span style="color:#07b4f2">Desde</span> la <span style="color:#ff2dc0">web</span>. 
<span style="color:#ff2dc0">Código fuente</span> <span style="color:#07b4f2">no</span> dice <span style="color:#07b4f2">mucho</span>.
Parece que es un <span style="color:#07b4f2">escaneador</span> de <span style="color:#ff2dc0">Virus</span>, es una <span style="color:#07b4f2">beta</span> del <span style="color:#07b4f2">servicio</span>. Nos <span style="color:#07b4f2">pide</span> un <span style="color:#ff2dc0">código de invitación</span>.

Cuando intento <span style="color:#07b4f2">meter</span> un <span style="color:#ff2dc0">código</span>, me dice "<span style="color:#ecacb6">wrong information</span>".
<span style="color:#ff2dc0">SQLI</span> <span style="color:#07b4f2">no</span> parece <span style="color:#07b4f2">funcionar</span>.

<span style="color:#ff2dc0">Fuzzeemos</span> para encontrar <span style="color:#07b4f2">algo</span>.
<span style="color:#ecacb6">/login</span> :    nos dice que el <span style="color:#07b4f2">método</span> de la <span style="color:#ff2dc0">request</span> <span style="color:#07b4f2">no</span> está <span style="color:#07b4f2">habilitado</span>. 
<span style="color:#ecacb6">/scan</span> :    es el mismo que <span style="color:#07b4f2">tenemos</span>.
<span style="color:#ecacb6">/console </span>:    es una <span style="color:#07b4f2">consola</span> de <span style="color:#ff2dc0">python</span>. Nos <span style="color:#07b4f2">pide</span> un <span style="color:#ff2dc0">PIN</span>. Si <span style="color:#07b4f2">cambio</span> a <span style="color:#ff2dc0">POST</span>, ocasiono un "<span style="color:#07b4f2">error</span>", lo <span style="color:#07b4f2">dice</span> el <span style="color:#ff2dc0">código</span>.
<span style="color:#ecacb6">/output</span> :    nos dice que el <span style="color:#07b4f2">método</span> de la <span style="color:#ff2dc0">request</span> <span style="color:#07b4f2">no</span> está <span style="color:#07b4f2">habilitado</span>.  Si <span style="color:#07b4f2">cambio</span> a <span style="color:#ff2dc0">POST</span>, me <span style="color:#07b4f2">redirige</span> a <span style="color:#ecacb6">/scan</span>

<span style="color:#ecacb6">/scan</span> probé con<span style="color:#07b4f2"> comillas dobles</span>, y ocasiono un <span style="color:#07b4f2">error</span>. <span style="color:#07b4f2">Posible</span> <span style="color:#ff2dc0">SQLI</span>. Efectivamente, hice <span style="color:#ecacb6"> test" or 1=1-- -</span> y <span style="color:#07b4f2">entro</span>.

---
<span style="color:#379075">Nota</span>. Podría <span style="color:#ff2dc0">fuzzear</span> con <span style="color:#ff2dc0">wfuzz</span>, caracteres especiales para ver <span style="color:#07b4f2">con cuáles truena</span>, (<span style="color:#379075">hay que ver cómo se envía la data</span>
<span style="color:#88c425">wfuzz -c -w /usr/share/SecLists/Fuzzing/special-chars.txt -d 'password=FUZZ' -u http://192.168.0.30:8080/login</span>
).
<span style="color:#379075">Nota</span>. Podría también <span style="color:#07b4f2">conseguir</span> el/los <span style="color:#ff2dc0">códigos</span> de <span style="color:#07b4f2">invitación enumerando</span> las <span style="color:#ff2dc0">bases de datos</span>. Practicamos <span style="color:#ff2dc0">SQLI Boolean Bind Based</span>. <span style="color:#07b4f2">Recordemos</span> que es <span style="color:#ff2dc0">SQLite</span>.
<span style="color:#ff2dc0">Script</span> <span style="color:#07b4f2">realizado</span>
![[Pasted image 20240811155600.png]]
<span style="color:#379075">Consiguiendo más códigos</span>.
<span style="color:#ecacb6">myinvitecode123</span>,<span style="color:#ecacb6">mysecondinvitecode</span>,<span style="color:#ecacb6"><span style="color:#ecacb6">cloudavtech</span></span>,<span style="color:#ecacb6">mostsecurescanner</span>

---

<span style="color:#bef202">Enumerar todo</span>.
Usuario "<span style="color:#ecacb6">scanner</span>".
<span style="color:#07b4f2">Pide</span> un <span style="color:#07b4f2">archivo</span>, pero si le <span style="color:#07b4f2">meto punto y coma</span>, puedo <span style="color:#07b4f2">ejecutar</span> otro <span style="color:#07b4f2">comando</span> (<span style="color:#379075">como cat /etc/passwd</span>).
Usuarios "<span style="color:#ecacb6">cloudav</span>", "<span style="color:#ecacb6">scanner</span>".

Probemos <span style="color:#ff2dc0">RCE</span>, <span style="color:#07b4f2">sino</span> seguimos <span style="color:#07b4f2">enumerando</span>.
Ejecuto una <span style="color:#ff2dc0">Reverse Shell </span>y pa deeentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Bionic.

<span style="color:#ff669c">Puertos internos</span>.
<span style="color:#ff669c">53</span>
<span style="color:#ff669c">22</span>
<span style="color:#ff669c">8080</span>
<span style="color:#ff669c">54096</span>

Vemos un <span style="color:#07b4f2">archivo</span> en <span style="color:#ff2dc0">C</span>, en el <span style="color:#ecacb6">home</span> <span style="color:#07b4f2">de</span> <span style="color:#ecacb6">scanner</span>.
Hace una <span style="color:#07b4f2">movidas</span>, <span style="color:#07b4f2">reserva memoria</span> para el <span style="color:#07b4f2">comando</span>, y luego <span style="color:#07b4f2">usa</span> las <span style="color:#07b4f2">funciones</span>
<span style="color:#ecacb6">setgid(0);</span>
<span style="color:#ecacb6">setuid(0);</span>
Para posterior <span style="color:#07b4f2">ejecutar</span> con <span style="color:#ff2dc0">system</span> el <span style="color:#07b4f2">comando</span>, pero desde<span style="color:#07b4f2"> antes junta</span> la <span style="color:#07b4f2">utilidad con</span> el <span style="color:#07b4f2">argumento</span> que le <span style="color:#07b4f2">pasamos</span> al <span style="color:#07b4f2">programa</span> en <span style="color:#ff2dc0">C</span>, entonces podemos <span style="color:#07b4f2">englobarlo</span> entre <span style="color:#07b4f2">comillas</span>, jugar con un <span style="color:#07b4f2">punto y coma</span>, y <span style="color:#07b4f2">asignale</span> el <span style="color:#07b4f2">permiso</span> <span style="color:#ff2dc0">SUID</span> a la <span style="color:#ff2dc0">bash</span>.

<span style="color:#88c425">bash -p</span> y pa deeeeentro.
FIN.
