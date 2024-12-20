# Blurry

Tags: [[_cve]],[[_webExploitation]],[[_sudoersAbusing]],[[_libraryHijacking]]

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
<span style="color:#07b4f2">Buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>, <span style="color:#bef202">un posible</span> <span style="color:#ecacb6">Ubuntu Bullseye/Jammy</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">22</span> - SSH. <span style="color:#379075">Version 8.4p1</span>
<span style="color:#ff669c">80</span> - HTTP. <span style="color:#379075">Nginx 1.18.0</span>


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">22</span>.
<span style="color:#07b4f2">No tengo</span> <span style="color:#ff2dc0">credenciales</span> de momento.


<span style="color:#ff669c">80</span>.
<span style="color:#ff2dc0">NMAP</span>.
Me <span style="color:#ff2dc0">redirige</span> a <span style="color:#ecacb6">app.blurry.htb</span>
Contemplamos el <span style="color:#ff2dc0">dominio</span> y <span style="color:#ff2dc0">subdominio</span> en el <span style="color:#ecacb6">/etc/hosts</span>

<span style="color:#ff2dc0">whatweb</span>. Sobre <span style="color:#ecacb6">app.blurry.htb</span> <span style="color:#ff2dc0">Title</span> "<span style="color:#ecacb6">ClearML</span>", poco más.

<span style="color:#ff2dc0">Desde la web</span>.
<span style="color:#ff2dc0">ClearML</span>. 
<span style="color:#379075">ClearML es una plataforma de inteligencia artificial de código abierto y de extremo a extremo diseñada para optimizar la adopción de la inteligencia artificial y todo el ciclo de vida del desarrollo</span>.

Enumerando la web, vemos que <span style="color:#07b4f2">nos proporcionan</span> <span style="color:#ff2dc0">claves</span>, y podemos <span style="color:#07b4f2">crear</span> un <span style="color:#ff2dc0">archivo</span>.

Investigando.
Vemos que es <span style="color:#07b4f2">posible</span> hacer un <span style="color:#ff2dc0">Pickle Deserialization Attack</span>.
Hay un <span style="color:#ecacb6">CVE-2024-24590</span>
https://medium.com/@vishalchaudharydevsec/hacking-clearml-using-malicious-pickle-file-upload-pickle-deserialization-41182d731cd2 :    la <span style="color:#07b4f2">mayoría</span> de lo que hay que hay que <span style="color:#07b4f2">hacer</span> te <span style="color:#07b4f2">lo dice</span> la <span style="color:#ff2dc0">web</span>.
<span style="color:#07b4f2">Retocamos</span> unas cosas del <span style="color:#ff2dc0">script</span>.
![[Pasted image 20241005225016.png]]
<span style="color:#379075">Consideremos quitar</span> <span style="color:#ecacb6">extension_name</span> 

Otro <span style="color:#ff2dc0">script</span>, este <span style="color:#07b4f2">funciona bien</span>, probablemente sea <span style="color:#07b4f2">por</span> el <span style="color:#ecacb6">project_name</span>, que es igual a <span style="color:#ecacb6">Black Swan</span> (<span style="color:#379075">el cual se inicia de primeras (ya dentro podemos ver que sí, solo los guarda los que tienen ese nombre, además de que le añade la extensión .pkl)</span>).
![[Pasted image 20241005230147.png]]

Puestos en <span style="color:#ff2dc0">escucha</span>, <span style="color:#07b4f2">esperamos</span> eso sí, y pa deentro.


## Escalada

Enumerar todo el sistema.
Efectivamente, Ubuntu Bullseye.

<span style="color:#07b4f2">Usuarios</span> válidos a <span style="color:#ff2dc0">nivel</span> de <span style="color:#ff2dc0">sistema</span>.
<span style="color:#ecacb6">root</span>
<span style="color:#ecacb6">jippity</span>

<span style="color:#88c425">sudo -l</span>
<span style="color:#ecacb6">(root) NOPASSWD: /usr/bin/evaluate_model /models/*.pth</span> 

En <span style="color:#ecacb6">/models</span> hay un<span style="color:#ecacb6"> .pth</span> de prueba y un <span style="color:#ff2dc0">evaluate_model.py</span>

Yo hago 
<span style="color:#88c425">sudo /usr/bin/evaluate_model /models/demo_model.pth</span> :    se lo paso a un <span style="color:#ff2dc0">script</span> de <span style="color:#ff2dc0">bash</span>, que determina <span style="color:#07b4f2">si es válido</span> y todo, de ser así, se <span style="color:#07b4f2">lo pasa al</span> <span style="color:#ff2dc0">evaluate_model.py</span> que es de <span style="color:#ff2dc0">python</span>.

Lo <span style="color:#07b4f2">curioso</span> es que <span style="color:#07b4f2">cuando</span> hago
<span style="color:#88c425">sudo /usr/bin/evaluate_model /models/demo_model.pth</span> :    <span style="color:#07b4f2">llega</span> al de <span style="color:#ff2dc0">python</span> y <span style="color:#07b4f2">busca</span> por un <span style="color:#ecacb6">torch.py</span>, me salen unos mensajes. Más que buscar, lo importa al principio (<span style="color:#ecacb6">import torch</span>), pues lo <span style="color:#07b4f2">reemplazamos</span>.
Así que <span style="color:#07b4f2">hago</span>
<span style="color:#88c425">echo 'import os; os.system("bash")' > /modules/torch.py</span> :    la <span style="color:#07b4f2">extensión importa</span>, y debe ser<span style="color:#07b4f2"> en esa</span> <span style="color:#ff2dc0">ruta</span>, porque <span style="color:#07b4f2">ahí está</span> el <span style="color:#ff2dc0">programa</span> de <span style="color:#ff2dc0">python</span>.
<span style="color:#88c425">sudo /usr/bin/evaluate_model /models/demo_model.pth</span> :    y pa deeeentro.

FIN.
