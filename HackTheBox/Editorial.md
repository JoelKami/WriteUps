# Editorial

Tags: [[_webFuzzing]],[[_webExploitation]],[[_infoLeakage]],[[_sudoersAbusing]]

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
<span style="color:#ff669c">80</span> - HTTP. <span style="color:#379075">Nginx 1.18.0</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. Aplica <span style="color:#ff2dc0">redirect</span>, no conoce <span style="color:#ecacb6">editorial.htb</span>, así que <span style="color:#07b4f2">lo metemos en</span> <span style="color:#ecacb6">/etc/hosts</span>
Aplicamos de nuevo, y <span style="color:#07b4f2">no</span> dice <span style="color:#07b4f2">mucho</span>.

<span style="color:#ff2dc0">Desde la web</span>.
Posible <span style="color:#07b4f2">users</span>.
<span style="color:#ecacb6">John Kat</span>
<span style="color:#ecacb6">Stephen K.</span>
<span style="color:#ecacb6">José Sara
</span>

Otro <span style="color:#07b4f2">posible</span> <span style="color:#ff2dc0">subdominio</span>,<span style="color:#ecacb6"> tiempoarriba.htb</span>

<span style="color:#ff2dc0">Recursos</span>.
<span style="color:#ecacb6">/upload</span> :    hay un <span style="color:#07b4f2">apartado</span> de <span style="color:#ff2dc0">subir</span> <span style="color:#07b4f2">libros</span>, lo van a checar. <span style="color:#07b4f2">Coloqué</span> <span style="color:#ff2dc0">datos</span> y <span style="color:#ff2dc0">subí</span> un <span style="color:#ff2dc0">cmd.php</span> y me <span style="color:#07b4f2">deja</span>, pero <span style="color:#07b4f2">no sé</span> si <span style="color:#07b4f2">se guarde</span>. Vemos con <span style="color:#ff2dc0">BurpSuite</span>, y al <span style="color:#07b4f2">parecer no se envía</span> el <span style="color:#ff2dc0">archivo</span>.

<span style="color:#ff2dc0">Fuzzeemos por URIs</span>.
No mucho.

<span style="color:#ff2dc0">Fuzeemos por subdominios</span>.
Pero no encontramos.

Algo me dice que debemos <span style="color:#ff2dc0">reventar</span> la <span style="color:#ff2dc0">funcionalidad</span> de <span style="color:#ff2dc0">subir</span> nuestro <span style="color:#07b4f2">libro</span>.
<span style="color:#07b4f2">Volvemos</span> a <span style="color:#ecacb6">/upload</span> 
Al <span style="color:#ff2dc0">cargar</span> un <span style="color:#ff2dc0">archivo</span> y <span style="color:#07b4f2">dar</span> a "<span style="color:#ecacb6">Preview</span>", podemos ver que <span style="color:#07b4f2">lo guarda</span> y lo <span style="color:#07b4f2">pone</span> como <span style="color:#ff2dc0">icono</span>.
Al <span style="color:#07b4f2">dar</span> en <span style="color:#ecacb6">ver en nueva ventana</span>, lo <span style="color:#ff2dc0">descarga</span>, pero <span style="color:#07b4f2">no</span> pasa <span style="color:#07b4f2">mucho</span>.
Sin embargo, <span style="color:#07b4f2">si colocamos </span>una "<span style="color:#ff2dc0">Cover URL</span>" (<span style="color:#379075">nuestro server</span>) y <span style="color:#07b4f2">damos</span> a <span style="color:#ecacb6">preview</span>, <span style="color:#07b4f2">nos llega</span> un <span style="color:#ff2dc0">GET</span>.

Podemos ver con <span style="color:#88c425">curl -v</span> de la <span style="color:#ff2dc0">URL</span> de la <span style="color:#07b4f2">imagen</span> cuando le doy a "<span style="color:#ecacb6">Preview</span>", <span style="color:#07b4f2">lo trata como</span> un <span style="color:#ecacb6">application/octet-stream</span>, por lo que<span style="color:#07b4f2"> no debería</span> de <span style="color:#ff2dc0">interpretar</span> cosas y al<span style="color:#07b4f2"> final solo se ve</span> el <span style="color:#07b4f2">contenido</span>.

En lugar de montar un server http,<span style="color:#07b4f2"> nos ponemos</span> en <span style="color:#ff2dc0">escucha</span> con <span style="color:#ff2dc0">netcat</span> por el <span style="color:#ff669c">puerto 80</span> y <span style="color:#07b4f2">vemos</span> que en la <span style="color:#ff2dc0">cabecera</span> <span style="color:#ecacb6">User-Agent</span> viene <span style="color:#ecacb6">python-requests/2.25.1</span>. Lo que quiere decir que está <span style="color:#07b4f2">usando</span> la <span style="color:#ff2dc0">librería</span> de <span style="color:#ff2dc0">python</span> para <span style="color:#07b4f2">hacer</span> las <span style="color:#ff2dc0">peticiones</span>.
Lo que el <span style="color:#ff2dc0">servidor</span> <span style="color:#07b4f2">hace</span> podría <span style="color:#07b4f2">verse como</span> esto
<span style="color:#88c425">python3 -c 'import requests; requests.get("http://10.10.14.192")'</span> :    lo <span style="color:#07b4f2">probamos</span> y <span style="color:#07b4f2">se ve igual</span>.

<span style="color:#07b4f2">Concluimos</span> que puede <span style="color:#ff2dc0">interpretar Python</span>, o <span style="color:#07b4f2">al menos con</span> las <span style="color:#ff2dc0">peticiones</span>.
Probamos <span style="color:#ff2dc0">inyectar comandos</span>, pero <span style="color:#07b4f2">nada</span>.


<span style="color:#07b4f2">Pensando</span> que <span style="color:#07b4f2">no</span> podemos <span style="color:#07b4f2">hacer mucho</span> al <span style="color:#07b4f2">lanzarnos</span> <span style="color:#ff2dc0">peticiones</span>, podríamos <span style="color:#07b4f2">tratar</span> de <span style="color:#07b4f2">aprovechar mejor </span>el <span style="color:#ff2dc0">SSRF</span>.
<span style="color:#07b4f2">Probamos</span> con el <span style="color:#ecacb6">127.0.0.1</span> de la <span style="color:#ff2dc0">máquina víctima</span>, al <span style="color:#07b4f2">igual</span> que <span style="color:#07b4f2">varios</span> <span style="color:#ff669c">puertos</span> pero siempre <span style="color:#07b4f2">devuelve</span> lo <span style="color:#07b4f2">mismo</span>, la <span style="color:#07b4f2">imagen</span> por defecto.

Podríamos <span style="color:#07b4f2">tratar</span> de <span style="color:#ff2dc0">fuzzear</span> <span style="color:#ff669c">puertos</span> y ver <span style="color:#07b4f2">qué cambia</span>.
Con <span style="color:#ff2dc0">ffuf</span>
<span style="color:#88c425">ffuf -w dic.txt -request data.req -u http://editorial.htb/upload-cover -fs 61</span>
<span style="color:#ecacb6">dict.txt</span> tiene los <span style="color:#ff669c">puertos</span> a <span style="color:#ff2dc0">fuzzear</span>.
<span style="color:#ecacb6">data.req </span>es la <span style="color:#ff2dc0">petición</span> que <span style="color:#07b4f2">copié</span> de <span style="color:#ff2dc0">BurpSuite</span>, y <span style="color:#07b4f2">puse</span> <span style="color:#ff2dc0">FUZZ</span> como el <span style="color:#ff669c">puerto</span>.
<span style="color:#ecacb6">upload-cover</span> es a <span style="color:#07b4f2">donde se manda</span> la <span style="color:#07b4f2">información</span> por <span style="color:#ff2dc0">POST</span>.
<span style="color:#ecacb6">-fs 61</span> para que me <span style="color:#07b4f2">quite</span> las que tengan <span style="color:#07b4f2">ese tamaño</span> como <span style="color:#ff2dc0">respuesta</span>.

Y nos aparece que el <span style="color:#ff669c">5000</span> nos <span style="color:#07b4f2">devuelve algo</span>, y sí, devuelve <span style="color:#07b4f2">una</span> <span style="color:#ff2dc0">ruta</span>, la copiamos y pegamos en la página y nos <span style="color:#07b4f2">descargamos</span> un <span style="color:#ff2dc0">archivo</span> con <span style="color:#ff2dc0">data en JSON</span>. <span style="color:#07b4f2">Parece</span> que hay un <span style="color:#ff2dc0">API</span> por <span style="color:#07b4f2">detrás</span>.
Podemos ver <span style="color:#07b4f2">varios</span> <span style="color:#ff2dc0">endpoints</span>, solo <span style="color:#07b4f2">algunos devuelven cosas</span> interesantes
<span style="color:#ecacb6">/api/latest/metadata/messages/coupons</span>
<span style="color:#ecacb6">/api/latest/metadata/messages/authors</span> :    <span style="color:#ff2dc0">credenciales</span>
<span style="color:#ecacb6">dev:dev080217_devAPI!@</span> -> <span style="color:#07b4f2">Para</span> un<span style="color:#07b4f2"> foro interno dice</span>.

<span style="color:#ecacb6">/api/latest/metadata/changelog</span> :    vemos <span style="color:#07b4f2">otras</span> <span style="color:#ff2dc0">rutas</span> del <span style="color:#ff2dc0">API</span>.

<span style="color:#07b4f2">Intentamos</span> ingresar <span style="color:#07b4f2">por</span> <span style="color:#ff2dc0">SSH</span> con
<span style="color:#ecacb6">dev:dev080217_devAPI!@</span>
Y pa deentro.

<span style="color:#bef202">Extra</span>.
Para <span style="color:#ff2dc0">enumerar</span> mejor los recursos del <span style="color:#ff2dc0">API</span>, <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">script</span>.
![[Pasted image 20240929183852.png]]
![[Pasted image 20240929183924.png]]


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Jammy.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">prod</span>
<span style="color:#ecacb6">dev</span>

<span style="color:#ff2dc0">Enumeremos</span> el directorio <span style="color:#ecacb6">/home/dev/apps</span>
Hagámoslo <span style="color:#07b4f2">bien</span> porque <span style="color:#07b4f2">hay</span> un <span style="color:#ecacb6">.git</span>
<span style="color:#88c425">git log -p 1e84a036b2f33c59e2390730699a488c65643d28</span> :    <span style="color:#07b4f2">vemos</span> <span style="color:#ecacb6">prod:080217_Producti0n_2023!@</span>
Es un <span style="color:#ff2dc0">commit</span> <span style="color:#07b4f2">de antes</span>, se <span style="color:#07b4f2">reemplazaron esas</span> <span style="color:#ff2dc0">credenciales</span> <span style="color:#07b4f2">por las</span> que <span style="color:#07b4f2">obtuvimos antes</span>.

Probamos a <span style="color:#07b4f2">migrar</span> a <span style="color:#ecacb6">prod</span> y <span style="color:#07b4f2">funcionan</span>.
<span style="color:#88c425">sudo -l</span>  :   <span style="color:#ecacb6"> (root) /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py *</span> 
Lo que hace es como clonar un repositorio, creo. <span style="color:#07b4f2">Tiene</span> que <span style="color:#07b4f2">ver</span> con la <span style="color:#ff2dc0">librería GitPython</span>.

Investigando
https://publish.obsidian.md/droids-library/Blog/Season+5+Summary :    <span style="color:#07b4f2">parte</span> de <span style="color:#ecacb6">Priv Esc with GitPython</span>.
Podemos ver <span style="color:#07b4f2">información</span> aquí https://security.snyk.io/vuln/SNYK-PYTHON-GITPYTHON-3113858 :    ya tiene <span style="color:#ff2dc0">hardcodeado</span> el <span style="color:#ff2dc0">comando</span>, pero <span style="color:#07b4f2">nosotros</span> lo <span style="color:#07b4f2">pasamos</span> como <span style="color:#ff2dc0">parámetro</span> <span style="color:#07b4f2">al</span> <span style="color:#ff2dc0">script</span>.
<span style="color:#07b4f2">Vemos</span> si tenemos esa <span style="color:#07b4f2">versión</span> <span style="color:#ff2dc0">vulnerable</span>
<span style="color:#88c425">pip3 list | grep "GitPython"</span> :    sí, <span style="color:#ecacb6">3.1.29</span>

Vimos que podemos <span style="color:#ff2dc0">ejecutar comandos</span>, y como <span style="color:#07b4f2">controlamos</span> el <span style="color:#ff2dc0">input</span>, <span style="color:#07b4f2">en lugar </span>de pasarle una <span style="color:#ff2dc0">URL</span> podemos <span style="color:#07b4f2">hacer</span>
<span style="color:#88c425">sudo /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::sh -c chmod% 4777% /bin/bash'</span>
Los <span style="color:#ecacb6">'%'</span> son <span style="color:#07b4f2">por</span> los <span style="color:#07b4f2">espacios</span>.

<span style="color:#07b4f2">Para</span> hacer
<span style="color:#88c425">bash -p</span> :    y pa deeeentro.
FIN.
