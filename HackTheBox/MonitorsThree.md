# MonitorsThree

Tags: [[_sqli]],[[_webFuzzing]][[_webExploitation]],[[_internalPortAbusing]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Jammy</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.9p1</span>
<span style="color:#ff669c">80</span> - HTTP. <span style="color:#379075">Nginx 1.18.0</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">whatweb</span>. <span style="color:#ff2dc0">Redirect</span> a <span style="color:#ecacb6">monitorsthree</span>, lo metemos en el <span style="color:#ecacb6">/etc/hosts</span>

<span style="color:#ff2dc0">Desde la web</span>. 
Vemos que <span style="color:#07b4f2">hay</span> un <span style="color:#ff2dc0">login</span>.
Hay un <span style="color:#ecacb6">forgot_password.php</span> donde <span style="color:#07b4f2">podemos</span> <span style="color:#ff2dc0">enumerar</span> <span style="color:#ecacb6">usuarios</span>. Incluso al <span style="color:#07b4f2">meter</span> una <span style="color:#07b4f2">comilla</span>, ocurre un <span style="color:#07b4f2">error</span> de <span style="color:#ff2dc0">sintaxis SQL</span>, así que <span style="color:#07b4f2">tratemos</span> de <span style="color:#07b4f2">generar</span> un <span style="color:#ff2dc0">SQLI</span>.

Posible <span style="color:#ff2dc0">Users</span>.
<span style="color:#ecacb6">Nicola Jhonson</span>
<span style="color:#ecacb6">Glenn Jones</span>

<span style="color:#ff2dc0">Correos</span>.
<span style="color:#ecacb6">sales@monitorsthree.htb</span> 

<span style="color:#ff2dc0">SQLI</span>.
Esto <span style="color:#07b4f2">en</span> el apartado de <span style="color:#ecacb6">forgot_password.php</span>
<span style="color:#ecacb6">9 columnas</span>.
<span style="color:#07b4f2">Parece</span> que es <span style="color:#ff2dc0">SQLI Error Boolean Based Blind</span>.
A <span style="color:#07b4f2">darle</span> hasta <span style="color:#07b4f2">tener</span> el <span style="color:#ff2dc0">script</span>...
![[Pasted image 20241031144149.png]]

<span style="color:#ff2dc0">Databases</span>.
<span style="color:#ecacb6">information_schema</span>
<span style="color:#ecacb6">monitorsthree_bd</span> 

<span style="color:#ff2dc0">Tables</span> from <span style="color:#ecacb6">monitorsthree_db</span>: <span style="color:#ecacb6">invoices,customers,changelog,tasks,invoice_tasks,users</span>
<span style="color:#ff2dc0">Columns</span> from <span style="color:#ecacb6">users</span> in <span style="color:#ecacb6">monitorsthree_db</span>:<span style="color:#ecacb6">id,username,email,password,name,position,dob,start_date,salary</span>
<span style="color:#ff2dc0">Information</span> in <span style="color:#ecacb6">username,password</span> 
<span style="color:#ecacb6">admin:31a181c8372e3afc59dab863430610e8</span>
Sería -> <span style="color:#ecacb6">admin:greencacti2001</span> 
<span style="color:#07b4f2">Hay más información</span>.


<span style="color:#07b4f2">Entramos</span> como <span style="color:#ff2dc0">admin</span> en el <span style="color:#ecacb6">login</span>.
Vemos de todo pero <span style="color:#07b4f2">no</span> hay <span style="color:#07b4f2">mucho</span>.

<span style="color:#ff2dc0">Enumerar Subdominios</span>.
<span style="color:#ecacb6">cacti.monitorsthree.htb</span>
<span style="color:#ff2dc0">Cacti</span>. <span style="color:#07b4f2">Herramienta</span> de código abierto, <span style="color:#ff2dc0">monitorear</span> y <span style="color:#ff2dc0">recopilar datos </span>de <span style="color:#ff2dc0">redes</span>.
<span style="color:#07b4f2">Nos</span> <span style="color:#ff2dc0">logueamos</span> como <span style="color:#ecacb6">admin</span>.
<span style="color:#ecacb6">Version 1.2.26</span>

Investigando <span style="color:#ff2dc0">vulnerabilidades</span>.
https://github.com/5ma1l/CVE-2024-25641
<span style="color:#ff2dc0">Ejecutamos</span> lo indicado, <span style="color:#07b4f2">sobre</span> esta <span style="color:#ff2dc0">URL</span>
<span style="color:#ecacb6">http://cacti.monitorsthree.htb/cacti/</span>
Y pa deeentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Jammy.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">marcus</span> 

En <span style="color:#ecacb6">/var/www/html/app/admin/db.php</span> hay
<span style="color:#ecacb6">app_user:php_app_password</span> 

<span style="color:#ff669c">Puertos</span> abiertos.
<span style="color:#ecacb6">22</span>
<span style="color:#ecacb6">53</span>
<span style="color:#ecacb6">80</span>
<span style="color:#ecacb6">3306</span>
<span style="color:#ecacb6">8084</span>
<span style="color:#ecacb6">8200</span>
<span style="color:#ecacb6">36228</span>
<span style="color:#ecacb6">36798</span>
<span style="color:#ecacb6">40969</span>

Como siempre probamos de qué trata cada puerto.
El <span style="color:#ff669c">8200</span> es <span style="color:#ff2dc0">HTTP</span>. <span style="color:#ff2dc0">Tiny WebServer</span>.

<span style="color:#07b4f2">Probando</span>, vemos la <span style="color:#ff2dc0">contraseña</span> de <span style="color:#ecacb6">marcus</span>.
<span style="color:#ecacb6">marcus:12345678910</span> :    <span style="color:#07b4f2">pudimos</span> haber hecho <span style="color:#ff2dc0">brute force</span> sobre el <span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">Tenemos</span> su <span style="color:#ecacb6">id_rsa</span>

Investigando <span style="color:#07b4f2">más</span> acerca de<span style="color:#ff2dc0"> Tiny WebServer</span>, sobre el <span style="color:#ff2dc0">Duplicati Login</span>, encontramos <span style="color:#07b4f2">algo</span> acerca de un <span style="color:#ff2dc0">ByPass</span>.
https://medium.com/@STarXT/duplicati-bypassing-login-authentication-with-server-passphrase-024d6991e9ee

Hacemos las <span style="color:#07b4f2">pruebas pertinentes</span>.
<span style="color:#07b4f2">Buscamos</span> por archivos <span style="color:#ecacb6">.sqlite</span> en la <span style="color:#ff2dc0">máquina víctima</span> y <span style="color:#07b4f2">encontramos 2</span>, los descargamos, <span style="color:#07b4f2">uno</span> es donde <span style="color:#07b4f2">tiene</span> la información <span style="color:#07b4f2">importante</span>.
<span style="color:#ecacb6">Duplicati-server.sqlite</span> :    lo abrimos con <span style="color:#ff2dc0">sqlite3</span>, y <span style="color:#ff2dc0">enumeramos</span> la tabla <span style="color:#ecacb6">Option</span>.

Habiendo seguido los <span style="color:#07b4f2">pasos</span> para <span style="color:#07b4f2">replicar</span> el <span style="color:#ff2dc0">ByPass</span>, esto es lo <span style="color:#07b4f2">importante</span> al <span style="color:#07b4f2">momento</span> de<span style="color:#ff2dc0"> iniciar sesión</span> <span style="color:#07b4f2">después de tener</span> la <span style="color:#07b4f2">cadena</span> en <span style="color:#ff2dc0">Hexadecimal</span>.
Interceptando con <span style="color:#ff2dc0">BurpSuite</span>.
1.- Colocar <span style="color:#07b4f2">cualquier</span> <span style="color:#ecacb6">contraseña</span> y dar <span style="color:#89d3ec">enter</span> (<span style="color:#379075">en la web</span>).
2.- <span style="color:#ecacb6">Do Intercept</span> y <span style="color:#07b4f2">copiamos</span> el <span style="color:#ff2dc0">Nonce</span>.
3.- Vamos a la <span style="color:#ff2dc0">consola</span> desde el <span style="color:#ff2dc0">navegador</span>
<span style="color:#88c425">allow pasting</span>
<span style="color:#88c425">var noncedpwd = ...</span> (lo <span style="color:#07b4f2">demás</span>, la <span style="color:#07b4f2">cadena</span> en <span style="color:#ff2dc0">Hex</span> y el <span style="color:#ff2dc0">Nonce</span>).
<span style="color:#88c425">noncedpwd</span> :    ver su valor.
4.- <span style="color:#ecacb6">Forward</span>.
5.- En el <span style="color:#07b4f2">campo</span> <span style="color:#ecacb6">password</span> <span style="color:#07b4f2">colocamos</span> el <span style="color:#07b4f2">valor</span> de <span style="color:#ff2dc0">noncedpwd</span> y damos <span style="color:#89d3ec">enter</span>.

De esta manera <span style="color:#07b4f2">ya estaríamos dentro</span> del <span style="color:#ff2dc0">Duplicati</span>.


<span style="color:#ff2dc0">Enumerar</span> de todo.
Encontramos https://www.youtube.com/watch?v=fzQv8crBhXM

Nuestra idea es <span style="color:#07b4f2">meter</span> nuestra <span style="color:#ff2dc0">clave pública </span>como <span style="color:#ecacb6">authorized_keys</span> en <span style="color:#ecacb6">/root/.ssh</span>, <span style="color:#07b4f2">o traernos</span> la <span style="color:#ff2dc0">clave privada</span> de <span style="color:#ecacb6">root</span>. (<span style="color:#379075">En nuestro caso obtuvimos la flag</span>).

Cosas a <span style="color:#07b4f2">tener</span> en <span style="color:#07b4f2">cuenta</span>.
+Los <span style="color:#ff2dc0">backups</span> al <span style="color:#07b4f2">crearlos</span> y al <span style="color:#07b4f2">restaurar</span> <span style="color:#ecacb6">archivos</span>, se hacen <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">servidor</span> <span style="color:#07b4f2">mismo</span>.
+Un <span style="color:#ff2dc0">backup</span> es un <span style="color:#ff2dc0">comprimido</span>, cuando <span style="color:#07b4f2">se restaura</span> entonces <span style="color:#07b4f2">se</span> <span style="color:#ff2dc0">descomprimen</span> los <span style="color:#ecacb6">archivos</span>.
+<span style="color:#07b4f2">Olvidemos</span> las <span style="color:#07b4f2">rutas</span> que nos <span style="color:#07b4f2">dan</span> y <span style="color:#07b4f2">pongámoslas</span> como ya <span style="color:#07b4f2">sabemos</span>. También, si es <span style="color:#ecacb6">/home...</span> que sea <span style="color:#ecacb6">/source/home...</span>
+Se entiende, el <span style="color:#ecacb6">Destination</span> es a <span style="color:#07b4f2">donde</span> quiero <span style="color:#07b4f2">meter</span> el <span style="color:#ff2dc0">backup</span>, y el <span style="color:#ecacb6">Source Data</span> es<span style="color:#07b4f2"> lo que</span> quiero que <span style="color:#07b4f2">contenga</span>.

FIN.
