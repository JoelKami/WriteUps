# Djinn 3

Tags: [[_bruteForce]],[[_scripting]],[[_ssti]],[[_webExploitation]],[[_sudoersAbusing]]

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

Buscamos codename con launchpad, un posible Ubuntu Bionic.

Puertos abiertos.
22 - SSH. Versión 7.6, enumerar usuarios. De momento no tengo credenciales válidas.

80 - HTTP. Reporta que es una web normalita.

5000 - HTTP (Werkzeug). Se ve que es una web, hecha con Python. Y UPnP.

31337 - Elite. (Investigar)


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

22.
De momento nada.
Con los usuarios jack y (jason, david, freddy) vamos a ver si podemos enumerarlos en el SSH. Pero parece que está parcheado o no se puede.

80.
Whatweb: HTTPServer lighttpd/1.4.45
(Fuzzear. Enumerar URIS).
Es estática la web, no se ve mucha interacción.

5000.
Whatweb: HTPPServer Werkzeug/1.0.1 Python3.6.9, esto me podría hacer pensar en un SSTI.
(Fuzzear. Enumerar URIS y recursos .py).
Es un Werkzeug, podemos probar la ruta típica /console pero no existe.

En la web, en un link se meciona a jack.
En otro link se meciona a (jason, david, freddy) y se menciona que tienen cuentas con privilegios en el sistema que quieren eliminar.

Esos links, se cargan con un id, de primeras no se acontece un SQLInjection, pero podemos ver cómo se comportan esos valores, porque sale estado 500 si el identificador no existe.

Así que fuzzeemos para ver cuáles son los id válidos en base al tamaño en la respuesta y código de esta, esto con wfuzz.
En un rango de 0000 a 9999 nada de nada, solo los mismos.

De momento no hay un lugar donde probar un SSTI. 


31337.
Como que NMAP intentó autenticarse pero nada, hay unas ruta expuestas del sistema.

Cuando nos intentamos conectarnos con nc al 31337, nos pide credenciales.
<span style="color:#88c425">nc 192.168.0.18 31337</span> 
Podríamos brute forcear para probar credenciales básicas, tipo root:root, jack:jack...

Script en Python:

![[Pasted image 20240605044352.png]]
![[Pasted image 20240605044423.png]]

Y bueno, nos reporta que es guest:guest, con eso nos conectamos al Sistema de Ticketing.

Y como habíamos visto que habían tickets en el HTTP:5000, podríamos intentar hacer uno, esto porque el Ticketing conecta con la web.


5000 - 31337
Si yo hago
<span style="color:#88c425">open</span> :    y le agrego un Tittle y una Description, al recargar la web, se puede ver.
Ya puedo representar cosas.

Y como el Werkzeug con Python está, en el puerto 5000, puedo probar un SSTI.
Si hago
![[Pasted image 20240607025212.png]]
En la Description sí se ve representado en la web las dos operatorias.

Intentar Ejecutar Comando.
En PayloadAllTheThings
Llamando a subprocess.Popen
![[Pasted image 20240607025813.png]]

Lo usamos para ponerlo un campo del Ticket.
Y en la web sí se ve representado.

Así que ganamos acceso al sistema.

## Escalada

Vemos 3 usuarios a nivel de sistema, en el /opt hay archivos ocultos.
id :    no tenemos permisos asignados.
Al enumerar SUID no vemos mucho.

En /opt
Los archivos tienen extensión .pyc
Están compilados y no es legible, pero podemos aplicar el proceso inverso.

En mi máquina
pip install uncompyle6 :    para descargarlo.

Y nos transferimos los archivos a mi máquina

Con uncompyle6
![[Pasted image 20240607034435.png]]
Para pasarlos a script.
Viendo un poquito, toma todos los archivos que terminen en .json del dir /saint y de /tmp

Vamos a subir pspy para enumerar tareas que se ejecutan a intervalos regulares de tiempo.
Como tiene internet, hacemos un wget de la url del pspy64 en la máquina víctima. (O si no, monto un server con python y luego descargamos en la máquina víctima).

Y lo ejecutamos.
Podemos ver que el UID=1000 (saint) ejecuta
/home/saint/.sync-data/syncer.py

Los dos scripts están relacionados.
Dice que los archivos .jason deben tener "URL" y "Output".
Creamos archivos en /tmp con la estructura que vimos que debe tener.
<span style="color:#88c425">touch 02-04-2024.config.json</span> :    en máquina víctima.
Donde le meto:
![[Pasted image 20240608174927.png]]
Y nos creamos el archivo "test" en mi sistema.
Compartimos un servidor http con python.
Y a esperar.
![[Pasted image 20240608175124.png]]
Ojo.
Y se crea el archivo de "prueba" en la máquina víctima, que contiene lo que yo puse en el "test".
Y a parte lo crea el usuario saint.

Voy a crear pares de clave ssh.
<span style="color:#88c425">rm ~/.ssh/*</span> :    borrar todo lo del .ssh
<span style="color:#88c425">ssh-keygen</span> :    crear las claves.
<span style="color:#88c425">cat ~/.ssh/id_rsa.pub</span> :    ver la clave pública.
<span style="color:#88c425">cat ~/.ssh/id_rsa.pub > authorized_keys</span> :    para meterlo ahí.

Montamos un servidor http con python.

Y el .json lo cambiamos a:
![[Pasted image 20240608181114.png]]


Nos conectamos a la máquina como saint, y pa dentro.

## Escalada 2

Están los ficheros de python, cosas varias pero nada importante.
<span style="color:#88c425">id</span> :    no vemos nada.
<span style="color:#88c425">sudo -l</span> :    uy cuidadito, puedo ejecutar adduser.
<span style="color:#88c425">sudo adduser joelkami --gid 0</span> :    porque no podemos hacer --uid 0, le ponemos una contraseña y todo enter.
<span style="color:#88c425">su joelkami</span> y pongo la password.

¿Esto de qué sirve?
Si yo creo un archivo en /tmp por ejemplo, lo creo como propietario joelkami y root como grupo asignado.

Ok, yo puedo crear un usuario (saint puede), saint no puede leer el /etc/sudoers, pero los grupos sí.

Así que con el usuario joelkami lo puedo ver.
Dice que jason puede ejecutar ap-get. PERO, el user no existe.

Lo creamos.

Y en gtfobins vemos qué se puede hacer con apt-get
Copiamos, le ponemos que para una bash y pa deeeentro.

Finalizada.





