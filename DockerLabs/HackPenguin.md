# HackPenguin

Tags: [[_webFuzzing]],[[_bruteForce]],[[_analysis]],[[_permissionAbuse]]

Creamos un directorio como la IP o nombre de la máquina.
Creamos los directorios de trabajo.
Le hacemos un ping a la máquina.
Posible máquina Linux.

Empezamos el escaneo con NMAP.

Puerto 22 y 80 abiertos.
Identificamos el codename.
Parece ser un Ubuntu Jammy.

La web es la página por defecto de Apache.
Habría que fuzzear lo más probable.

Ya que estamos con NMAP, sigamos aquí.
--script=http-enum

No reporta mucho, usemos otro más potente.

Wfuzz.
En directorios nada, fuzeemos por extensiones.

Gobuster.
Encontré un archivo penguin.html

Hay una imagen de un pinguino, y el posible nombre de un usuario, "penguin".
Podemos pensar en esteganografía.
Pero es posible que no.


Probaremos un ataque de fuerza bruta con hydra, si no, seguimos enumerando la web por extensiones .jpg

Parece que de primeras no, a no ser que la password esté muy abajo en el rockyou.

Pues parece que sí se está aplicando esteganografía en la imagen.
Tendríamos que conseguir dicha información por fuerza bruta.

steg_brute.py :    va con python2.7
Descubrió contenido con la contraseña: chocolate 
La cual es de la imagen en sí.

Y usando steghide
<span style="color:#88c425">steghide extract -sf Untitled.jpeg -p chocolate  </span>

El resultado es un binario, pero tiene otra contraseña.
<span style="color:#88c425">keepass2john penguin.kdbx > hash</span> :    sacamos el hash que vamos a brute forcear.

<span style="color:#88c425">john --wordlist=/usr/share/wordlists/rockyou.txt hash</span>

password1 (penguin) :    siendo este la pass del .kdbx

Con esa contraseña accedemos al .kdbx

Descargar el kepass para ver el contenido.
Ya descargado, abrimos el .kdbx con la contraseña, podemos ver una contraseña:
pinguinomaravilloso123

No sé si sea del usuario pinguino que viene ahí dentro, o por el contrario sea del usuario "penguin".

RECAPITULANDO.
1.- Descargué la imagen.
2.- Usando la utilidad steg_brute.py, conseguimos por fuerza bruta el contenido dentro de la imagen, la cual era una contraseña.
3.- Con esa contraseña y usando steghide, extrajimos el contenido de la imagen, el cual es un binario.
4.- Con keepass2john, creamos un hash de ese .kdbx
5.- Con john hicimos un ataque de fuerza bruta para romper el  hash, dando como resultado una contraseña.
6.- Con dicha contraseña abrimos el .kdbx en el keepassxc
7.- Visualizamos la contraseña de un usuario, que es del SSH.


Una ves dentro, hay checar todo.

Hay un grupo, hachpenguin al cual pertenecemos.

Parece que hay un script que se ejecuta cada cierto tiempo, hace algo simple, pero lo podía modificar, lo usé para asignar SUID a la bash.

Y ya, eso es todo.





