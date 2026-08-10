`date` imprime o establece la fecha y hora del sistema. Sin argumentos muestra el instante actual en el formato del locale; con un argumento `+FORMATO` lo imprime según los especificadores indicados. Su verdadera potencia está en `-d`, que acepta cadenas de fecha en lenguaje casi natural (`"next friday"`, `"2 days ago"`, `"@1768000000"`) y permite hacer aritmética de calendario sin salir del shell. Forma parte de GNU coreutils; los especificadores derivan de `strftime(3)` pero incluyen extensiones GNU.

```
date [OPCIÓN]... [+FORMATO]
date [OPCIÓN]... MMDDhhmm[[CC]YY][.ss]
```

> Las opciones que seleccionan la fecha a mostrar son **mutuamente excluyentes**: `--date`, `--file`, `--reference` y `--resolution` no pueden combinarse entre sí.

---

## Selección de la fecha de entrada

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-d` / `--date=STRING` | Muestra el instante descrito por `STRING` en lugar de "ahora" ([[date#Sintaxis de las cadenas de fecha]]). | `date -d 'next friday'` |
| `-f` / `--file=DATEFILE` | Como `--date`, pero una vez por cada línea del fichero. Con `-` lee de stdin. | `date -f fechas.txt +%F` |
| `-r` / `--reference=FILE` | Muestra la fecha de última modificación (mtime) de `FILE`. | `date -r /etc/passwd` |
| `--resolution` | Imprime la resolución disponible de los timestamps del sistema. | `date --resolution` |

> `-f -` es la forma eficiente de convertir miles de fechas: un solo proceso en lugar de un `date -d` por línea.

---

## Formatos de salida predefinidos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-I` / `--iso-8601[=FMT]` | Salida ISO 8601. `FMT` puede ser `date` (por defecto), `hours`, `minutes`, `seconds` o `ns`. | `date -Iseconds` |
| `-R` / `--rfc-email` | Formato RFC 5322, el de las cabeceras de correo. | `date -R` |
| `--rfc-3339=FMT` | Formato RFC 3339 con espacio como separador. `FMT` es `date`, `seconds` o `ns`. | `date --rfc-3339=ns` |

> `-I` usa `T` como separador fecha/hora; `--rfc-3339` usa un espacio. Para logs y comparaciones lexicográficas, `date -Is` (o `+%F %T`) es la opción segura.

---

## Zona horaria

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-u` / `--utc` / `--universal` | Interpreta e imprime en UTC, ignorando la zona local. | `date -u +%FT%TZ` |
| `TZ=...` (variable) | Fija la zona para esa invocación. No es una opción de `date`, sino del entorno. Ver `tzselect(1)`. | `TZ='America/Los_Angeles' date` |
| `TZ` dentro de `-d` | La cadena de fecha puede llevar su propia `TZ` entrecomillada, que aplica solo a esa fecha. | `date -d 'TZ="Asia/Tokyo" 09:00 next Fri'` |

> Para aritmética de calendario que cruce cambios de horario (DST), fijar `TZ=UTC0` evita saltos de ±1 h o incluso ±24 h.

---

## Ajuste del reloj del sistema

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-s` / `--set=STRING` | Establece la hora del sistema al instante descrito por `STRING`. Requiere root. | `date -s '2026-08-10 12:00:00'` |
| `MMDDhhmm[[CC]YY][.ss]` | Forma posicional POSIX para fijar la hora, sin `-s`. Requiere root. | `date 081012002026.30` |

> Ajusta el reloj **del kernel**, no el RTC de hardware: hay que sincronizar después con `hwclock -w` ([[hwclock]]). En sistemas con `systemd-timesyncd`, `chrony` o `ntpd` activos, el cambio será revertido; deshabilitar antes con `timedatectl set-ntp false`.

---

## Diagnóstico y ayuda

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--debug` | Anota cómo se ha interpretado la fecha y avisa por stderr de usos dudosos. | `date --debug -d '3/4/2026'` |
| `--help` | Muestra la ayuda, incluida la tabla completa de especificadores. | `date --help` |
| `--version` | Muestra la versión de coreutils. | `date --version` |

> `--debug` es la herramienta para desambiguar formatos: revela si `3/4/2026` se leyó como 4 de marzo o 3 de abril y si se aplicó la zona esperada.

---

## Especificadores de formato: fecha

| Especificador | Descripción | Ejemplo |
|---------------|-------------|---------|
| `%Y` | Año con todos los dígitos. | `date +%Y` |
| `%y` | Últimos dos dígitos del año (ambiguo, `00`–`99`). | `date +%y` |
| `%C` | Siglo: como `%Y` sin los dos últimos dígitos. | `date +%C` |
| `%m` | Mes numérico (`01`–`12`). | `date +%m` |
| `%b` | Nombre abreviado del mes según el locale. | `date +%b` |
| `%h` | Igual que `%b`. | `date +%h` |
| `%B` | Nombre completo del mes según el locale. | `date +%B` |
| `%d` | Día del mes con cero a la izquierda (`01`–`31`). | `date +%d` |
| `%e` | Día del mes con espacio a la izquierda; igual que `%_d`. | `date +%e` |
| `%j` | Día del año (`001`–`366`). | `date +%j` |
| `%q` | Trimestre del año (`1`–`4`). | `date +%q` |
| `%a` | Nombre abreviado del día de la semana. | `date +%a` |
| `%A` | Nombre completo del día de la semana. | `date +%A` |
| `%u` | Día de la semana `1`–`7`, lunes = 1. | `date +%u` |
| `%w` | Día de la semana `0`–`6`, domingo = 0. | `date +%w` |
| `%F` | Fecha completa; equivale a `%+4Y-%m-%d`. | `date +%F` |
| `%D` | Fecha ambigua `%m/%d/%y`. Evitar en scripts. | `date +%D` |
| `%x` | Fecha en el formato del locale (ambigua). | `date +%x` |

---

## Especificadores de formato: hora

| Especificador | Descripción | Ejemplo |
|---------------|-------------|---------|
| `%H` | Hora en formato 24 h (`00`–`23`). | `date +%H` |
| `%k` | Hora 24 h con espacio a la izquierda (` 0`–`23`); igual que `%_H`. | `date +%k` |
| `%I` | Hora en formato 12 h (`01`–`12`). | `date +%I` |
| `%l` | Hora 12 h con espacio a la izquierda; igual que `%_I`. | `date +%l` |
| `%M` | Minuto (`00`–`59`). | `date +%M` |
| `%S` | Segundo (`00`–`60`; 60 en sistemas con leap seconds). | `date +%S` |
| `%N` | Nanosegundos (`000000000`–`999999999`). | `date +%N` |
| `%p` | `AM`/`PM` del locale; vacío si no aplica. | `date +%p` |
| `%P` | Como `%p` pero en minúsculas. | `date +%P` |
| `%T` | Hora completa; equivale a `%H:%M:%S`. | `date +%T` |
| `%R` | Hora y minuto; equivale a `%H:%M`. | `date +%R` |
| `%r` | Hora de 12 h del locale. | `date +%r` |
| `%X` | Hora en la representación del locale. | `date +%X` |

---

## Especificadores de formato: semana ISO, epoch y compuestos

| Especificador | Descripción | Ejemplo |
|---------------|-------------|---------|
| `%V` | Número de semana ISO (`01`–`53`), lunes como primer día. | `date +%V` |
| `%G` | Año al que pertenece la semana ISO. Solo útil junto a `%V`. | `date +%G-W%V` |
| `%g` | Últimos dos dígitos de `%G` (ambiguo, `00`–`99`). | `date +%g` |
| `%U` | Semana del año con domingo como primer día (`00`–`53`). | `date +%U` |
| `%W` | Semana del año con lunes como primer día (`00`–`53`). | `date +%W` |
| `%s` | Segundos desde el Epoch (1970-01-01 00:00 UTC). | `date +%s` |
| `%c` | Fecha y hora completas del locale. | `date +%c` |

> `%V` debe emparejarse siempre con `%G`, nunca con `%Y`: el 31 de diciembre puede pertenecer a la semana 1 del año siguiente y `%Y-W%V` produciría una semana inexistente.

---

## Especificadores de formato: zona horaria

| Especificador | Descripción | Ejemplo |
|---------------|-------------|---------|
| `%z` | Desplazamiento numérico `+hhmm`. | `date +%z` |
| `%:z` | Desplazamiento numérico `+hh:mm`. | `date +%:z` |
| `%::z` | Desplazamiento numérico `+hh:mm:ss`. | `date +%::z` |
| `%:::z` | Desplazamiento con la precisión mínima necesaria (`-04`, `+05:30`). | `date +%:::z` |
| `%Z` | Abreviatura alfabética de la zona (`CEST`, `UTC`). | `date +%Z` |

---

## Especificadores de formato: literales

| Especificador | Descripción | Ejemplo |
|---------------|-------------|---------|
| `%%` | Un signo de porcentaje literal. | `date +'%d%% del mes'` |
| `%n` | Un salto de línea. | `date +'%F%n%T'` |
| `%t` | Un tabulador. | `date +'%F%t%T'` |

---

## Modificadores de relleno, ancho y locale

Se colocan entre el `%` y la letra del especificador, en el orden: *flags* → ancho → modificador.

| Modificador | Descripción | Ejemplo |
|-------------|-------------|---------|
| `-` | No rellenar el campo. | `date +%-d` |
| `_` | Rellenar con espacios. | `date +%_d` |
| `0` | Rellenar con ceros. | `date +%0e` |
| `+` | Rellenar con ceros y anteponer `+` a los años de más de 4 dígitos. | `date +%+4Y` |
| `^` | Convertir a mayúsculas si es posible. | `date +%^A` |
| `#` | Invertir la capitalización si es posible. | `date +%#A` |
| `ANCHO` | Número decimal con el ancho mínimo del campo. | `date +%6N` |
| `E` | Usar las representaciones alternativas del locale. | `date +%Ex` |
| `O` | Usar los símbolos numéricos alternativos del locale. | `date +%Od` |

> `%-d`, `%-m`, `%-H` son imprescindibles cuando la salida alimenta aritmética de shell: `08` se interpreta como octal inválido en `$(( ))`.

---

## Sintaxis de las cadenas de fecha

Acepta múltiples elementos separados por espacios, **en cualquier orden**. La cadena vacía significa el comienzo de hoy (medianoche).

### Fechas de calendario

| Forma | Descripción | Ejemplo |
|-------|-------------|---------|
| `YYYY-MM-DD` | ISO 8601. La única no ambigua. | `date -d 2026-11-14` |
| `MM/DD/YYYY` | Escritura estadounidense. | `date -d 11/14/2026` |
| `DD.MM.YYYY` | Escritura europea. | `date -d 14.11.2026` |
| `DD MONTH YYYY` | Mes literal, completo o abreviado a 3 letras. | `date -d '14 Nov 2026'` |
| `MONTH DD, YYYY` | Mes literal delante. | `date -d 'November 14, 2026'` |
| `DD-MONTH-YYYY` | Guiones con mes literal. | `date -d 14-nov-2026` |
| `MM/DD`, `DD.MM.`, `MONTH DD` | Año omitido: se usa el último indicado o el actual. | `date -d 'nov 14'` |
| `YY-MM-DD` | Año de dos dígitos: `00`–`68` → 20xx, `69`–`99` → 19xx. No recomendado. | `date -d 26-11-14` |

### Hora del día

| Forma | Descripción | Ejemplo |
|-------|-------------|---------|
| `HH:MM:SS` | Hora, minuto y segundo. El segundo admite fracción con `.` o `,`. | `date -d '20:02:00.500'` |
| `HH:MM` | Segundos implícitos a cero. | `date -d 20:02` |
| `H:MMam` / `H:MMpm` | Notación de 12 h; `a.m.`/`p.m.` también válidos. `12am` = medianoche, `12pm` = mediodía. | `date -d '8:02pm'` |
| `HH:MM±HHMM` | Con corrección de zona explícita; fuerza la interpretación respecto a UTC. | `date -d '20:02-0500'` |

### Zona horaria

| Forma | Descripción | Ejemplo |
|-------|-------------|---------|
| `UTC`, `Z` | Tiempo universal coordinado. Las únicas abreviaturas recomendadas. | `date -d '2026-11-14 12:00 UTC'` |
| `±HHMM`, `±HH:MM`, `±HH` | Corrección numérica respecto a UTC. Máximo ±24 h. | `date -d '12:00 +05:30'` |
| `ZONA DST` | Zona sin horario de verano seguida de `DST` en palabra aparte. | `date -d '12:00 EST DST'` |
| `TZ="Region/Ciudad"` | Regla de zona completa dentro de la propia cadena. | `date -d 'TZ="Europe/Madrid" 09:00'` |

> Las abreviaturas distintas de `UTC` y `Z` son obsolescentes y ambiguas (`EST` no significa lo mismo en EE. UU. que en Australia). Usar correcciones numéricas o `TZ="..."`.

### Día de la semana

| Forma | Descripción | Ejemplo |
|-------|-------------|---------|
| `monday` … `sunday` | Avanza la fecha (solo si hace falta) hasta ese día. Abreviable a 3 letras; también `Tues`, `Wednes`, `Thur`, `Thurs`. | `date -d friday` |
| `next DAY` | Una semana después del día que `DAY` representaría por sí solo. | `date -d 'next monday'` |
| `last DAY` | Una semana antes de ese mismo día. | `date -d 'last friday'` |
| `N DAY` | Adelanta `N-1` semanas adicionales; `third monday`, `2 tuesday`. | `date -d 'third monday'` |

### Elementos relativos

| Forma | Descripción | Ejemplo |
|-------|-------------|---------|
| `N unidad` | Desplazamiento hacia el futuro. Unidades: `year`, `month`, `fortnight` (14 d), `week`, `day`, `hour`, `minute`/`min`, `second`/`sec`. La `s` final se acepta y se ignora. | `date -d '3 weeks'` |
| `N unidad ago` | Desplazamiento hacia el pasado; equivale a multiplicador `-1`. | `date -d '2 days ago'` |
| `-N unidad` | Multiplicador con signo explícito. | `date -d '-1 month'` |
| `tomorrow` | Un día en el futuro; equivale a `day`. | `date -d tomorrow` |
| `yesterday` | Un día en el pasado; equivale a `day ago`. | `date -d yesterday` |
| `now`, `today` | Desplazamiento cero. Sirven para enfatizar otros elementos. | `date -d '12:00 today'` |
| `this` | Desplazamiento cero, preferido delante de un día de la semana. | `date -d 'this thursday'` |

> Los efectos de los elementos relativos se **acumulan**: `-d '1 month 3 days 2 hours ago'` es válido.

> `year` y `month` son unidades difusas. `2026-12-31 -1 month` puede dar 2026-12-01, porque 2026-11-31 no existe. Para el mes anterior de forma fiable: `date -d "$(date +%Y-%m-15) -1 month" +%B`.

### Números puros y epoch

| Forma | Descripción | Ejemplo |
|-------|-------------|---------|
| `YYYYMMDD` | Interpretado como fecha si no hay otro elemento de calendario antes. | `date -d 20261114` |
| `HHMM` | Interpretado como hora si no hay otro elemento horario antes. | `date -d '20261114 2102'` |
| `@N` | Timestamp Unix completo, en segundos desde el Epoch. Admite fracción decimal. No combinable con ningún otro elemento. | `date -d @1768000000` |
| `@-N` | Instantes anteriores al Epoch. | `date -d @-1` |

> Si a la izquierda de un número aparecen ya una fecha y una hora, pero ningún elemento relativo, el número **sobrescribe el año**.

---

## Recetas

```bash
# Timestamp para nombres de fichero y logs (ordenable lexicográficamente)
date +%Y%m%d_%H%M%S
date -Iseconds

# UTC estricto en formato Zulu
date -u +%Y-%m-%dT%H:%M:%SZ

# Epoch actual y conversión inversa
date +%s
date -d @1768000000 '+%F %T %Z'

# Milisegundos desde el Epoch
date +%s%3N

# Diferencia en segundos y en días entre dos fechas
echo $(( $(date -d 2026-12-31 +%s) - $(date -d 2026-08-10 +%s) ))
echo $(( ( $(date -d 2026-12-31 +%s) - $(date +%s) ) / 86400 ))

# Último día del mes actual
date -d "$(date +%Y-%m-01) +1 month -1 day" +%F

# Primer día del mes anterior
date -d "$(date +%Y-%m-01) -1 month" +%F

# Semana ISO correcta (siempre %G con %V)
date +%G-W%V-%u

# mtime de un fichero en formato ISO
date -r /etc/passwd -Iseconds

# Convertir un lote de fechas de un solo proceso
printf '%s\n' 2026-01-01 2026-06-15 | date -f - +'%A %d de %B'

# Hora en otra zona sin cambiar la del sistema
TZ='Asia/Tokyo' date -R

# Desambiguar una fecha dudosa
date --debug -d '3/4/2026'

# Fijar la hora del sistema y persistirla en el RTC
timedatectl set-ntp false
date -s '2026-08-10 12:00:00'
hwclock -w
```

---

## Precisión y trampas

- `date +%s` y `date +%N` se leen en llamadas distintas si van en comandos separados; para un instante coherente usar una sola invocación: `date +%s.%N`.
- `%N` no está disponible en `date` de BSD/macOS ni en busybox: los scripts portables no deben depender de él.
- `%D`, `%x`, `%y` y `%g` producen salidas ambiguas o dependientes del locale. En scripts, fijar `LC_ALL=C` o usar `%F`, `%T`, `%Y`.
- `date` sale con estado `0` si la fecha se interpreta y se imprime, y distinto de `0` si la cadena es inválida. Esto lo hace útil como validador: `date -d "$s" >/dev/null 2>&1 || echo inválida`.
- La forma posicional `MMDDhhmm...` se confunde fácilmente con un `+FORMATO` mal escrito; usar siempre `-s` para fijar la hora.

---

Relacionado: [[hwclock]], [[tzselect]], [[cronjobs]], [[at]], [[stat]], [[touch]].
