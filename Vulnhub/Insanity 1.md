# Insanity 1

Tags: [[_webFuzzing]],[[_sqli]],[[_bruteForce]],[[_webExploitation]]

Hacemos un escaneo en la red para determinar la IP de la máquina.
Mediante el OUI reconocemos la máquina que vamos a atacar.

Creamos un directorio como la IP o nombre de la máquina.
Ponemos la IP de la máquina en un lugar visible.
Creamos los directorios de trabajo.
Le hacemos un ping a la máquina.
Posible máquina Linux.

Empezamos el escaneo con NMAP.

En base a las versiones, determinarnos el codename, se trata supuestamente de un CentOS Trusty.

Puertos abiertos.
21 ftp. Puedo conectarme de forma anónima.
22 ssh. No poseo credenciales de momento. Es versión inferior a la 7.7
80 http. No dice mucho, juguemos con whatweb.


Puerto 80.
Tenemos un correo. Luego procedemos a analizarla.

Puerto 21. Vamos a ver más esto.
No hay ni pingas.
Comprobemos si podemos subir archivos.
<span style="color:#88c425">put example.txt</span> :    desde la máquina víctima, pero no se puede. Tampoco se puede el dir pub.

No hay más.

Vamos a analizar por completo el puerto 80.
De primeras tiene pinta de virtual hosting, pueden haber otros dominios.

Hay una ruta "monitoring", que me lleva a un panel de autenticación.

Cosas a probar:
slq injection
fuerza bruta
Fuzzear directorios
Encontrar otros dominios.

Voy a probar poner el dominio en el /etc/hosts, pero me lleva a la misma página, podría fuzzear subdominios. También enumerar recursos php ya que interpreta php.

Fuzeemos por rutas.
Y efectivamente, me muestra "monitoring", y más cosas interesante.

Dir "news", vemos posible usuario otis (podríamos enumerarlo en ssh, pero a priori no).
No me carga bien, porque está tomando recursos de un subdominio que no conozco, ponemos el subdominio con www en el /etc/hosts.

Dir "webmail", nos lleva a un panel de autenticación.
Dir "monitoring", me lleva al panel de autenticación.
Dir "phpmyadmin", nos lleva al panel de autenticación de PHP. Es de la base de datos. Probar usuario "root" sin contraseña, pero no funciona.


Tenemos 3 paneles de autenticación.

SquirrelMail.
Podemos pensar en el usuario "hello", por el correo, y "otis".
Haremos fuerza bruta con Hydra.
Tenemos credenciales, otis:123456

Vamos a movernos un poquito dentro.


Login de monitoring.
Entramos con otis:123456
Dice que nos llega un correo cuando un servidor se cae, así que podemos verlo en el SquirreMail.

Antes, algo que podemos hacer, es modificar el monitoreo y poner mi IP, por lo visto me manda un PING, así que puedo pensar el colarle algo.

Si yo meto algo que no tenga sentido en IP, me llega un mensaje de error al correo.

Podemos probar cosas como:
test || whoami, etc., pero nada.
O mandar cadenas en hexadecimal con el -p en ping. Pero no es el caso.

Pero claro, en el correo se ve reflejado el nombre del estado del servidor.
Podríamos hacer inyecciones sql.
Y se pone chungo.
Jugando con varias slq injection, dumpeamos las credenciales de usuarios.

Las credenciales están hasheadas, y no se pueden crackear.

Debemos encontrar otro vector de ataque en las sql injection.
Dumpeamos la base de datos mysql y conseguimos un hash de un usuario, lo crakeamos en crackstation.net 

phpMyAdmin.
No se reutilizan credenciales aquí. Pero poniendo otis sin contraseña, pa dentro.


Con el usuario elliot y contraseña elliot123, podemos intentar ingresar por ssh y se puede.

Vemos que hay un archivo .Xauthority y un .mozilla
Dentro de .mozilla/firefox hay una sesión.
En esmhp32w.default-default podemos ver logins.json y key4.db

Necesitamos el key4.db para ver los logins.json
Usaremos la herramienta firepwd.py (yo desde un usuario normal).
Me transfiero los archivos a mi máquina.
Y ejecuto python3 firepwd.py
Y vemos credenciales hasta abajo.

Son de root, migro a root y ya estaría.


