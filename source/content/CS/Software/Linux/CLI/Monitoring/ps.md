`ps` (process status) muestra una instantánea de los procesos activos en el sistema. A diferencia de [[top]], no es interactivo: reporta el estado en el momento de la ejecución. Acepta tres sintaxis de opciones —UNIX (prefijo `-`), BSD (sin prefijo) y GNU (prefijo `--`)— que pueden mezclarse y producen resultados ligeramente distintos. Es la base del diagnóstico de procesos, jerarquías padre-hijo y consumo de CPU/memoria.

```
ps [opciones] [PID]
```

| PID | TTY | STAT | TIME | CMD |
| --- | --- | --- | --- | --- |
| ID del proceso | El dispositivo terminal en el que corre el proceso | El estado del proceso ([[ps#Process states]]) | Tiempo que el proceso ha estado corriendo en la CPU | El comando usado para ejecutar el programa |

# Selección simple de procesos

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-A`, `-e` | Todos los procesos del sistema. | `ps -e` |
| `-a` | Todos los procesos con terminal, excepto líderes de sesión. | `ps -a` |
| `a` (BSD) | Procesos de todos los usuarios con terminal. | `ps ax` |
| `x` (BSD) | Procesos sin terminal de control (incluye daemons). | `ps ax` |
| `-d` | Todos los procesos excepto los líderes de sesión. | `ps -d` |
| `-N`, `--deselect` | Niega la selección: muestra los que NO cumplen el criterio. | `ps -ef -N` |
| `T` (BSD) | Procesos asociados a la terminal actual. | `ps T` |
| `r` (BSD) | Solo procesos en ejecución (running). | `ps r` |

# Selección por criterio

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-p`, `p`, `--pid` | Procesos por PID (uno o varios). | `ps -p 1234 5678` |
| `--ppid` | Procesos hijos de un PID padre. | `ps --ppid 1337` |
| `-q`, `q`, `--quick-pid` | Selección rápida por PID (sin orden ni recolección extra). | `ps -q 1234` |
| `-C` nombre | Procesos por nombre exacto de comando. | `ps -C nc` |
| `-u`, `u`, `--user` | Procesos por usuario efectivo (EUID). | `ps -u www-data` |
| `-U`, `--User` | Procesos por usuario real (RUID). | `ps -U root` |
| `-g`, `--group` | Procesos por grupo efectivo / nombre de sesión. | `ps -g audio` |
| `-G`, `--Group` | Procesos por grupo real (RGID). | `ps -G developers` |
| `-s`, `--sid` | Procesos por ID de sesión. | `ps -s 1` |
| `-t`, `t`, `--tty` | Procesos asociados a una terminal concreta. | `ps -t pts/0` |
| `-U root -u root u` | Todos los procesos corriendo como root (real y efectivo). | `ps -U root -u root u` |

# Control del formato de salida

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-f` | Formato completo (UID, PID, PPID, C, STIME, TTY, TIME, CMD). | `ps -ef` |
| `-F` | Formato completo extendido (añade SZ, RSS, PSR). | `ps -eF` |
| `-l` | Formato largo (incluye F, S, PRI, NI, ADDR, WCHAN). | `ps -l` |
| `l` (BSD) | Formato largo estilo BSD. | `ps axl` |
| `-o`, `--format` | Salida personalizada con columnas concretas. | `ps -eo pid,ppid,user,comm,args` |
| `-O` formato | Formato por defecto precargado más las columnas indicadas. | `ps -O pri,ni` |
| `o`, `u` (BSD) | Formato de usuario (USER, %CPU, %MEM, VSZ, RSS...). | `ps aux` |
| `j`, `-j` | Formato de jobs (PGID, SID). | `ps -j` |
| `-c` | Muestra la clase de planificación y la prioridad RT. | `ps -c -p 1` |
| `-y` | Con `-l`, sustituye ADDR por RSS (no muestra flags/dirección). | `ps -ly` |
| `--sort` | Ordena la salida por una o varias claves ([[ps#Ordenación de la salida]]). | `ps aux --sort=-%mem` |
| `-M`, `Z` | Añade columnas de etiqueta de seguridad (SELinux). | `ps -eM` |
| `--context` | Muestra el contexto de seguridad. | `ps --context` |
| `-w`, `w` (BSD) | Salida ancha; repetir (`ww`) elimina el truncado. | `ps -ef -ww` |
| `--cols`, `--columns`, `--width` | Fija el ancho de salida en columnas. | `ps -ef --width 200` |
| `--rows`, `--lines` | Fija el nº de filas de pantalla. | `ps --rows 50` |
| `--signames` | Muestra máscaras de señal con nombres en vez de hex. | `ps -eo pid,sig --signames` |

# Ordenación de la salida

`ps` no ordena por defecto: devuelve los procesos en el orden en que los lee de `/proc` (aproximadamente por PID ascendente). La ordenación explícita se solicita con `--sort` (GNU), su alias BSD `k`, o la sintaxis obsoleta `O`.

```
--sort  [+|-]clave[,[+|-]clave[,...]]
k       [+|-]clave[,[+|-]clave[,...]]
O       [+|-]k1[,[+|-]k2[,...]]
```

## Mecanismos de ordenación

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `--sort=CLAVE` | Ordena por una clave larga del juego de especificadores de formato. Dirección ascendente por defecto (numérica o lexicográfica). | `ps -e --sort=%mem` |
| `--sort CLAVE` | Misma opción con espacio en lugar de `=`. | `ps -e --sort pid` |
| `+CLAVE` | Reitera explícitamente la dirección ascendente. Opcional. | `ps aux --sort=+%cpu` |
| `-CLAVE` | Invierte a descendente **solo** la clave que precede. | `ps aux --sort=-%cpu` |
| `clave1,clave2,...` | Ordenación multinivel: `clave2` desempata los valores iguales de `clave1`. | `ps -eo user,pid,%cpu --sort=user,-%cpu` |
| `k spec` | Alias BSD de `--sort`. Sintaxis y claves idénticas; admite pegarse a otras opciones BSD. | `ps jaxkuid,-ppid,+pid` |
| `k spec` (separado) | La clave puede ir como argumento separado. | `ps kstart_time -ef` |
| `O spec` | Sintaxis BSD obsoleta con claves de **una letra** ([[ps#Claves cortas BSD obsoletas]]). Opción sobrecargada: heurística decide si es orden o formato. | `ps aux O-C` |
| `-O spec` | **No ordena.** Precarga el formato por defecto más las columnas indicadas. Usa `--sort` para evitar la ambigüedad de `O`. | `ps -O pri` |

> `--sort` ordena por el **valor interno** que maneja `ps`, no por el valor "cocinado" que se imprime. `--sort=tty` ordena por número de dispositivo, no por el nombre de terminal mostrado; `--sort=user` ordena por la cadena, pero `--sort=uid` por el entero. Para ordenar por el texto visible, canaliza a `sort(1)`.

> Cualquiera de los 198 especificadores que lista `ps L` se acepta como clave de `--sort`. Los especificadores de relleno (`_left`, `_right`, `_unlimited` y sus variantes `2`) son sintácticamente válidos pero no producen ninguna ordenación útil.

## Claves de CPU y planificación

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `%cpu` | Porcentaje de CPU (tiempo de CPU / tiempo de vida del proceso). Alias `pcpu`. | `ps aux --sort=-%cpu` |
| `pcpu` | Alias de `%cpu`. | `ps -eo pid,comm,pcpu --sort=-pcpu` |
| `c` | Utilización de procesador como entero. Alias `util`, `lastcpu` en cabecera `C`. | `ps -eo pid,c --sort=-c` |
| `util` | Alias de `c` (cabecera `C`). | `ps -eo pid,util --sort=-util` |
| `cp` | Uso de CPU en per-mil (décimas de porcentaje). Mayor resolución que `%cpu`. | `ps -eo pid,cp --sort=-cp` |
| `cuu` | Utilización de CPU en formato extendido `##.###`. | `ps -eo pid,cuu --sort=-cuu` |
| `cuc` | Utilización de CPU incluyendo hijos muertos, formato `##.###`. | `ps -eo pid,cuc --sort=-cuc` |
| `psr` | Procesador en el que se ejecutó por última vez. Alias `cpu`, `cpuid`, `lastcpu`. | `ps -eo pid,psr --sort=psr` |
| `cpu` | Alias de `psr`. | `ps -eo pid,cpu --sort=cpu` |
| `cpuid` | Alias de `psr`. | `ps -eo pid,cpuid --sort=cpuid` |
| `lastcpu` | Alias de `psr` (cabecera `C`). | `ps -eo pid,lastcpu --sort=lastcpu` |
| `sgi_p` | Procesador en el que se ejecuta ahora; `*` si no está en ejecución. | `ps -eo pid,sgi_p --sort=sgi_p` |
| `numa` | Nodo NUMA del último procesador usado. `-1` si no hay información. | `ps -eo pid,numa --sort=numa` |
| `pri` | Prioridad del proceso; número mayor = prioridad mayor. | `ps -eo pid,pri --sort=-pri` |
| `priority` | Escala de prioridad interna (cabecera `PRI`). | `ps -eo pid,priority --sort=priority` |
| `opri` | Prioridad en escala invertida respecto a `pri` (cabecera `PRI`). | `ps -eo pid,opri --sort=opri` |
| `intpri` | Prioridad interna, misma escala que `opri` (cabecera `PRI`). | `ps -eo pid,intpri --sort=intpri` |
| `ni` | Valor `nice`, de 19 (más amable) a -20. Alias `nice`. | `ps -eo pid,ni,comm --sort=ni` |
| `nice` | Alias de `ni`. | `ps -eo pid,nice --sort=-nice` |
| `rtprio` | Prioridad de tiempo real. | `ps -eo pid,rtprio,comm --sort=-rtprio` |
| `class` | Clase de planificación (`TS`, `FF`, `RR`, `B`, `ISO`, `IDL`, `DLN`). Alias `cls`, `policy`. | `ps -eo pid,class --sort=class` |
| `cls` | Alias de `class`. | `ps -eo pid,cls --sort=cls` |
| `policy` | Alias de `class` (cabecera `POL`). | `ps -eo pid,policy --sort=policy` |
| `sched` | Política de planificación como entero (0=OTHER … 6=DEADLINE). | `ps -eo pid,sched --sort=-sched` |
| `ag_id` | Identificador de autogroup del planificador CFS. | `ps -eo pid,ag_id --sort=ag_id` |
| `ag_nice` | Valor `nice` del autogroup. | `ps -eo pid,ag_nice --sort=ag_nice` |
| `pri_foo` | Escala interna alternativa de prioridad (cabecera `FOO`). Aparece en `ps L`, no documentada en `man ps`. | `ps -eo pid,pri_foo --sort=pri_foo` |
| `pri_bar` | Escala interna alternativa de prioridad (cabecera `BAR`). No documentada en `man ps`. | `ps -eo pid,pri_bar --sort=pri_bar` |
| `pri_baz` | Escala interna alternativa de prioridad (cabecera `BAZ`). No documentada en `man ps`. | `ps -eo pid,pri_baz --sort=pri_baz` |
| `pri_api` | Escala interna alternativa de prioridad (cabecera `API`). No documentada en `man ps`. | `ps -eo pid,pri_api --sort=pri_api` |

## Claves de memoria

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `%mem` | RSS respecto a la memoria física total, en porcentaje. Alias `pmem`. | `ps aux --sort=-%mem` |
| `pmem` | Alias de `%mem`. | `ps -eo pid,pmem --sort=-pmem` |
| `rss` | Resident set size en KiB (memoria física no swapeada). Alias `rssize`, `rsz`. | `ps -eo pid,rss,comm --sort=-rss` |
| `rssize` | Alias de `rss`. | `ps -eo pid,rssize --sort=-rssize` |
| `rsz` | Alias de `rss` (cabecera `RSZ`). | `ps -eo pid,rsz --sort=-rsz` |
| `sgi_rss` | RSS en estilo SGI (cabecera `RSS`). | `ps -eo pid,sgi_rss --sort=-sgi_rss` |
| `vsz` | Tamaño de memoria virtual en KiB. Alias `vsize`. | `ps -eo pid,vsz,comm --sort=-vsz` |
| `vsize` | Alias de `vsz`. | `ps -eo pid,vsize --sort=-vsize` |
| `sz` | Tamaño de la imagen del proceso en páginas físicas (texto+datos+pila). | `ps -eo pid,sz --sort=-sz` |
| `size` | Swap aproximado que haría falta si el proceso ensuciara todas sus páginas escribibles. | `ps -eo pid,size --sort=-size` |
| `m_size` | Tamaño total de memoria (cabecera `SIZE`). | `ps -eo pid,m_size --sort=-m_size` |
| `pss` | Proportional share size: memoria física con la compartida repartida entre los procesos que la mapean. | `ps -eo pid,pss,comm --sort=-pss` |
| `uss` | Unique set size: memoria física no compartida con ningún otro proceso. | `ps -eo pid,uss,comm --sort=-uss` |
| `drs` | Data resident set size: memoria privada reservada (DATA). | `ps -eo pid,drs --sort=-drs` |
| `m_drs` | Variante de `drs` (cabecera `DRS`). | `ps -eo pid,m_drs --sort=-m_drs` |
| `dsiz` | Tamaño del segmento de datos. | `ps -eo pid,dsiz --sort=-dsiz` |
| `trs` | Text resident set size: memoria física dedicada a código ejecutable. | `ps -eo pid,trs --sort=-trs` |
| `m_trs` | Variante de `trs` (cabecera `TRS`). | `ps -eo pid,m_trs --sort=-m_trs` |
| `trss` | Text resident set size (cabecera `TRSS`). | `ps -eo pid,trss --sort=-trss` |
| `tsiz` | Tamaño del segmento de texto. | `ps -eo pid,tsiz --sort=-tsiz` |
| `htprv` | Memoria privada respaldada por páginas hugetlbfs, no contada en `rss`/`pss`. | `ps -eo pid,htprv --sort=-htprv` |
| `htshr` | Memoria compartida respaldada por páginas hugetlbfs, no contada en `rss`/`pss`. | `ps -eo pid,htshr --sort=-htshr` |
| `maj_flt` | Número de fallos de página mayores (cabecera `MAJFL`). | `ps -eo pid,maj_flt --sort=-maj_flt` |
| `majflt` | Fallos de página mayores (cabecera `MAJFLT`). | `ps -eo pid,majflt --sort=-majflt` |
| `min_flt` | Número de fallos de página menores (cabecera `MINFL`). | `ps -eo pid,min_flt --sort=-min_flt` |
| `minflt` | Fallos de página menores (cabecera `MINFLT`). | `ps -eo pid,minflt --sort=-minflt` |
| `pagein` | Número de páginas traídas desde disco. | `ps -eo pid,pagein --sort=-pagein` |
| `oom` | OOM score (0–1000): probabilidad de ser elegido por el OOM killer. | `ps -eo pid,oom,comm --sort=-oom` |
| `oomadj` | Factor de ajuste sumado al OOM score. | `ps -eo pid,oomadj --sort=oomadj` |
| `lim` | Límite de memoria (`RLIMIT`) del proceso. | `ps -eo pid,lim --sort=-lim` |

## Claves de tiempo

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `time` | Tiempo de CPU acumulado, formato `[DD-]HH:MM:SS`. Alias `cputime`. | `ps -eo pid,time,comm --sort=-time` |
| `cputime` | Alias de `time`. | `ps -eo pid,cputime --sort=-cputime` |
| `times` | Tiempo de CPU acumulado en segundos. Alias `cputimes`. Ordena numéricamente sin ambigüedad. | `ps -eo pid,times --sort=-times` |
| `cputimes` | Alias de `times`. | `ps -eo pid,cputimes --sort=-cputimes` |
| `atime` | Tiempo de CPU acumulado (cabecera `TIME`). | `ps -eo pid,atime --sort=-atime` |
| `bsdtime` | Tiempo de CPU acumulado usuario+sistema, formato `MMM:SS`. | `ps -eo pid,bsdtime --sort=-bsdtime` |
| `etime` | Tiempo transcurrido desde el arranque del proceso, `[[DD-]hh:]mm:ss`. | `ps -eo pid,etime,comm --sort=-etime` |
| `etimes` | Tiempo transcurrido en segundos. Preferible a `etime` para ordenar. | `ps -eo pid,etimes --sort=-etimes` |
| `start_time` | Hora o fecha de arranque, resolución adaptativa. Alias `stime`. | `ps -eo pid,start_time --sort=start_time` |
| `stime` | Alias de `start_time`. | `ps -ef --sort=stime` |
| `start` | Hora de arranque `HH:MM:SS` si es de hoy, `Mmm dd` si no. | `ps -eo pid,start --sort=start` |
| `lstart` | Hora de arranque completa `DDD mmm HH:MM:SS YYY`. | `ps -eo pid,lstart,comm --sort=lstart` |
| `bsdstart` | Hora de arranque estilo BSD: ` HH:MM` si <24 h, si no ` Mmm:SS`. | `ps -eo pid,bsdstart --sort=bsdstart` |

## Claves de identificadores de proceso e hilos

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `pid` | ID del proceso. Alias `tgid`. | `ps -e --sort=-pid` |
| `tgid` | Thread group ID (PID del líder del grupo de hilos). Alias `pid`. | `ps -eLo tgid,tid --sort=tgid` |
| `ppid` | PID del padre. Útil para agrupar hijos del mismo padre. | `ps -ef --sort=ppid,pid` |
| `pgid` | Process group ID. Alias `pgrp`. | `ps -eo pid,pgid --sort=pgid` |
| `pgrp` | Alias de `pgid`. | `ps -eo pid,pgrp --sort=pgrp` |
| `sess` | Session ID (PID del líder de sesión). Alias `session`, `sid`. | `ps -eo pid,sess --sort=sess` |
| `session` | Alias de `sess`. | `ps -eo pid,session --sort=session` |
| `sid` | Alias de `sess`. | `ps -eo pid,sid --sort=sid` |
| `tpgid` | PGID del grupo en primer plano de la tty; `-1` si no hay tty. | `ps -eo pid,tpgid --sort=tpgid` |
| `lwp` | ID del hilo (light weight process). Alias `spid`, `tid`. | `ps -eLo pid,lwp --sort=lwp` |
| `spid` | Alias de `lwp`. | `ps -eLo pid,spid --sort=spid` |
| `tid` | Alias de `lwp`. | `ps -eLo pid,tid --sort=tid` |
| `nlwp` | Número de hilos del proceso. Alias `thcount`. | `ps -eo pid,nlwp,comm --sort=-nlwp` |
| `thcount` | Alias de `nlwp`. | `ps -eo pid,thcount --sort=-thcount` |

## Claves de usuario y grupo

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `uid` | UID efectivo, numérico. Alias `euid`. | `ps -e --sort=uid` |
| `euid` | Alias de `uid`. | `ps -eo euid,pid --sort=euid` |
| `uid_hack` | Variante interna del UID efectivo (cabecera `UID`). | `ps -eo uid_hack,pid --sort=uid_hack` |
| `ruid` | UID real. | `ps -eo ruid,euid,pid --sort=ruid` |
| `suid` | UID guardado (saved). Alias `svuid`. | `ps -eo suid,pid --sort=suid` |
| `svuid` | Alias de `suid`. | `ps -eo svuid,pid --sort=svuid` |
| `fuid` | UID de acceso a filesystem. Alias `fsuid`. | `ps -eo fuid,pid --sort=fuid` |
| `fsuid` | Alias de `fuid`. | `ps -eo fsuid,pid --sort=fsuid` |
| `user` | Nombre de usuario efectivo. Alias `euser`, `uname`. Ordena por cadena. | `ps aux --sort=user,-%cpu` |
| `euser` | Alias de `user`. | `ps -eo euser,pid --sort=euser` |
| `uname` | Alias de `user`. | `ps -eo uname,pid --sort=uname` |
| `ruser` | Nombre de usuario real. | `ps -eo ruser,euser,pid --sort=ruser` |
| `suser` | Nombre de usuario guardado. Alias `svuser`. | `ps -eo suser,pid --sort=suser` |
| `svuser` | Alias de `suser`. | `ps -eo svuser,pid --sort=svuser` |
| `fuser` | Nombre de usuario de acceso a filesystem. Alias `fsuser`. | `ps -eo fuser,pid --sort=fuser` |
| `fsuser` | Alias de `fuser`. | `ps -eo fsuser,pid --sort=fsuser` |
| `ouid` | UID del propietario de la sesión del proceso (requiere soporte systemd). | `ps -eo ouid,pid --sort=ouid` |
| `luid` | Login ID asociado al proceso. | `ps -eo luid,pid --sort=luid` |
| `gid` | GID efectivo. Alias `egid`. | `ps -eo gid,pid --sort=gid` |
| `egid` | Alias de `gid`. | `ps -eo egid,pid --sort=egid` |
| `rgid` | GID real. | `ps -eo rgid,pid --sort=rgid` |
| `sgid` | GID guardado. Alias `svgid`. | `ps -eo sgid,pid --sort=sgid` |
| `svgid` | Alias de `sgid`. | `ps -eo svgid,pid --sort=svgid` |
| `fgid` | GID de acceso a filesystem. Alias `fsgid`. | `ps -eo fgid,pid --sort=fgid` |
| `fsgid` | Alias de `fgid`. | `ps -eo fsgid,pid --sort=fsgid` |
| `group` | Nombre de grupo efectivo. Alias `egroup`. | `ps -eo group,pid --sort=group` |
| `egroup` | Alias de `group`. | `ps -eo egroup,pid --sort=egroup` |
| `rgroup` | Nombre de grupo real. | `ps -eo rgroup,pid --sort=rgroup` |
| `sgroup` | Nombre de grupo guardado. Alias `svgroup`. | `ps -eo sgroup,pid --sort=sgroup` |
| `svgroup` | Alias de `sgroup`. | `ps -eo svgroup,pid --sort=svgroup` |
| `fgroup` | Nombre de grupo de acceso a filesystem. Alias `fsgroup`. | `ps -eo fgroup,pid --sort=fgroup` |
| `fsgroup` | Alias de `fgroup`. | `ps -eo fsgroup,pid --sort=fsgroup` |
| `supgid` | GIDs de los grupos suplementarios. | `ps -eo supgid,pid --sort=supgid` |
| `supgrp` | Nombres de los grupos suplementarios. | `ps -eo supgrp,pid --sort=supgrp` |

## Claves de estado, terminal y comando

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `s` | Estado mínimo, un carácter ([[ps#Process states]]). Alias `state`. | `ps -eo s,pid,comm --sort=s` |
| `state` | Alias de `s`. | `ps -eo state,pid --sort=state` |
| `stat` | Estado multicarácter (incluye `<`, `N`, `L`, `s`, `l`, `+`). | `ps aux --sort=stat` |
| `f` | Flags del proceso. Alias `flag`, `flags`. | `ps -eo f,pid --sort=f` |
| `flag` | Alias de `f`. | `ps -eo flag,pid --sort=flag` |
| `flags` | Alias de `f`. | `ps -eo flags,pid --sort=flags` |
| `tty` | Terminal de control. Ordena por número de dispositivo, no por nombre. Alias `tt`, `tname`. | `ps -e --sort=tty,pid` |
| `tt` | Alias de `tty`. | `ps -eo tt,pid --sort=tt` |
| `tname` | Alias de `tty` (cabecera `TTY`). | `ps -eo tname,pid --sort=tname` |
| `longtname` | Nombre largo de la terminal de control. | `ps -eo longtname,pid --sort=longtname` |
| `tty4` | Nombre de tty truncado a 4 caracteres. | `ps -eo tty4,pid --sort=tty4` |
| `tty8` | Nombre de tty truncado a 8 caracteres. | `ps -eo tty8,pid --sort=tty8` |
| `comm` | Nombre del ejecutable solo. Alias `ucmd`, `ucomm`. Orden alfabético. | `ps -eo pid,comm --sort=comm` |
| `ucmd` | Alias de `comm`. | `ps -eo pid,ucmd --sort=ucmd` |
| `ucomm` | Alias de `comm`. | `ps -eo pid,ucomm --sort=ucomm` |
| `args` | Comando con todos sus argumentos. Alias `cmd`, `command`. | `ps -eo pid,args --sort=args` |
| `cmd` | Alias de `args`. | `ps -ef --sort=cmd` |
| `command` | Alias de `args`. | `ps -eo pid,command --sort=command` |
| `fname` | Primeros 8 bytes del nombre base del ejecutable. | `ps -eo pid,fname --sort=fname` |
| `exe` | Ruta absoluta del ejecutable. | `ps -eo pid,exe --sort=exe` |
| `environ` | Variables de entorno del proceso (cabecera `ENVIRONM`). | `ps -eo pid,environ --sort=environ` |
| `wchan` | Nombre de la función del kernel donde duerme el proceso. Alias `wname`. | `ps -eo pid,wchan,comm --sort=wchan` |
| `wname` | Alias de `wchan`. | `ps -eo pid,wname --sort=wname` |
| `nwchan` | Dirección de la función del kernel donde duerme el proceso. | `ps -eo pid,nwchan --sort=nwchan` |
| `stackp` | Dirección del inicio (fondo) de la pila. Alias `start_stack`. | `ps -eo pid,stackp --sort=stackp` |
| `start_stack` | Alias de `stackp`. | `ps -eo pid,start_stack --sort=start_stack` |
| `eip` | Instruction pointer. A partir de kernel 4.9 se muestra a cero salvo en salida/core dump. | `ps -eo pid,eip --sort=eip` |
| `esp` | Stack pointer. Mismo comportamiento que `eip` desde kernel 4.9. | `ps -eo pid,esp --sort=esp` |
| `fds` | Número total de descriptores de fichero abiertos. | `ps -eo pid,fds,comm --sort=-fds` |

## Claves de señales

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `pending` | Máscara de señales pendientes del proceso. Alias `sig`, `sig_pend`. | `ps -eo pid,pending --sort=pending` |
| `sig` | Alias de `pending`. | `ps -eo pid,sig --sort=sig` |
| `sig_pend` | Alias de `pending` (cabecera `SIGNAL`). | `ps -eo pid,sig_pend --sort=sig_pend` |
| `tsig` | Señales pendientes a nivel de hilo (cabecera `PENDING`). | `ps -eo pid,tsig --sort=tsig` |
| `blocked` | Máscara de señales bloqueadas. Alias `sig_block`, `sigmask`. | `ps -eo pid,blocked --sort=blocked` |
| `sig_block` | Alias de `blocked`. | `ps -eo pid,sig_block --sort=sig_block` |
| `sigmask` | Alias de `blocked`. | `ps -eo pid,sigmask --sort=sigmask` |
| `caught` | Máscara de señales capturadas. Alias `sig_catch`, `sigcatch`. | `ps -eo pid,caught --sort=caught` |
| `sigcatch` | Alias de `caught`. | `ps -eo pid,sigcatch --sort=sigcatch` |
| `sig_catch` | Alias de `caught` (cabecera `CATCHED`). | `ps -eo pid,sig_catch --sort=sig_catch` |
| `ignored` | Máscara de señales ignoradas. Alias `sig_ignore`, `sigignore`. | `ps -eo pid,ignored --sort=ignored` |
| `sigignore` | Alias de `ignored`. | `ps -eo pid,sigignore --sort=sigignore` |
| `sig_ignore` | Alias de `ignored`. | `ps -eo pid,sig_ignore --sort=sig_ignore` |

## Claves de E/S

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `rchars` | Bytes que la tarea ha provocado leer del almacenamiento (incluye caché). | `ps -eo pid,rchars,comm --sort=-rchars` |
| `wchars` | Bytes que la tarea ha provocado o provocará escribir a disco. | `ps -eo pid,wchars,comm --sort=-wchars` |
| `rbytes` | Bytes realmente traídos de la capa de almacenamiento. | `ps -eo pid,rbytes,comm --sort=-rbytes` |
| `wbytes` | Bytes realmente enviados a la capa de almacenamiento. | `ps -eo pid,wbytes,comm --sort=-wbytes` |
| `wcbytes` | Bytes de escritura cancelados. | `ps -eo pid,wcbytes --sort=-wcbytes` |
| `rops` | Número de operaciones de lectura (`read(2)`, `pread(2)`). | `ps -eo pid,rops,comm --sort=-rops` |
| `wops` | Número de operaciones de escritura (`write(2)`, `pwrite(2)`). | `ps -eo pid,wops,comm --sort=-wops` |

## Claves de cgroups, namespaces y contenedores

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `cgroup` | Control groups a los que pertenece el proceso. | `ps -eo pid,cgroup --sort=cgroup` |
| `cgname` | Nombre de los control groups del proceso. | `ps -eo pid,cgname --sort=cgname` |
| `slice` | Slice unit de systemd a la que pertenece. | `ps -eo pid,slice --sort=slice` |
| `unit` | Unit de systemd a la que pertenece. | `ps -eo pid,unit --sort=unit` |
| `uunit` | User unit de systemd a la que pertenece. | `ps -eo pid,uunit --sort=uunit` |
| `lsession` | Identificador de sesión de login (requiere soporte systemd). | `ps -eo pid,lsession --sort=lsession` |
| `seat` | Identificador del seat de hardware asignado (requiere soporte systemd). | `ps -eo pid,seat --sort=seat` |
| `machine` | Nombre de la máquina para procesos asignados a VM o contenedor. | `ps -eo pid,machine --sort=machine` |
| `docker` | ID abreviado del contenedor Docker; `-` si no está en uno. | `ps -eo pid,docker,comm --sort=docker` |
| `lxc` | Nombre del contenedor LXC; `-` si no está en uno. | `ps -eo pid,lxc,comm --sort=lxc` |
| `zone` | Zona (Solaris) a la que pertenece el proceso. | `ps -eo pid,zone --sort=zone` |
| `cgroupns` | Número de inodo del cgroup namespace. | `ps -eo pid,cgroupns --sort=cgroupns` |
| `ipcns` | Número de inodo del IPC namespace. | `ps -eo pid,ipcns --sort=ipcns` |
| `mntns` | Número de inodo del mount namespace. Agrupa procesos por raíz de FS. | `ps -eo pid,mntns,comm --sort=mntns` |
| `netns` | Número de inodo del network namespace. Detecta procesos fuera del netns del host. | `ps -eo pid,netns,comm --sort=netns` |
| `pidns` | Número de inodo del PID namespace. | `ps -eo pid,pidns --sort=pidns` |
| `userns` | Número de inodo del user namespace. | `ps -eo pid,userns --sort=userns` |
| `utsns` | Número de inodo del UTS namespace. | `ps -eo pid,utsns --sort=utsns` |
| `timens` | Número de inodo del time namespace. | `ps -eo pid,timens --sort=timens` |

## Claves de seguridad

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `label` | Etiqueta de seguridad MAC (típicamente contexto SELinux). | `ps -eo pid,label --sort=label` |
| `context` | Contexto de seguridad del proceso. | `ps -eo pid,context --sort=context` |
| `pcap` | Capabilities permitidas como bitmask hexadecimal. | `ps -eo pid,pcap,comm --sort=-pcap` |
| `pcaps` | Capabilities permitidas como cadena de nombres. | `ps -eo pid,pcaps,comm --sort=pcaps` |

## Claves de relleno (sin efecto de orden)

| **Clave** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `_left` | Columna de relleno alineada a la izquierda. Aceptada por `--sort`, sin efecto útil. | `ps -eo pid,_left` |
| `_left2` | Segunda columna de relleno a la izquierda. | `ps -eo pid,_left2` |
| `_right` | Columna de relleno alineada a la derecha. | `ps -eo pid,_right` |
| `_right2` | Segunda columna de relleno a la derecha. | `ps -eo pid,_right2` |
| `_unlimited` | Columna de relleno de ancho ilimitado. | `ps -eo pid,_unlimited` |
| `_unlimited2` | Segunda columna de relleno de ancho ilimitado. | `ps -eo pid,_unlimited2` |

## Claves cortas BSD obsoletas

Solo válidas con la opción BSD `O`. La opción GNU `--sort` y su alias `k` **no** las aceptan: usan los especificadores largos de las tablas anteriores. Ordenan por el valor interno de `ps`, no por el valor mostrado.

| **Clave** | **Equivalente largo** | **Función** | **Ejemplo** |
| --- | --- | --- | --- |
| `c` | `cmd` | Nombre simple del ejecutable. | `ps axOc` |
| `C` | `pcpu` | Utilización de CPU. | `ps aux O-C` |
| `f` | `flags` | Flags del campo `F` del formato largo. | `ps alOf` |
| `g` | `pgrp` | Process group ID. | `ps axOg` |
| `G` | `tpgid` | PGID de la tty de control. | `ps axOG` |
| `j` | `cutime` | Tiempo de usuario acumulado (incluye hijos). | `ps axOj` |
| `J` | `cstime` | Tiempo de sistema acumulado (incluye hijos). | `ps axOJ` |
| `k` | `utime` | Tiempo de usuario. | `ps axO-k` |
| `m` | `min_flt` | Fallos de página menores. | `ps axO-m` |
| `M` | `maj_flt` | Fallos de página mayores. | `ps axO-M` |
| `n` | `cmin_flt` | Fallos de página menores acumulados. | `ps axO-n` |
| `N` | `cmaj_flt` | Fallos de página mayores acumulados. | `ps axO-N` |
| `o` | `session` | Session ID. | `ps axOo` |
| `p` | `pid` | Process ID. | `ps axOp` |
| `P` | `ppid` | PID del padre. | `ps axOP` |
| `r` | `rss` | Resident set size. | `ps aux O-r` |
| `R` | `resident` | Páginas residentes. | `ps aux O-R` |
| `s` | `size` | Tamaño de memoria en KiB. | `ps aux O-s` |
| `S` | `share` | Cantidad de páginas compartidas. | `ps aux O-S` |
| `t` | `tty` | Número de dispositivo de la tty de control. | `ps axOt` |
| `T` | `start_time` | Instante de arranque del proceso. | `ps axOT` |
| `U` | `uid` | UID numérico. | `ps axOU` |
| `u` | `user` | Nombre de usuario. | `ps axOu` |
| `v` | `vsize` | Tamaño total de VM en KiB. | `ps aux O-v` |
| `y` | `priority` | Prioridad de planificación del kernel. | `ps alOy` |

## Recetas de ordenación

```bash
# Top 10 consumidores de CPU
ps -eo pid,user,pcpu,comm --sort=-pcpu | head -11

# Top 10 consumidores de memoria residente
ps -eo pid,user,rss,comm --sort=-rss | head -11

# Procesos agrupados por usuario y, dentro de cada uno, por CPU descendente
ps -eo user,pid,pcpu,comm --sort=user,-pcpu

# Los procesos más antiguos del sistema (etimes ordena numéricamente)
ps -eo pid,etimes,lstart,comm --sort=-etimes | head

# Procesos con más hilos
ps -eo pid,nlwp,comm --sort=-nlwp | head

# Procesos con mayor riesgo ante el OOM killer
ps -eo pid,oom,oomadj,rss,comm --sort=-oom | head

# Mayor consumo de E/S en bloque real
ps -eo pid,rbytes,wbytes,comm --sort=-wbytes | head

# Procesos agrupados por namespace de red (detección de contenedores)
ps -eo pid,netns,mntns,comm --sort=netns,pid

# Ordenar por el nombre de tty mostrado (valor "cocinado"), no por su número de dispositivo
ps -eo tty,pid,comm | tail -n +2 | sort -k1,1
```

# Modificadores BSD agregados

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `aux` | Formato BSD completo (todos los usuarios, con/sin tty). | `ps aux` |
| `axw` | Máxima información con argumentos completos. | `ps axw` |
| `-U root -u root u` | Todos los procesos de root en formato de usuario. | `ps -U root -u root u` |

# Threads (hilos)

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-L` | Muestra los threads (LWP/NLWP) de cada proceso. | `ps -p 1234 -L` |
| `-T` | Muestra los threads con la columna SPID. | `ps -T -p 1234` |
| `H` (BSD) | Muestra los threads como si fueran procesos. | `ps -eLf H` |
| `m` (BSD) | Muestra la información de los threads tras cada proceso. | `ps m` |
| `-m` | Muestra los threads después de su proceso. | `ps -m -p 1234` |

# Vista en árbol (jerarquía)

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-f --forest` | Árbol jerárquico de procesos con conectores ASCII. | `ps -ef --forest` |
| `f` (BSD) | Árbol de procesos estilo BSD. | `ps axf` |
| `-H` | Muestra la jerarquía mediante indentación. | `ps -eH` |
| `-ejH` | Árbol de procesos con formato de jobs. | `ps -ejH` |

# Información y ayuda

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `L` | Lista todos los códigos de formato disponibles para `-o`. | `ps L` |
| `--help` sección | Ayuda por sección (`simple`, `list`, `output`, `threads`, `misc`, `all`). | `ps --help output` |
| `--info` | Imprime información de depuración. | `ps --info` |
| `-V`, `--version` | Muestra la versión de procps. | `ps --version` |

# Process states

| **State** | **Description**                                                     |
| --------- | ------------------------------------------------------------------- |
| D         | uninterruptible sleep (usually IO)                                  |
| I         | Idle kernel thread                                                  |
| R         | running or runnable (on run queue)                                  |
| S         | interruptible sleep (waiting for an event to complete)              |
| T         | stopped by job control signal                                       |
| t         | stopped by debugger during the tracing                              |
| W         | paging (not valid since the 2.6.xx kernel)                          |
| X         | dead (should never be seen)                                         |
| Z         | defunct ("zombie") process, terminated but not reaped by its parent |

| **Character** | **Description** |
| --- | --- |
| `<` | high-priority (not nice to other users) |
| N | low-priority (nice to other users) |
| L | has pages locked into memory (for real-time and custom IO) |
| s | is a session leader |
| l | is multi-threaded (using CLONE_THREAD, like NPTL pthreads do) |
| + | is in the foreground process group |

## Protips
### Find zombie processes
```
ps aux | awk '$8 ~ /^Z/'
```

## Notas

- `ps aux` (BSD) y `ps -ef` (UNIX) son equivalentes en cobertura pero difieren en columnas; `aux` muestra `%CPU`/`%MEM`, `-ef` muestra `PPID`/`STIME`.
- Para monitorización continua o por nombre, ver [[pgrep]], [[pidstat]] y [[top]].
