# CA1

Tags: [[_bruteForce]],[[_bypassing]],[[_suidAbusing]]

Entrar en una sesión de TMUX.
1.- Crear un directorio como el nombre de la máquina.
2.- Un escaneo en la red con arp-scan, para ver la máquina víctima.
(Arreglé el problema de la asignación de la IP)
3.- Poner la IP de la máquina en un lugar visible, con settarget.
4.- Verificar que la máquina esté encendida con un PING.
5.- Crear los directorios de trabajo.

## Enumeración

1.- nmap para hacer reconocimiento de los puertos.
2.- Análisis exhaustivo de los puertos abiertos.

Veremos su OS en launchpad.
Dan resultados de:
+Ubuntu Sid
+Ubuntu Impish

Tiene el ssh abierto, pero no poseo credenciales de momento.

Tiene un una página web http por el puerto 80

Tiene una base de datos MySQL por el puerto 3306

Tiene un muysqlx por el puerto 33060


A primera instancia, analicemos el puerto 80.
Con whatweb, vemos que es un tipo login (en el escaneo targeted también figura eso).
Es un login que pide email y password.
Es como de algo de qdPM. Es de gestión de proyectos.

Podríamos fuzzear para encontrar directorios o archivos.

## Reconocimiento

Vamos a ver si tiene una vulnerabilidad contemplada en searhsploit.
En internet exploit-db.com también se ve una vulnerabilidad interesante.
Es un .txt, vamos a checarlo.
Nos dice una ruta en la web donde hay contraseñas.
Lo descargamos, abrimos y vemos un usuario y contraseña. Las gaurdamos.

3.-
Probarlas en el Loign sería raro, porque pide un correo.

Probaremos por ssh. En un principio no se puede.

Hay que ver todo, es de una base de datos, hasta en el nombre del archivo lo dice.
Es de MySQL.
Nos conectamos a la DB por ssh. No se puede en un principio.
Puede que sea de una DB, pero solo son credenciales, hay que pensar más.

Intentamos conectarnos a la DB con mysql.
<span style="color:#88c425">mysql -h localhost -u root -p</span> :    -h de host, la IP de la víctima, -u de usuario, -p para después poner contraseña.

Y estamos dentro de la DB.
Usando sentencias SQL vamos a buscar cosillas.

Encontramos usuarios y contraseñas en base64. (les hacemos un base64 -d y chinpum).


----
No sé para qué puedan ser, ssh está abierto.
Probé los usuarios con la inicial mayúscula y nah, después con el nombre de usuario en minúsculas, y el usuario es dexter.
Estamos dentro.

-----

Para no hacerlo uno por uno.
Creamos un archivo de usuarios.
Otro de contraseñas.
Con hydra:
<span style="color:#88c425">hydra -L "users" -P "passwords" ssh://ipVíctima</span>
-L para indicarle el archivo de usuarios.
-P para indicarle el archivo de contraseñas.
ssh con la IP de la máquina víctima. Porque está abierto.
Para ver qué usuario y contraseña son correctas, es como que prueba todas las contraseñas para un usuario, y así con todos.

----
CUIADAO, hay dos usuario con credenciales válidas.
dexter
travis


Una vez dentro.
Y vemos que es un Debian bullseye.

## Escalada

Checamos privilegios.
sudo -l
id

La nota dice que el contenido de los ejecutables se puede ver parcialmente.

Hacemos:
cat /etc/passwd
Vemos 2 usuarios a nivel de sistema:
dexter
travis
No sé si tenga que entrar ahí, de primeras no puedo.

<span style="color:#88c425">find \-perm -user root -4000 2>/dev/null</span> :    se hace desde la raíz, para ver archivos por permisos suid, cuyo propietario sea root.

Hay un /opt/get_access

Con strings, veo que ocupa cat, para ver el contenido de un archivo.
Cosas a probar:
path hijacking.


Así que en tmp
<span style="color:#88c425">touch cat</span> :    para crear mi propio cat
<span style="color:#88c425">chmod +x cat</span>
Exportamos el PATH para que empiece a buscar por tmp.
<span style="color:#88c425">export PATH=/tmp:$PATH</span> :    lo copié de antes, puse tmp y luego pegué el resto.

En el cat que creé, pongo:
<span style="color:#88c425">chmod u+s /bin/bash</span> :    para que le asigne permiso suid a la bash.

Ejecuto el /opt/get_access
Y se le asignó el suid a la bash.
Hacemos:
bash -p :    -p de privilage.
Y chinpum.

Volvemos a dejar el PATH como antes, visualizamos la flag, y ya.
