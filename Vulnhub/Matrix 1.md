# Matrix 1

Tags: [[_crypto]],[[_sudoersAbusing]]

Primero le acomodamos bien para que agarre una IP (poner modo Bridge).

Organizamos TMUX.
Hacemos un escaneo en la red para determinar la IP de la máquina.
Mediante el OUI reconocemos la máquina que vamos a atacar.

Creamos un directorio como la IP o nombre de la máquina.
Ponemos la IP de la máquina en un lugar visible.
Creamos los directorios de trabajo.
Le hacemos un ping a la máquina.
Posible máquina Linux.

Empezamos el escaneo con NMAP.

No buscamos codename con launchpad, no se ve mucho en el escaneo.

Puertos abiertos.
22 - SSH 7.7
80 - HTTP SimpleHTTPServer. Python.
31337 - HTTP Simple HTTPServer. Python.

Enumerar.
22.
De momento nada.

80.
Whatweb. Lo típico.
Fuzzear. No hay mucho.

31337.
Whatweb. Lo típico.
En el código fuente, se ve algo como en base64. Se ve que es un comando a nivel de sistema, que mete texto en un archivo Cypher.matrix, el cual tiene data que se ve desde la web.

Fuzzear.
/31337.py uy uy uy.
Es un programa en python, solo hace la conexión.
Ahora por extensión .matrix para ver si hay más.

Cypher.matrix.
Son caracteres algo raros. Seguro algo cifrado.
Con brainfuck se puede descifrar.
Me da credenciales.
guest:k1ll0rXX :    las XX tengo que reemplazarlas.
Supongo que por SSH. Pero antes montemos un diccionario y todo lo que corresponda.

crunch. Sirve para hacer diccionarios.
<span style="color:#88c425">crunch 8 8 -t k1ll0r%@ > diccionary.txt</span>
<span style="color:#ecacb6">8</span> de mínimo de longitud.
<span style="color:#ecacb6">8</span> de máximo de longitud.
<span style="color:#ecacb6">-t </span>para indicarle las letras, ponemos <span style="color:#ecacb6">%</span> de números y <span style="color:#ecacb6">@</span> de letras en minúsculas.
Nota. Hacemos las combinatorias necesarias, empezamos primero con letras y luego números, etc.

Y las probamos para SSH con Hydra.
<span style="color:#88c425">hydra -l guest -P diccionary.txt ssh://192.168.0.17 -t 20 2>/dev/null</span>
Obtenemos la <span style="color:#07b4f2">password</span>: k1ll0r7n

Y pa dentro.
Es una restricted bash.
Escapemos, al conectarnos, metemos bash al final, para que cargue antes de que spawnee la shell.


## Elevar Privilegios

Listar capabilities, no hay mucho.
Hay otro usuarios.
sudo -l, puedo ejecutar como root un binario, y como trinity /bin/cp, pero también podemos ejecutar como quien queramos, lo que queramos.
sudo su :    meto la contraseña, y como root.


