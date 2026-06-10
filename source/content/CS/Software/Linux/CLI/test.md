**test** evalúa una expresión condicional y devuelve un código de salida (`0` verdadero, `1` falso, `>1` error de sintaxis). Existe como *builtin* de la shell (`bash`, `sh`, `zsh`) y como binario independiente en `/usr/bin/test` (coreutils). Su forma sinónima `[ EXPR ]` exige un `]` de cierre como último argumento. Se usa para controlar flujo en scripts (`if`, `while`, `&&`, `||`).

```
test EXPR
[ EXPR ]
```

> Nota: el `[` es un comando real, no sintaxis. Cada operando y operador debe ir separado por espacios. Comillas las variables (`[ "$x" = y ]`) para evitar errores cuando están vacías o contienen espacios.

## Tests de tipo de archivo

| **Operador** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-e FILE` | El archivo existe (cualquier tipo). | `[ -e /etc/passwd ] && echo existe` |
| `-f FILE` | Existe y es archivo regular. | `[ -f config.ini ] && source config.ini` |
| `-d FILE` | Existe y es directorio. | `[ -d /var/log ] || mkdir /var/log` |
| `-b FILE` | Existe y es dispositivo de bloque. | `[ -b /dev/sda ] && echo disco` |
| `-c FILE` | Existe y es dispositivo de carácter. | `[ -c /dev/tty ] && echo terminal` |
| `-p FILE` | Existe y es FIFO (named pipe). | `[ -p /tmp/mypipe ] && echo pipe` |
| `-S FILE` | Existe y es socket. | `[ -S /var/run/docker.sock ] && echo socket` |
| `-L FILE` / `-h FILE` | Existe y es enlace simbólico. | `[ -L /bin ] && echo symlink` |
| `-s FILE` | Existe y tiene tamaño mayor que cero. | `[ -s salida.log ] || echo "log vacío"` |
| `-t FD` | El descriptor de archivo está abierto sobre una terminal. | `[ -t 1 ] && echo "stdout es TTY"` |

## Tests de permisos y propiedad

| **Operador** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-r FILE` | Permiso de lectura concedido al usuario efectivo. | `[ -r /etc/shadow ] || echo "sin lectura"` |
| `-w FILE` | Permiso de escritura concedido. | `[ -w "$dir" ] && touch "$dir/test"` |
| `-x FILE` | Permiso de ejecución (o acceso a directorio). | `[ -x ./script.sh ] && ./script.sh` |
| `-u FILE` | Tiene activado el bit SUID. | `[ -u /usr/bin/passwd ] && echo SUID` |
| `-g FILE` | Tiene activado el bit SGID. | `[ -g /usr/bin/wall ] && echo SGID` |
| `-k FILE` | Tiene activado el sticky bit. | `[ -k /tmp ] && echo sticky` |
| `-O FILE` | El usuario efectivo (EUID) es el propietario. | `[ -O ~/.ssh/id_rsa ] && echo "soy dueño"` |
| `-G FILE` | El grupo efectivo (EGID) es el propietario del archivo. | `[ -G compartido.txt ] && echo "mismo grupo"` |
| `-N FILE` | Modificado desde la última lectura (mtime > atime). | `[ -N buzon ] && echo "correo nuevo"` |

## Comparación entre archivos

| **Operador** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `F1 -nt F2` | F1 es más nuevo que F2 (newer than), o F1 existe y F2 no. | `[ src.c -nt bin ] && make` |
| `F1 -ot F2` | F1 es más viejo que F2 (older than), o F2 existe y F1 no. | `[ cache -ot fuente ] && regenerar` |
| `F1 -ef F2` | F1 y F2 referencian el mismo inodo (mismo dispositivo y nº de inodo). | `[ /bin/sh -ef /usr/bin/dash ] && echo "mismo archivo"` |

## Tests de cadenas

| **Operador** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `-z STR` | La cadena tiene longitud cero. | `[ -z "$1" ] && echo "falta argumento"` |
| `-n STR` | La cadena tiene longitud distinta de cero. | `[ -n "$PATH" ] && echo "PATH definido"` |
| `STR` | Verdadero si la cadena no está vacía (equivale a `-n`). | `[ "$flag" ] && echo activo` |
| `STR1 = STR2` | Las cadenas son iguales. | `[ "$user" = root ] && echo admin` |
| `STR1 != STR2` | Las cadenas son distintas. | `[ "$ans" != y ] && exit` |
| `STR1 < STR2` | STR1 ordena antes que STR2 (orden lexicográfico/ASCII). En `test`/`[` hay que escapar: `\<`. | `[ "$a" \< "$b" ] && echo "a primero"` |
| `STR1 > STR2` | STR1 ordena después que STR2. Escapar `\>`. | `[ "$a" \> "$b" ] && echo "a después"` |

## Comparación de enteros

| **Operador** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `N1 -eq N2` | Iguales (equal). | `[ "$#" -eq 0 ] && echo "sin args"` |
| `N1 -ne N2` | Distintos (not equal). | `[ "$ret" -ne 0 ] && echo fallo` |
| `N1 -lt N2` | Menor que (less than). | `[ "$edad" -lt 18 ] && echo menor` |
| `N1 -le N2` | Menor o igual (less or equal). | `[ "$n" -le 10 ] && continuar` |
| `N1 -gt N2` | Mayor que (greater than). | `[ "$intentos" -gt 3 ] && bloquear` |
| `N1 -ge N2` | Mayor o igual (greater or equal). | `[ "$disco" -ge 90 ] && alertar` |

## Operadores lógicos y agrupación

| **Operador** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `! EXPR` | Niega la expresión. | `[ ! -f lock ] && touch lock` |
| `EXPR1 -a EXPR2` | AND lógico (obsoleto; preferir `&&` entre dos `test`). | `[ -f x -a -r x ] && cat x` |
| `EXPR1 -o EXPR2` | OR lógico (obsoleto; preferir `\|\|`). | `[ -z "$a" -o -z "$b" ] && error` |
| `( EXPR )` | Agrupa para forzar precedencia (paréntesis escapados en `[`). | `[ \( -f a \) -o \( -f b \) ]` |

> POSIX desaconseja `-a`, `-o` y `( )` por ambigüedad sintáctica. Usar varias invocaciones combinadas con `&&` y `||`, o el constructo `[[ ... ]]` de `bash`/`zsh` (que sí soporta `&&`, `||`, `<`, `>` sin escapar y comparación con patrones).

## Opciones del binario independiente (`/usr/bin/test`)

| **Parámetro** | **Función** | **Ejemplo** |
| --- | --- | --- |
| `--help` | Muestra la ayuda. Solo en el binario coreutils, no en el builtin. | `/usr/bin/test --help` |
| `--version` | Muestra la versión de coreutils. | `/usr/bin/test --version` |

Relacionado: [[type]], [[bc]], [[sh]], [[zsh]].
