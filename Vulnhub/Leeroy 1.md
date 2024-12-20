# Leeroy 1

Tags: [[_webFuzzing]],[[_cms]],[[_lfi]],[[_rfi]],[[_infoLeakage]],[[_crypto]],[[_webExploitation]],[[_sudoersAbusing]]

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
22 - SSH (versión 8.2)

80 - HTTP (nginx)

8080 - HTTP (Jetty 9.4.27)

13380 - HTTP (Apache)

33060 - MySQLX


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

22.
De momento no tengo credenciales válidas y no puedo validar usuarios a nivel de sistema.


80.
Empecemos por aquí.
whatweb no dice mucho, 

Desde la web.
Hay una imagen, podemos pensar en Esteganografía, luego lo probamos.
El código fuente no dice mucho, así que luego fuzzeemos.
Haciendo fuzzing:
/images nos pone forbidde. Fuzzeando dentro, no podemos ver la ruta donde están las imágenes.
<span style="color:#379075">(Fuzzeemos 8080).</span>



8080.
whatweb, nos da Forbidden, es un Jenkins(2.222.3), hay un Jetty, nos aplica un redirect a un panel de autenticación del Jenkins.

Desde la web.
Enumerar Jenkins.
El código fuente no dice mucho, hay un panel de autenticación para entrar al Jenkins. Podríamos fuzzear.
Haciendo fuzzing:
No se puede porque está el login.
<span style="color:#379075">(Fuzzeemos 13380).</span>


13380.
whatweb nos dice que es un Apache, nos redirige a leeroy.htb pero mi sistema no lo conoce, hay que contemplarlo en el /etc/hosts
Volviendo a lanzar el whatweb, nos redirige a un Wordpress.

Desde la web.
Enumerar Wordpress.
El código fuente menciona una ruta, entro y no arroja error, no se ve nada, pero en su código fuente se ven cositas.
Habla de un proceso de Hashing, donde tengo que introducir algo en el "localStorage"...

Wordpress 5.4.1, veamos en searchsploit (SQLI autheticated).

Vemos un usuario "Leeroy", "Gravatar".
Hay un apartado para subir un comentario.

El panel para looguearnos al Worpress nos dice que "leeroy" es usuario válido.
<span style="color:#379075">(Fuzzeemos el 80).</span>

Haciendo fuzzing:
/wp-content :    no se ve nada.
/wp-includes :    
/wp-admin :    es el login.
/server-status :    forbidden

Juagando con curl para ver plugins visibles del Wordpress.
Hay unos cuantos.
Hay que obtener el nombre del plugin, para ordenarlos y buscar vulnerabilidades.
Regex:
<span style="color:#88c425">curl -s -X GET "http://leeroy.htb:13380/" | gre -oP '/plugins/\K[^/]+'</span>
Donde hay /plugins/ 
Con <span style="color:#ecacb6">/K </span>reconfiguro  punto de matching al siguiente punto para que /plugins me lo quite y se quede con el resto.

<span style="color:#ecacb6">[^/]+</span> :    quee me filtre hasta la barra.

<span style="color:#88c425">sort -u </span>para quitar repeticiones.
Y ya con searchsploit buscamos vulnerabilidades para esos Plugins.
Hay uno de Remote File Inclusion para el plugin
wp-with-spritz

Lo descargamos y analizamos...
Nos habla de una ruta la cual es:
<span style="color:#ecacb6">/wp-content/plugins/wp-with-spritz/readme.txt</span>
Hay cosas de configuración.

Llegamos a esta ruta
<span style="color:#ecacb6">/wp-content/plugins/wp-with-spritz/wp.spritz.content.filter.php?url=/etc/passwd</span>
Sí me lo muestra, también puedo apuntar a otras lados.

Pero lo chulo no es el LFI, si no el RFI:
<span style="color:#ecacb6">http://leeroy.htb:13380/wp-content/plugins/wp-with-spritz/wp.spritz.content.filter.php?url=http://192.168.0.18:8080/login?from=1</span>
Nos muestra el contenido de la web que está en otro puerto.

Buscando de todo.
Hay varios recursos php, hay un admin.view.php
Hay imágenes, podría traerlas y ver qué tienen.
Antes probemos lanzarnos una petición a mi.

Probar hacer una petición a mi.
Uff, telita, sí me llega. Tratamos de que nos interprete algo de código PHP, pero nada.


Sigamos tratando de incluir un archivo local.
/proc/net/tcp :    podemos ver si hay otro puerto, recordemos que tenemos un RFI.
Volteamos los valores y hacemos
<span style="color:#88c425">echo "$((0x7F)).$((0x00)).$((0x00)).$((0x35))"</span>

Seguimos enumerando el sistema.

Recordamos cuando buscamos credenciales por defecto para el jenkins, pues hay una ruta:
<span style="color:#ecacb6">/var/lib/jenkins/secrets/initialAdminPassword</span>
Pero nada.

Podemos ver el /bash_history de leeroy.
Obtenemos lo que parece ser una contraseña
<span style="color:#ff9a57">z1n$AiWY40HWeQ@KJ53P</span>

La probamos en todos lados, pero funciona para el panel de Jenkins, para <span style="color:#07b4f2">admin:z1n$AiWY40HWeQ@KJ53P</span>
Usamos la caja de script, y buscamos una reverse shell que use groove (java).
https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76 :    lo ajusto para Linux, y pa deeentro.

Pruebas:
Podemos buscar cómo hacer scripts normalmente.
<span style="color:#ff2dc0">println "ls".execute().text</span>

No le sientan bien a Jenkins los caracteres especiales.
Así que compartimos un recurso con una reverse shell.
Y desde el Script Console (no van los pipes):
![[Pasted image 20240701034948.png]]
Luego lo vemos
![[Pasted image 20240701035025.png]]

Para ejecutarlo con bash
![[Pasted image 20240701035056.png]]
Ya yo estando en escucha, ganamos acceso.


33060.
De momento no tengo credenciales para conectarme al servicio de mysqlx.


## Escalada

Enumerar todo el sistema.
Hay un credentials.xml que habla de id´s, y si recordamos, encontramos un panel algo interesante.
Pero habla más de login al Wordpress.

También tengo un hash.

Volviendo a la password cifrada, busquemos en internet "jenkins derypt password", porque el credentials.xml es muy común.
Y efectivamente, jenkins tiene una función para eso:
https://devops.stackexchange.com/questions/2191/how-to-decrypt-jenkins-passwords-from-credentials-xml

Hice:
![[Pasted image 20240701043330.png]]
Y me resultó una cadena, la probé y pa deentro.

<span style="color:#bef202">Elevar a root.</span> 

Hay un archivo que podemos ver con 
sudo -l
El cual instala cosas, usa wget para exportar el output en un archivo, puedo sobrescribir otros archivos, como el /etc/shadow /etc/passwd

Vemos que se podría acontecer un ataque, porque el /etc/hosts se puede retocar (<span style="color:#379075">hay que verlo</span>), entonces puedo hacerme pasar por el dominio al que hace la petición. Necesito estar como Jenkins pero eso es fácil.
Para solo crear las rutas y cargar un /etc/passwd modificado con una contrasña des unix.

Sin embargo, una cosa a tener en cuenta es que lo hace a un HTTPS, así que debería jugar con Apache.

1.- Modifico el /etc/hosts para que apunte a mi cuando se referencie el dominio.
2.- Creo los directorios en mi máquina, creando a su vez los archivos.
Los archivos deben tener el /etc/passwd de la máquina víctima.
Seteamos una una contraseña con hash hardcodeada en formato des unix.
La creamos con openssl.
3.- Iniciamos el servicio de apache.
<span style="color:#88c425">service apache2 start</span> 
Pero si ponemos https://localhost no jala.
Así que habilitemos el SSL.
<span style="color:#88c425">a2enmod ssl</span>
<span style="color:#88c425">systemctl restart apache2</span> :    para <span style="color:#07b4f2">reiniciar</span> el <span style="color:#07b4f2">servicio</span>.

4.- Ahora, para que tome la ruta donde tenemos los archivos creados, imaginemos que es aquí:
<span style="color:#ecacb6">/home/joelkami/notas/content </span>
Abrimos
<span style="color:#88c425">nvim /etc/apache2/sites-available/default-ssl.conf </span>:    para que donde ponga <span style="color:#07b4f2">DoumentRoot</span> pongamos la ruta donde están los archivos.
<span style="color:#88c425">service apache2 restart</span>  :    reiniciar apache.

En este punto carga, pero pone Forbbiden.
No los indexa bien, así que en el .conf nuevamente:
<span style="color:#88c425">nvim /etc/apache2/sites-available/default-ssl.conf </span>
Hasta abajo antes de VirtualHost, ponemos
<Diretory "/home/joelkami/notas/content/debian-stable/binary"> 
	Options +indexes
	Require all granted
</Directory>

Guardamos y <span style="color:#88c425">service apache2 restart</span>, y ya estaría.

Y ya en la máquina víctima ejecutamos el script y le pasamos como argumento el <span style="color:#ecacb6">/etc/passwd</span> para que ponga el otro.
