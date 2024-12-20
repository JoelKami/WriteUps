# Epsilon

Tags: [[_analysis]],[[_ssti]],[[_webExploitation]],[[_timeTasks]],[[_missConfigurations]],[[_infoLeakage]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Focal</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

| Port                                    | Service              |
| --------------------------------------- | -------------------- |
| <span style="color:#ff669c">22</span>   | OpenSSH 8.2p1        |
| <span style="color:#ff669c">80</span>   | Apache httpd 2.4.41  |
| <span style="color:#ff669c">5000</span> | Werkzeug httpd 2.0.2 |


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> válidas.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">NMAP</span>. Reporta un <span style="color:#ecacb6">.git</span>
<span style="color:#ff2dc0">whatweb</span>. No mucho.
<span style="color:#ff2dc0">Desde la web</span>. <span style="color:#ecacb6">Forbidden</span>.

<span style="color:#07b4f2">Tratemos</span> de <span style="color:#ff2dc0">recomponer</span> el <span style="color:#ff2dc0">proyecto</span> de <span style="color:#ff2dc0">github</span>.
Con https://github.com/internetwache/GitTools/blob/master/Dumper/gitdumper.sh al <span style="color:#07b4f2">igual</span> que el <span style="color:#ff2dc0">extractor</span>.
<span style="color:#07b4f2">Logramos</span> <span style="color:#ff2dc0">recomponer</span> el <span style="color:#ff2dc0">proyecto</span>.

En el proyecto completo tenemos varia <span style="color:#07b4f2">información</span>, viendo <span style="color:#ff2dc0">commits</span> <span style="color:#07b4f2">vemos</span> cosas de <span style="color:#ecacb6">aws_access_key_id</span> y demás, hay que <span style="color:#07b4f2">entenderla</span>
<span style="color:#ff2dc0">SubDominio</span>: <span style="color:#ecacb6">cloud.epsilon.htb</span>, <span style="color:#07b4f2">agregamos</span> el <span style="color:#ff2dc0">dominio</span> y <span style="color:#ff2dc0">subdominio</span> al <span style="color:#ecacb6">/etc/hosts</span>

Hay dos<span style="color:#ecacb6"> .py</span>, un <span style="color:#ecacb6">server.py</span> que <span style="color:#07b4f2">si</span> <span style="color:#ff2dc0">ejecuto</span> se pone en <span style="color:#ff2dc0">escucha</span> por el <span style="color:#ff669c">puerto 5000</span>, es una <span style="color:#ff2dc0">web</span>, en el <span style="color:#ff2dc0">código fuente</span> veo que <span style="color:#07b4f2">hay un</span> <span style="color:#ff2dc0">secret</span>, <span style="color:#07b4f2">pero cambia</span> cada vez.
En los archivos <span style="color:#07b4f2">vemos</span> <span style="color:#ff2dc0">rutas</span>
<span style="color:#ecacb6">/home</span>
<span style="color:#ecacb6">/track</span> :    podemos <span style="color:#07b4f2">entrar</span> pero al <span style="color:#07b4f2">hacer</span> alguna <span style="color:#07b4f2">acción</span>, <span style="color:#07b4f2">nos lleva a la raíz</span>.
<span style="color:#ecacb6">/order</span>

<span style="color:#07b4f2">Seguimos</span> <span style="color:#ff2dc0">enumerando</span> los <span style="color:#ff2dc0">archivos</span>.
En <span style="color:#ecacb6">track_api_CR_148.py</span> <span style="color:#07b4f2">se repite</span> mucho <span style="color:#ecacb6">lambda</span>, buscamos<span style="color:#ecacb6"> lambda file python</span> en <span style="color:#ff2dc0">Internet</span>.
https://aws.amazon.com/cli/

Podríamos<span style="color:#ff2dc0"> instalarnos aws</span>
<span style="color:#88c425">pip install aws</span> :    y <span style="color:#07b4f2">probar</span> cosas.
<span style="color:#88c425">pip install awscli</span> :   <span style="color:#379075"> lo necesita</span>.
<span style="color:#88c425">aws help</span> :    podemos ver que <span style="color:#07b4f2">viene</span> lo de <span style="color:#ff2dc0">Lambda</span>.
<span style="color:#88c425">aws configure</span> :    nos <span style="color:#07b4f2">pide cosas</span>, <span style="color:#07b4f2">las cuales tenemos</span> gracias al <span style="color:#ff2dc0">proyecto</span> de <span style="color:#ff2dc0">git</span>
![[Pasted image 20241217003558.png]]
Ya tenemos una <span style="color:#07b4f2">parte configurada</span> para que nos <span style="color:#07b4f2">conectemos</span> al <span style="color:#ff2dc0">endpoint</span> (<span style="color:#ecacb6">cloud.epsilon.htb</span>)

<span style="color:#88c425">aws --endpoint-url=http://cloud.epsilon.htb lambda help</span> :    para <span style="color:#07b4f2">ver</span> lo que podemos <span style="color:#07b4f2">hacer</span> con esa <span style="color:#ff2dc0">función</span>. Hay varias cosas, entre ellas
<span style="color:#88c425">aws --endpoint-url=http://cloud.epsilon.htb lambda list-functions</span>
![[Pasted image 20241217004632.png]]

<span style="color:#07b4f2">Teniendo</span> el <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">function</span>
<span style="color:#88c425">aws --endpoint-url=http://cloud.epsilon.htb lambda get-function --function-name costume_shop_v1</span>
![[Pasted image 20241217005124.png]]
<span style="color:#07b4f2">Información extra</span>.
Entramos a la <span style="color:#ff2dc0">URL</span> y nos podemos <span style="color:#07b4f2">descargar</span> el <span style="color:#ecacb6">.zip</span>

<span style="color:#07b4f2">Analizar</span> contenido.
Es un <span style="color:#ff2dc0">script</span> en <span style="color:#ff2dc0">python</span>, <span style="color:#07b4f2">como</span> todas las <span style="color:#ff2dc0">funciones</span>.
![[Pasted image 20241217011434.png]]

<span style="color:#07b4f2">Antes</span> habíamos <span style="color:#07b4f2">visto</span> que en <span style="color:#ecacb6">secret</span> no había <span style="color:#07b4f2">nada</span>, así que es <span style="color:#07b4f2">posible que sea este</span>, <span style="color:#07b4f2">tenemos</span> los <span style="color:#ff2dc0">valores</span> y por lo tanto <span style="color:#07b4f2">creamos</span> el <span style="color:#ff2dc0">JWT</span>
![[Pasted image 20241217012720.png]]
<span style="color:#379075">De esta manera o de otra forma más cómoda</span>.
El <span style="color:#ff2dc0">aplicativo</span> <span style="color:#07b4f2">busca</span> por un "<span style="color:#ecacb6">auth</span>", así que <span style="color:#07b4f2">en el navegador lo creamos</span> y <span style="color:#07b4f2">ponemos</span> la <span style="color:#ff2dc0">cookie</span>.

Una vez hecho lo anterior, volvemos a ir a 
<span style="color:#ecacb6">/track</span> :    porque <span style="color:#07b4f2">por sí solo no nos lleva</span> a <span style="color:#07b4f2">ningún lado</span> al <span style="color:#07b4f2">poner</span> la <span style="color:#ff2dc0">cookie</span> y <span style="color:#07b4f2">recargar</span>.

Una vez dentro, <span style="color:#07b4f2">analizar</span> todo.
En <span style="color:#ecacb6">/order</span> al interceptar la <span style="color:#ff2dc0">petición</span> vemos un <span style="color:#ff2dc0">campo</span> <span style="color:#ecacb6">costume</span> <span style="color:#ff2dc0">vulnerable</span> a un <span style="color:#ff2dc0">SSTI</span>.
<span style="color:#07b4f2">Este</span> me <span style="color:#07b4f2">funciona</span>.
![[Pasted image 20241217014323.png]]
<span style="color:#07b4f2">En lugar</span> de <span style="color:#88c425">id</span>, ejecutamos una <span style="color:#ff2dc0">reverse shell</span> y pa deeentro.


<span style="color:#ff669c">5000</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#07b4f2">Campo</span> de <span style="color:#ecacb6">password</span>, <span style="color:#ff2dc0">Python 3.8.10</span>, <span style="color:#ff2dc0">Werkzeug</span>.
<span style="color:#ff2dc0">Desde la web</span>. Un apartado de <span style="color:#ff2dc0">login</span>.
<span style="color:#379075">Como el .git estaba en el 80, la explicación se ve por allá, aunque recordemos que se trata de este puerto</span>.


## Escalada

Enumerar todo el sistema.
Efectivamente, un Ubuntu Focal.

<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">tom</span> -> como el que estamos.

En <span style="color:#ecacb6">/var/www/app/app.py</span>, que es el archivo que teníamos, pero, con una <span style="color:#07b4f2">contraseña</span> <span style="color:#ecacb6">4d_09@fhgRTdws2</span> de <span style="color:#ecacb6">admin</span>.
Probamos pero no es un usuario.

<span style="color:#ff2dc0">Host</span>: <span style="color:#ecacb6">10.10.11.134</span>
Tiene otras <span style="color:#ff2dc0">interfaces</span> con distintas <span style="color:#ff2dc0">IP</span>.
<span style="color:#ecacb6">172.17.0.1</span>,<span style="color:#ecacb6">172.19.0.1 </span>

<span style="color:#88c425">ps -faux</span> :    vemos un <span style="color:#ff2dc0">container</span> con un <span style="color:#ff669c">puerto</span> que se le configuró, está abierto. El <span style="color:#ecacb6">172.19.0.1:4566</span>, tiene el <span style="color:#ff2dc0">SSH abierto</span>.

Y <span style="color:#07b4f2">pensando</span> en la <span style="color:#ff2dc0">contraseña</span> que <span style="color:#07b4f2">conseguimos</span>, parece como cuando se hace una <span style="color:#ff2dc0">conexión</span> por <span style="color:#ff2dc0">SSH</span>. Pero <span style="color:#07b4f2">no se puede</span>.

Creamos un <span style="color:#ff2dc0">procmon</span>.
Vemos <span style="color:#07b4f2">cositas</span>.
<span style="color:#ecacb6">root</span> ejecuta <span style="color:#ecacb6">/bin/sh -c /usr/bin/backup.sh</span>
En una parte hace
<span style="color:#ecacb6">/usr/bin/tar -chvf "/var/backups/web_backups/${check_file}.tar" /opt/backups/checksum "/opt/backups/$file.tar"</span>
Pero usa <span style="color:#88c425">tar -chvf</span>, la <span style="color:#ecacb6">h</span> está <span style="color:#07b4f2">curiosa</span> ahí, porque si <span style="color:#07b4f2">vemos</span> con <span style="color:#ff2dc0">man</span> el manual de <span style="color:#ff2dc0">tar</span>, <span style="color:#07b4f2">explica</span> que <span style="color:#07b4f2">sige</span> los <span style="color:#ff2dc0">link simbólicos</span>.
Además, <span style="color:#07b4f2">está haciendo</span> un <span style="color:#ff2dc0">sleep 5</span> <span style="color:#07b4f2">antes</span> del comando <span style="color:#ff2dc0">tar</span>. En ese <span style="color:#07b4f2">margen</span> puedo <span style="color:#07b4f2">borrar</span> el <span style="color:#ecacb6">checksum</span> y <span style="color:#07b4f2">crear otro con</span> un <span style="color:#ff2dc0">enlace simbólico</span>.

<span style="color:#bef202">Proceder</span>.
![[Pasted image 20241217040444.png]]
Se entiende.
Después <span style="color:#07b4f2">nos traemos</span> el <span style="color:#ecacb6">.tar</span>, lo <span style="color:#ff2dc0">descomprimimos</span> y <span style="color:#07b4f2">buscamos</span> el <span style="color:#ecacb6">cheksum</span> que tiene la <span style="color:#ecacb6">id_rsa</span> de <span style="color:#ecacb6">root</span>, la usamos como <span style="color:#ff2dc0">fichero de identidad</span> para <span style="color:#ff2dc0">conectarnos</span> a la máquina y pa deeeentro.
Fin.
