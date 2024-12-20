# GreenHorn

Tags: [[_cms]],[[_webFuzzing]],[[_cracking]],[[_cve]],[[_webExploitation]],[[_analysis]]

## Primeros pasos
(Reconocimiento).

<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Activamos</span> <span style="color:#ff2dc0">VPN</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Linux</span>.

<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>.
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Jammy</span> - <span style="color:#ecacb6">Hirsute</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.9p1</span>
<span style="color:#ff669c">80</span> - HTTP  <span style="color:#379075">Nginx 1.18.0</span>
<span style="color:#ff669c">3000</span> - REACT <span style="color:#379075">http</span>.

## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. Lo normal, <span style="color:#07b4f2">aplica</span> un <span style="color:#ff2dc0">redirect</span> pero<span style="color:#07b4f2"> no conoce</span> el <span style="color:#ff2dc0">dominio</span>, lo añadimos en <span style="color:#ecacb6">/etc/hosts</span>
<span style="color:#07b4f2">Nuevamente</span> lo lanzamos, y <span style="color:#07b4f2">vemos</span> un <span style="color:#ff2dc0">Pluck-CMS</span>.

<span style="color:#ff2dc0">Pluck-CMS</span>.
En <span style="color:#ff2dc0">PHP</span>, <span style="color:#ff2dc0">CMS</span>, hay <span style="color:#ff2dc0">vulnerabilidades</span>.

<span style="color:#ff2dc0">Desde la web</span>.
<span style="color:#bef202">Sin subdominio</span>. <span style="color:#bef202">Con subdominio</span>. <span style="color:#379075">Ve se lo mismo</span>.
<span style="color:#07b4f2">Usuarios</span>
<span style="color:#ecacb6">admin</span>
<span style="color:#ecacb6">pluck</span>
<span style="color:#ecacb6">Mr. Green</span>

Hay <span style="color:#07b4f2">desde</span> la <span style="color:#ff2dc0">raíz</span>
<span style="color:#ecacb6">/?file=welcome-to-greenhorn
</span>
Pero vemos que <span style="color:#07b4f2">está</span> <span style="color:#ff2dc0">securizado</span>. <span style="color:#07b4f2">No podemos</span> por donde queramos.

<span style="color:#07b4f2">Hay</span> un <span style="color:#ff2dc0">login</span>.
<span style="color:#07b4f2">Para</span> entrar como <span style="color:#ecacb6">admin</span>.
<span style="color:#ecacb6">pluck 4.7.18 </span>-> <span style="color:#ff2dc0">Searchsploit</span>. <span style="color:#ff2dc0">RCE</span>, <span style="color:#ff2dc0">XSS</span>. (<span style="color:#379075">Authenticated</span>).

Así que <span style="color:#07b4f2">debemos</span> tratar de <span style="color:#07b4f2">entrar</span> antes.

<span style="color:#ff2dc0">Fuzz Subdominios</span>.
<span style="color:#07b4f2">Vemos</span> que <span style="color:#07b4f2">nada</span>.

<span style="color:#ff2dc0">Fuzz</span> <span style="color:#ecacb6">/?file=welcome-to-greenhorn</span>.
<span style="color:#07b4f2">No hay </span>mucho.

<span style="color:#ff2dc0">Fuzz raíz</span>.
<span style="color:#07b4f2">301</span>.
<span style="color:#ecacb6">/images</span>
<span style="color:#ecacb6">/docs</span> :    dentro <span style="color:#07b4f2">encuentro</span>, <span style="color:#ecacb6">README</span>, <span style="color:#ecacb6">CHANGES</span>, <span style="color:#ecacb6">COPYING</span>. <span style="color:#07b4f2">No mucho</span>.
<span style="color:#ecacb6">/files</span>
<span style="color:#ecacb6">/data</span> 

Probar<span style="color:#07b4f2"> qué se ve</span> en el <span style="color:#ff2dc0">Redirect</span>. Pero <span style="color:#07b4f2">nada</span>.

<span style="color:#ff2dc0">Rutas defecto</span>.
Es de <span style="color:#ff2dc0">código abierto</span>. Podemos <span style="color:#07b4f2">ver</span> las <span style="color:#ff2dc0">rutas</span> y su <span style="color:#ff2dc0">contenido</span> en
https://github.com/pluck-cms/pluck :    el <span style="color:#ff2dc0">análisis</span> está <span style="color:#ff2dc0">maniaco</span>.

<span style="color:#bef202">Vamos a 3000</span>.

<span style="color:#07b4f2">Probamos</span> la contraseña <span style="color:#ecacb6">iloveyou1</span> y <span style="color:#07b4f2">entramos</span>.
Ya podemos <span style="color:#07b4f2">pensar en</span> las <span style="color:#ff2dc0">vulnerabilidades asociadas</span>.
Tenemos que <span style="color:#07b4f2">subir</span> un<span style="color:#ecacb6"> .zip</span> con un <span style="color:#ff2dc0">archivo</span> <span style="color:#07b4f2">dentro</span>.
Porque le <span style="color:#07b4f2">mandamos</span> el <span style="color:#07b4f2">nombre</span> del <span style="color:#ff2dc0">comprimido</span>, <span style="color:#07b4f2">lo</span> <span style="color:#ff2dc0">descomprime</span> y <span style="color:#07b4f2">crea</span> un <span style="color:#ff2dc0">directorio</span> <span style="color:#07b4f2">con ese nombre</span>.
<span style="color:#07b4f2">Dentro</span> de <span style="color:#07b4f2">dicho</span> <span style="color:#ff2dc0">directorio</span> <span style="color:#07b4f2">almacena</span> su <span style="color:#ff2dc0">contenido</span>, en <span style="color:#07b4f2">nuestro caso</span>, un <span style="color:#ecacb6">cmd.php</span>

Después, <span style="color:#07b4f2">apuntamos a él</span> para que lo <span style="color:#ff2dc0">interprete</span>.

<span style="color:#bef202">Pasos</span>.
<span style="color:#ff2dc0">Comprimido</span>.
<span style="color:#ecacb6">cmd.php</span>
https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php :    lo <span style="color:#07b4f2">ajustamos</span> con <span style="color:#07b4f2">nuestra</span> <span style="color:#ff2dc0">IP</span> y <span style="color:#ff669c">puerto</span> en <span style="color:#ff2dc0">escucha</span>. <span style="color:#379075">Probé con uno de los que hacemos pero nada</span>.

<span style="color:#ff2dc0">Comprimir</span>.
<span style="color:#88c425">gzip cmd.php </span>:    al <span style="color:#ff2dc0">comprimido</span> <span style="color:#ecacb6">cmd.php.zip</span> resultante <span style="color:#07b4f2">le cambiamos</span> el <span style="color:#07b4f2">nombre a</span> <span style="color:#ecacb6">payload.zip</span>
<span style="color:#379075">Otra forma</span>.
<span style="color:#88c425">7z a payload.zip cmd.php</span>
<span style="color:#ecacb6">payload.zip</span> es <span style="color:#07b4f2">como queremos llamar </span>al <span style="color:#ff2dc0">comprimido</span>.
<span style="color:#ecacb6">cmd.php</span> es el <span style="color:#ff2dc0">archivo</span> <span style="color:#07b4f2">a</span> <span style="color:#ff2dc0">comprimir</span>.

<span style="color:#ff2dc0">Ganar acceso</span>.
<span style="color:#379075">Forma 1</span>.
http://greenhorn.htb/admin.php?action=managemodules -> <span style="color:#ecacb6">Install a module</span> -> (<span style="color:#379075">Elegimos el </span><span style="color:#ecacb6">payload.zip</span>)
Me pongo en <span style="color:#ff2dc0">escucha</span>, le <span style="color:#07b4f2">doy a</span> <span style="color:#ecacb6">Upload</span> y gano acceso.

<span style="color:#379075">Forma 2</span>.
Usamos el <span style="color:#ff2dc0">script</span> de 
https://github.com/Rai2en/CVE-2023-50564_Pluck-v4.7.18_PoC/blob/main/poc.py :    para <span style="color:#07b4f2">pasarle</span> el <span style="color:#ecacb6">.zip</span> a subir.
Script.
![[Pasted image 20240908185507.png]]
Hay que <span style="color:#379075">ajustar</span> el <span style="color:#ff2dc0">dominio</span>, <span style="color:#ff2dc0">credenciales</span>, <span style="color:#07b4f2">nombres</span> de los <span style="color:#ff2dc0">archivos</span> (<span style="color:#379075">nombre del comprimido al enviar y cuando el servidor descomprima, y el nombre del archivo comprimido, cmd.php</span>).
Y gano acceso.


<span style="color:#ff669c">3000</span>.
Vemos que <span style="color:#07b4f2">es</span> <span style="color:#ff2dc0">Git</span>.
<span style="color:#ecacb6">Gite 1.21.11</span> 
Podemos <span style="color:#07b4f2">registrarnos</span>, lo hacemos.

<span style="color:#ff2dc0">Fuzz Recursos</span>.
Puro <span style="color:#ff2dc0">redirect</span> a <span style="color:#ecacb6">/user/login</span>

Vemos un <span style="color:#ff2dc0">repositorio</span> <span style="color:#07b4f2">de</span> <span style="color:#ecacb6">GreenAdmin/GreenHorn</span>
Ojo, es el <span style="color:#ff2dc0">proyecto</span> <span style="color:#07b4f2">como lo encontramos en</span> <span style="color:#ff2dc0">internet</span>, <span style="color:#ff2dc0">analizar</span>.
Vemos un <span style="color:#07b4f2">un</span>  <span style="color:#ff2dc0">commit</span>. <span style="color:#379075">Enumeramos pero no mucho</span>.
<span style="color:#07b4f2">Vemos</span> un <span style="color:#ff2dc0">recurso</span> que <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">repositorio original</span> no me aparecía <span style="color:#07b4f2">nada</span>, <span style="color:#07b4f2">pero aquí sí</span>,
<span style="color:#ecacb6">require_once 'data/settings/pass.php';</span>
<span style="color:#ecacb6">require_once 'data/settings/token.php';</span>
<span style="color:#07b4f2">Están en</span> <span style="color:#ff2dc0">SHA512</span>.

Los <span style="color:#07b4f2">intentamos</span> <span style="color:#ff2dc0">crackear</span>.
<span style="color:#ecacb6">pass.php</span> -> <span style="color:#ecacb6">iloveyou1</span> 
<span style="color:#ecacb6">token.php</span> -> <span style="color:#ff9a57">NO</span> SE PUEDE.

<span style="color:#bef202">Volvemos a 80</span>.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Focal.

<span style="color:#07b4f2">Usuarios válidos</span> a nivel de <span style="color:#ff2dc0">sistema</span>
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">git</span>
<span style="color:#ecacb6">junior</span>

<span style="color:#07b4f2">Reutilizamos</span> <span style="color:#ff2dc0">contraseña</span> <span style="color:#ecacb6">iloveyou1</span> y <span style="color:#07b4f2">nos conectamos como</span> <span style="color:#ecacb6">junior</span> (<span style="color:#379075">podríamos por ssh ya</span>).
En <span style="color:#07b4f2">su</span> <span style="color:#ff2dc0">directorio</span> personal vemos un <span style="color:#ff2dc0">PDF</span>, me <span style="color:#07b4f2">lo traigo</span> a <span style="color:#07b4f2">mi equipo</span>.
Nos <span style="color:#07b4f2">platican</span> que <span style="color:#07b4f2">han instalado</span> <span style="color:#ff2dc0">OpenVAS</span> en el <span style="color:#ff2dc0">servidor</span>, para <span style="color:#07b4f2">identificar</span> <span style="color:#ff2dc0">vulnerabilidades</span>.
Solo el usuario <span style="color:#ecacb6">root</span> <span style="color:#07b4f2">puede</span> <span style="color:#ff2dc0">ejecutarlo</span>, da un ejemplo
<span style="color:#88c425">sudo /usr/sbin/openvas</span> 
Introduce la contraseña, pero está borrosa.
Ya <span style="color:#07b4f2">le hice</span> un <span style="color:#ff2dc0">strings</span> <span style="color:#07b4f2">al documento</span> pero <span style="color:#07b4f2">nada</span>.

Podríamos <span style="color:#07b4f2">pensar</span> en <span style="color:#ff2dc0">tareas cron</span>, que esté <span style="color:#07b4f2">realizando</span> <span style="color:#ecacb6">root</span>.
<span style="color:#ecacb6">root</span> está constantemente <span style="color:#ff2dc0">ejecutando</span>
<span style="color:#88c425">sleep 10</span>
No sé por que.

<span style="color:#ff2dc0">Puertos internos</span>.
...<span style="color:#88c425">"88A4" | while read port; do  echo "$((0x$port))" ;done | sort -un</span>
<span style="color:#ecacb6">22</span>
<span style="color:#ecacb6">53</span>
<span style="color:#ecacb6">80</span>
<span style="color:#ecacb6">3306</span> -> <span style="color:#07b4f2">no pudimos</span> conectarnos.
<span style="color:#ecacb6">34964</span>
<span style="color:#ecacb6">34980</span>
<span style="color:#ecacb6">35504</span>
<span style="color:#ecacb6">35514</span>
<span style="color:#ecacb6">35648</span>
<span style="color:#ecacb6">35650</span>
<span style="color:#ecacb6">43092</span>
<span style="color:#ecacb6">43108</span>
<span style="color:#ecacb6">47814</span>
<span style="color:#ecacb6">47824</span>
<span style="color:#ecacb6">51632</span>
<span style="color:#ecacb6">51648</span>
<span style="color:#ecacb6">56276</span>
<span style="color:#ecacb6">57394</span>

Yo sigo <span style="color:#07b4f2">pensando</span> en la <span style="color:#07b4f2">idea</span> de que <span style="color:#07b4f2">necesitamos quitar</span> eso <span style="color:#07b4f2">borroso</span> para <span style="color:#07b4f2">ver</span> la <span style="color:#ff2dc0">password</span>.
Le <span style="color:#07b4f2">tomamos</span> <span style="color:#ff2dc0">captura</span> y <span style="color:#07b4f2">buscamos</span> un <span style="color:#ff2dc0">aplicativo</span> que pueda <span style="color:#07b4f2">quitarlo</span>, pero <span style="color:#07b4f2">nada</span>.
Busqué "<span style="color:#ecacb6">reconocer password de textos borrosos</span>" y me salió

https://www.microsiervos.com/archivo/seguridad/recuperar-textos-contrasenas-rostros-imagenes-pixeladas-mosaicos.html
![[Pasted image 20240910114115.png]]

<span style="color:#07b4f2">Habla</span> de una <span style="color:#ff2dc0">herramienta</span> que puede <span style="color:#07b4f2">obtener</span> los <span style="color:#ff2dc0">pixeles</span> <span style="color:#07b4f2">que no están</span>.
https://github.com/spipm/Depix

<span style="color:#bef202">Pasos</span>.
+<span style="color:#07b4f2">Convertir con</span> herramienta <span style="color:#ff2dc0">pdfimages</span>, <span style="color:#ecacb6">OpenVas.pdf</span> <span style="color:#07b4f2">en formato</span> <span style="color:#ecacb6">.ppm</span>
<span style="color:#88c425">pdfimages OpenVas.pdf openvasTest</span> 

+Utilizar <span style="color:#ff2dc0">Depix</span>.
<span style="color:#88c425">python3 depix.py -p /home/joelkami/Documentos/HTB/Maquinas/GreenHorn/content/openvasTest-000.ppm -s images/searchimages/debruinseq_notepad_Windows10_closeAndSpaced.png -o test.png</span>
<span style="color:#ecacb6">-s</span> es <span style="color:#07b4f2">con el que hará</span> las <span style="color:#07b4f2">comparativas</span>.

<span style="color:#07b4f2">Vemos</span> la <span style="color:#07b4f2">imagen arreglada</span> (<span style="color:#379075">el output del programa, curioso cómo detecta esa parte solamente</span>).
![[Pasted image 20240910115909.png]]

<span style="color:#ecacb6">sidefromsidetheothersidesidefromsidetheotherside</span> 
Pero, <span style="color:#07b4f2">¿para qué?</span> 

Para <span style="color:#07b4f2">hacer</span>
<span style="color:#88c425">su root</span> :    <span style="color:#07b4f2">proporcionarlo como</span> <span style="color:#ff2dc0">contraseña</span> y pa deeeentro.

FIN.
