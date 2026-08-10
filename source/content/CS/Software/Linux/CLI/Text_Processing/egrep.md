egrep busca líneas que casan con un patrón interpretándolo como **expresión regular extendida** (ERE). Es funcionalmente idéntico a `grep -E` y acepta todas las opciones de [[grep]]; la única diferencia respecto a `grep` "pelado" es el sabor de regex por defecto. En GNU grep moderno `egrep` es un envoltorio **deprecado** que emite el aviso `egrep: warning: egrep is obsolescent; using grep -E` por stderr: se conserva por compatibilidad, pero la forma recomendada es `grep -E`. Esta guía cubre lo propio de ERE; para el resto de flags (contexto, recursión, formato, binarios, estado de salida) ver [[grep]].

```
egrep [opciones] PATRÓN [fichero...]      # ≡ grep -E [opciones] PATRÓN [fichero...]
```

---

## Diferencia BRE vs ERE

En regex básica (BRE, lo que usa `grep` por defecto) los metacaracteres `? + { } ( ) |` son literales salvo que se escapen con `\`. En ERE son metacaracteres directamente y se escapan para volverlos literales. Es la única diferencia semántica entre `egrep`/`grep -E` y `grep`.

| **Construcción** | **BRE (`grep`)**      | **ERE (`egrep` / `grep -E`)** |
| ---------------- | --------------------- | ----------------------------- |
| Alternancia      | `\|`                  | `\|`                          |
| Agrupación       | `\( \)`               | `( )`                         |
| Una o más        | `\+`                  | `+`                           |
| Cero o una       | `\?`                  | `?`                           |
| Intervalo        | `\{n,m\}`             | `{n,m}`                       |

---

## Metacaracteres ERE

| **Token**       | **Función**                                              | **Ejemplo**                                  |
| --------------- | -------------------------------------------------------- | -------------------------------------------- |
| `.`             | Cualquier carácter (salvo nueva línea).                  | `egrep "r..t" file`                          |
| `*`             | Cero o más del átomo previo.                             | `egrep "ab*c" file`                          |
| `+`             | Una o más del átomo previo.                              | `egrep "ab+c" file`                          |
| `?`             | Cero o una (opcional).                                   | `egrep "colou?r" file`                       |
| `{n}` `{n,}` `{n,m}` | Repetición exacta / mínima / en rango.              | `egrep "[0-9]{1,3}" hosts`                   |
| `\|`            | Alternancia (OR lógico).                                 | `egrep "warn\|error\|fatal" log`             |
| `( )`           | Agrupación / captura.                                    | `egrep "(ab)+" file`                         |
| `[ ]` `[^ ]`    | Clase de caracteres / clase negada.                      | `egrep "[aeiou]" f` · `egrep "[^0-9]" f`     |
| `^` `$`         | Ancla de inicio / fin de línea.                          | `egrep "^root" /etc/passwd`                  |
| `[[:clase:]]`   | Clases POSIX: `alpha`, `digit`, `alnum`, `space`, `upper`, `lower`, `punct`, `xdigit`… | `egrep "[[:digit:]]{4}" f` |
| `\b` `\<` `\>`  | Límite de palabra / inicio / fin de palabra (GNU).       | `egrep "\<admin\>" users.txt`                |

> ERE de POSIX **no** incluye lookarounds, `\d`, ni no-greedy. Para eso se necesita `grep -P` (PCRE), no `egrep`. Las extensiones `\b \< \> \w \s` sí las soporta GNU grep como añadido.

---

## Opciones

egrep hereda **todas** las opciones de grep. Las más usadas con ERE; la tabla completa está en [[grep]].

| **Flag**                       | **Función**                                       | **Ejemplo**                                  |
| ------------------------------ | ------------------------------------------------- | -------------------------------------------- |
| `-i`                           | Ignora mayúsculas/minúsculas.                     | `egrep -i "error\|warn" app.log`             |
| `-o`                           | Solo la porción coincidente.                      | `egrep -o "[0-9]{1,3}(\.[0-9]{1,3}){3}" log` |
| `-r` / `-R`                    | Búsqueda recursiva.                               | `egrep -r "TODO\|FIXME" src/`                |
| `-n`                           | Número de línea.                                  | `egrep -n "def \|class " app.py`             |
| `-v`                           | Invierte la coincidencia.                         | `egrep -v "^(#\|$)" config`                  |
| `-c`                           | Cuenta coincidencias.                             | `egrep -c "GET\|POST" acceso.log`            |
| `-w`                           | Palabra completa.                                 | `egrep -w "root\|admin" users.txt`           |
| `-e PATRÓN` / `-f FICHERO`     | Patrón explícito / patrones desde fichero.        | `egrep -f patrones.ere datos.txt`            |
| `-A`/`-B`/`-C NUM`             | Contexto después / antes / alrededor.             | `egrep -C2 "fail\|denied" auth.log`          |

---

## Casos de uso comunes

```bash
# Forma recomendada (sin el aviso de obsolescencia)
grep -E "warn|error|fatal" /var/log/syslog

# Varias palabras clave a la vez
egrep -i "login|logout|session" auth.log

# IPv4 completa
egrep -o "([0-9]{1,3}\.){3}[0-9]{1,3}" acceso.log | sort -u

# Líneas no vacías y no comentadas
egrep -v "^[[:space:]]*(#|$)" /etc/ssh/sshd_config

# Direcciones MAC
egrep -o "([0-9a-fA-F]{2}:){5}[0-9a-fA-F]{2}" salida.txt

# Filtro en dos fases: quitar ruido conocido (denylist en fichero, un patrón por línea)
# con -vf y luego quedarse con lo relevante. egrep "" casa todo: muestra lo que sobra
# tras descartar el ruido (cambiar "" por el patrón a buscar para afinar más).
egrep -vf patrones.exclude /var/log/messages | egrep ""
```

Relacionados: [[grep]] · [[sed]] · [[awk]] · [[rg-ripgrep]]
