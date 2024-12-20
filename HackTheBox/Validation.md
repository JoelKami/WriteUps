# Validation

Tags: [[_sqli]],[[_webExploitation]],[[_infoLeakage]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu, Debian</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

| Port                                    | Service            |
| --------------------------------------- | ------------------ |
| <span style="color:#ff669c">22</span>   | SSH 8.2p1          |
| <span style="color:#ff669c">80</span>   | HTTP Apache 2.4.48 |
| <span style="color:#ff669c">4566</span> | HTTP               |
| <span style="color:#ff669c">8080</span> | HTTP               |


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> válidas.

<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#ff2dc0">PHP</span>, poco más.
Desde la <span style="color:#ff2dc0">web</span>.
Hay un <span style="color:#07b4f2">apartado</span> para <span style="color:#07b4f2">ingresar</span> a una <span style="color:#07b4f2">competencia</span>.
<span style="color:#ff2dc0">Campo</span> <span style="color:#ecacb6">username</span> es <span style="color:#ff2dc0">vulnerable</span> a <span style="color:#ff2dc0">Stored XSS</span>, <span style="color:#ff2dc0">HTML Injection</span>.
<span style="color:#ff2dc0">Campo</span> <span style="color:#ecacb6">country</span> es <span style="color:#ff2dc0">vulnerable</span> a <span style="color:#ff2dc0">SQLI</span>.
<span style="color:#ff2dc0">Enumeramos</span> la <span style="color:#ff2dc0">DB</span>, pero no encontramos <span style="color:#07b4f2">nada</span> de <span style="color:#07b4f2">información</span>.
<span style="color:#07b4f2">Probamos</span> a<span style="color:#07b4f2"> crear un archivo</span> y nos <span style="color:#07b4f2">deja</span>, creamos un <span style="color:#ecacb6">.php</span> para entablarnos una <span style="color:#ff2dc0">reverse shell</span> y pa deeentro.


<span style="color:#ff669c">4566</span>.
<span style="color:#ff2dc0">whatweb</span>. No mucho.


<span style="color:#ff669c">8080</span>.
<span style="color:#ff2dc0">whatweb</span>. No mucho.


>[!Tip] Extra - AutoPwn
>Creamos el .php con el SQLI, después le hacemos una petición para realizar una reverse shell. Y ahí mismo en el script recibimos la shell.

![[Pasted image 20241213121655.png]]
![[Pasted image 20241213121721.png]]


## Escalada

Estamos en un contenedor.
<span style="color:#ff2dc0">Host</span>: <span style="color:#ecacb6">172.21.0.10</span>

En <span style="color:#ecacb6">/var/www/html/config.php</span> hay <span style="color:#ff2dc0">credenciales</span>.
<span style="color:#ecacb6">uhc:uhc-9qual-global-pw</span> 

<span style="color:#07b4f2">Las probamos</span> con <span style="color:#ecacb6">root</span> y pa deentro.
<span style="color:#07b4f2">No</span> está <span style="color:#07b4f2">pensada</span> para <span style="color:#07b4f2">escapar</span> del <span style="color:#ff2dc0">contenedor</span>.
Fin.
