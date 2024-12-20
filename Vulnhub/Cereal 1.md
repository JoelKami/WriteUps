# Cereal 1

Tags: [[_webFuzzing]],[[_deserialization]],[[_webExploitation]],[[_timeTasks]]

Hacemos un escaneo en la red para determinar la IP de la máquina.
Mediante el OUI reconocemos la máquina que vamos a atacar.

Creamos un directorio como la IP o nombre de la máquina.
Ponemos la IP de la máquina en un lugar visible.
Creamos los directorios de trabajo.
Le hacemos un ping a la máquina.
Posible máquina Linux.

Empezamos el escaneo con NMAP.

Mediante las versiones determinamos el codename, aunque en este caso no dice mucho el propio escaneo.

De primeras son bastantes puertos, algunos los conozco:
21 ftp - Puedo entrar como anónimo. Pero no hay nada.
22 ssh - No tengo credenciales, y no es inferior a la v7.7
80 http
139 - Tiene relación con SMB, pero de momento nada.
445 smb - Al enumerar con smbclient no reporta mucho.
3306 mysql - No tengo credenciales, así que tengámoslo en cuenta si conseguimos credenciales.

Los demás tienen todos los números iguales, como 22222, 44444, etc.

Hay uno que es el 44441, el cual parece ser un servicio http.


Enumeremos el puerto 80, y el puerto 44441.

80: Tenemos un correo.
Es un server Apache. Parece ser de tests.
Debemos fuzzear un poco.
Con gobuster y wfuzz.
Hay cositas.
+Hay una ruta donde hay un wordpress, pero se ve sin CSS.
En el código se ve que intenta cargar contenido de cereal.ctf, pero creo que como mi máquina no la conoce, no lo carga.
Debemos contemplarlo en el /etc/hosts
Vemos que es la misma web que poniendo la IP.

El wordpress, que ya se ve bien, habla acerca de un back up, que vuelva a funcionar, eso querrá decir que puede haber algo montado por ahí.
Viendo la ruta /wp-admin hay un panel de autenticación del Wordpress.
Podemos ver si tiene también el /xmlrpc.php para tener en cuenta si en el futuro hacer fuerza bruta con bash o python al mandar opeticiones por POST.
Al husmear la web, vemos que hay un usuario "cereal".
La versión del WordPress es 6.5.3 así que enumerar usuarios no se va a poder.

Hacemos una petición:
<span style="color:#88c425">curl -s -X GET "http://cereal.ctf/blog/index.php/2021/05/29/update/" | grep -i plugin</span> :    para detectar plugins. Pero nada.
WordPress en /wp-admin se puede validar si un usuario es válido o no.
(Básicamente podría probar con el usuario cereal para aplicar fuerza bruta, pero parece ser que el WordPress es un Rabbit Hot. . -.)


+Otra ruta de admin, es un panel de autenticación.
Está MySQL, podemos pensar en inyecciones.
(Ni inyecciones, ni fuerza bruta, no hay mucho).


Ahorita checamos por el puerto 44441.
Ya sea desde la IP o desde el dominio cereal.ctf aparece un "Coming soon..."
Posiblemente fuzzeando encontremos algo interesante.
Pero parece ser que no.
Seguramente como se mencionaba un back up, tenga que fuzzear por una extensión en particular.

Podríamos enumerar subdominios.
(enumerar subdominios, porque puede haber otro a parte de cereal.ctf, como admin.cereal.ctf, etc.)

<span style="color:#88c425">wfuzz -c --hc 404 --hl 1 -u http://cereal.ctf:44441/ -H "Host: FUZZ.cereal.ctf" -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -t 5</span> :    con este comando pude encontrarlo.

"secure.cereal.ctf"

Contenido nuevo, empecemos a enumerar...
Hace pings, puedo hacerme a mi, hay que pensar en SSRF.

Deberíamos ver cómo viaja la petición por detrás, qué estamos mandando y todo eso.
Porque de primeras, no hay forma de burlar la utilidad.

A parte de probar la utilidad, deberíamos fuzzear pero ahora en este nuevo host.

EN EL CÓDIGO FUENTE. Hay que analizar.
Se juega con serialización.
Vemos un php.js

Viendo la petición por detrás de la utilidad por detrás. Vemos que viaja un objeto serializado.

En el código de php.js no se toca nada de "Deserialización".
Por lo que fuzzear es una buena opción.
Claro, lo de serializar el "php", hay que buscar por esa extensión, pero, recordando algo de back up, podríamos buscar por extensión back.

No hizo falta, usando un diccionario más grande y fuzzeando por URIs, encontré un /back_en

Me pone forbidden, pero puedo fuzzear ahí dentro.

No encontramos nada, deberíamos fuzzear por extensiones.
Por php solo no encuetra nada, pero por php.bak sí.
Enontramos un index. Un /index.php.back

Ojito, ahí se toca el "Unserialize".

Haciendo una serialización de la clase testPing, para luego mandarla por BurpSuite, pudimos entablarnos una reverse Shell.
Es básciamente un ataque de deserialización en php.


ESCALADA...
Debemos husmear de todo.
El lsb_release no funciona.
Pero podemos ver el /etc/os-release
Es un fedora, poco más.

No hay información escondida, no permisos SUID, no tengo contraseña para sudo -l, capabilities nada, id y estoy como apache.

Getcap no lo tiene de primeras, el $PATH vale poquito, copiemos el nuestro y peguémoslo ahí.

Con getcap no hay mucho.
suexec, pero no es algo interesante. Viendo lo que hace, no dice mucho.

Podemos listar en apache, pero es Fedora:
Podemos buscar desde la raíz por el nombre de apache.
Está en /etc/httpd


Veamos las tareas que se ejecutan a intervalos regulares de tiempo.
Nos descargamos el pspy, el procmon.sh no reporta mucho.
https://github.com/DominicBreuker/pspy/releases/tag/v1.2.1
Ahí me descargo el "pspy64"
Y lo subo en la máquina víctima.
Me monto un servidor http con python, y con wget me lo descargo desde la máquina víctima, le damos permisos de ejecución y lo ejecutamos.

Usar systemctl list-timers, también reporta cosas a veces.

Toca esperar para ver qué reporta el pspy.
Hay cositas.

Se está ejecutando un programa chown.sh
Que al parecer hace un chown rocky:apache
Es decir, asigna a rocky como propietario y apache como grupo de unos archivos, a todos los que se encuentran en /home/rocky/public_htl

![[Pasted image 20240508084823.png]]


Mmm, quizá un ciclo para determinar los archivos que se encuentran ahí, o una forma más fácil, con un * para hacer /home/rocky/public_html/* y que englobe todo lo de adentro.

Primero chequemos con un archivo de prueba para ver si lo hace, con watch estaría bien.
Aunque parece que sí lo hace.


Puedo hacer un enlace simbólico al /etc/passwd para que lo pueda editar, porque los enlaces simbólicos arrastran el permiso:
<span style="color:#88c425">ln -s -f /etc/passwd pwned</span>

Pudiendo escribir en el /etc/passwd asignar una contraseña a root, porque ahora el propietario sería rocky y el grupo apache:
<span style="color:#88c425">openssl passwd</span> :    ponemos "hola" y la confirmamos para que sea esa la contraseña.
Esa contraseña Harcodeada en formato DES Unix, la metemos en la 'x' de root en el /etc/passwd

Hacer <span style="color:#88c425">su root</span>, proporcionar la contraseña que le puse y pa dentro.

FINALIZADA.

Con <span style="color:#88c425">hash-identifier</span>
Le pasamos el hash que hicimos con openssl, y te da el formato. Interesante.
