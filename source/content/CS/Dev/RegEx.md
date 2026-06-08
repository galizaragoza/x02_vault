Una expresión regular (regex) es un patrón formal que describe conjuntos de cadenas de texto, usado para buscar, validar, extraer y sustituir. Existen varios "sabores" con sintaxis ligeramente distinta: **BRE** (POSIX básica, usada por `grep`/`sed` sin flags), **ERE** (POSIX extendida, `grep -E`/`sed -E`/`awk`) y **PCRE** (Perl-compatible, `grep -P`, `pcregrep`, lenguajes como Python/JS), que añade lookarounds, lazy matching y clases `\d`/`\w`/`\s`. Las tablas siguientes usan notación PCRE salvo indicación contraria.

♦

| **Flags** | **Función**                                                                                | **Ejemplo de patrón** | **Resultado (coincide con)**                 |
| --------- | ------------------------------------------------------------------------------------------ | --------------------- | -------------------------------------------- |
| `/g`      | global: La expressión selecciona todos los matches, de no estar solo selecciona el primero | `/\w+\.com/g`         | Encuentra cualquier dominio de todo el input |
| `/m`      | Regex ve el texto como 1 line, con la flag multiline se procesa el texto línea por línea   | `/regex/m`            |                                              |
| `/i`      | Desactive la case-sensitivity                                                              | `/REGEX/i`            |                                              |
|           |                                                                                            |                       |                                              |


| **Categoría**                 | **Elemento** | **Función**                                                   | **Ejemplo de patrón**                     | **Resultado (coincide con)**          |
| ----------------------------- | ------------ | ------------------------------------------------------------- | ----------------------------------------- | ------------------------------------- |
| Clases de caracteres          | `.`          | Encuentra cualquier carácter excepto salto de línea.          | `a.c`                                     | "abc", "a1c"                          |
| Clases de caracteres          | `\d`         | Encuentra cualquier dígito [0-9].                             | `\d\d`                                    | "12", "99"                            |
| Clases de caracteres          | `\D`         | Encuentra cualquier carácter que no sea dígito.               | `\D+`                                     | "abc", "!!!"                          |
| Clases de caracteres          | `\w`         | Encuentra cualquier carácter alfanumérico [a-zA-Z0-9_].       | `\w+`                                     | "word_1", "abc123"                    |
| Clases de caracteres          | `\W`         | Encuentra cualquier carácter que no sea alfanumérico.         | `\W+`                                     | "!!!", "@#%"                          |
| Clases de caracteres          | `\s`         | Encuentra cualquier espacio en blanco.                        | `\s+`                                     | " ", "\t\n"                           |
| Clases de caracteres          | `\S`         | Encuentra cualquier carácter que no sea espacio en blanco.    | `\S+`                                     | "abc", "123"                          |
| Clases de caracteres          | `[abc]`      | Encuentra cualquiera de los caracteres a, b o c.              | `[abc]+`                                  | "abc", "bba"                          |
| Clases de caracteres          | `[^abc]`     | Encuentra cualquier carácter que no sea a, b o c.             | `[^abc]`                                  | "d", "1"                              |
| Clases de caracteres          | `[a-g]`      | Encuentra cualquier carácter entre a y g.                     | `[a-g]`                                   | "b", "f"                              |
| Anclajes                      | `^`          | Encuentra el inicio de la cadena o línea.                     | `^abc`                                    | "abcdef" (al inicio)                  |
| Anclajes                      | `$`          | Encuentra el fin de la cadena o línea.                        | `abc$`                                    | "defabc" (al final)                   |
| Límites                       | `\b`         | Encuentra un límite de palabra.                               | `\bword\b`                                | "word" (palabra completa)             |
| Límites                       | `\B`         | Encuentra un no-límite de palabra.                            | `a\Bb`                                    | "aab" (no límite)                     |
| Caracteres escapados          | `\. \* \\`   | Encuentra los caracteres especiales escapados como literales. | `\.`                                      | "." (literal)                         |
| Caracteres escapados          | `\t \n \r`   | Encuentra tabulador, salto de línea o retorno de carro.       | `\n`                                      | salto de línea                        |
| Caracteres escapados          | `\`          | Escapa metacaracteres para encontrarlos como literales.       | `\+`                                      | "+" (literal)                         |
| Piping                        | \|           | Indica que una expresión puede estar en distintas expresiones | (c\|r)at\|                                | Encuentra 'cat' y 'rat'               |
| Grupos y lookaround           | `(abc)`      | Encuentra y captura el grupo abc.                             | `(abc)`                                   | "abc" (capturado)                     |
| Grupos y lookaround           | `(?:abc)`    | Encuentra el grupo abc sin capturarlo.                        | `(?:abc)+`                                | "abcabc"                              |
| Grupos y lookaround           | `\1`         | Encuentra una referencia al grupo capturado #1.               | `(a)\1`                                   | "aa"                                  |
| Lookahead positivo            | `(?=abc)`    | Encuentra una aserción positiva lookahead para abc.           | `abc(?=def)`                              | "abcdef" (mira adelante "def")        |
| Lookahead negativo            | `(?!abc)`    | Encuentra una aserción negativa lookahead para abc.           | `abc(?!def)`                              | "abcxyz" (no "def" después)           |
| Lookbehind positivo           | `(?<=)`      | Mira tras el valor indicado                                   | `(?<=\$)\d+`                              | Encuentra n+ después de '$'           |
| Lookebehind negativo          | `(?<!)`      | Negación tras el valor indicado                               | `(?<!\$)\d+`                              | Encuentra n+ no precedido por '$'     |
| Cuantificadores y alternación | `*`          | Encuentra 0 o más repeticiones.                               | `*a`                                      | Todas la exp. terminadas en 'a'       |
| Lazy matching                 | `.*?`        | Por defecto regex hace greedy matching, lo más largo posible  | `.*?a`                                    | Solo la primera exp. terminada en 'a' |
| Cuantificadores y alternación | `+`          | Encuentra 1 o más repeticiones.                               | `a+`                                      | "a", "aaa"                            |
| Cuantificadores y alternación | `?`          | Encuentra 0 o 1 repetición.                                   | `a?`                                      | "", "a"                               |
| Cuantificadores y alternación | `{5}`        | Encuentra exactamente 5 repeticiones.                         | `a{5}`                                    | "aaaaa"                               |
| Cuantificadores y alternación | `{2,}`       | Encuentra 2 o más repeticiones.                               | `a{2,}`                                   | "aa", "aaaa"                          |
| Cuantificadores y alternación | `{1,3}`      | Encuentra entre 1 y 3 repeticiones.                           | `a{1,3}`                                  | "a", "aaa"                            |
| Cuantificadores y alternación | `+? {2,}?`   | Encuentra el mínimo número de repeticiones posible.           | `a+?`                                     | "a" (mínimo en greedy)                |
| Cuantificadores y alternación | `            | `                                                             | Encuentra una alternación entre opciones. | `a                                    |

# Clases de caracteres POSIX

Válidas dentro de corchetes (`[[:clase:]]`), portables entre BRE/ERE/PCRE.

| Clase | Equivalente | Coincide con |
|-------|-------------|--------------|
| `[[:alpha:]]` | `[A-Za-z]` | Letras. |
| `[[:digit:]]` | `[0-9]` / `\d` | Dígitos. |
| `[[:alnum:]]` | `[A-Za-z0-9]` | Letras y dígitos. |
| `[[:lower:]]` | `[a-z]` | Minúsculas. |
| `[[:upper:]]` | `[A-Z]` | Mayúsculas. |
| `[[:space:]]` | `\s` | Espacio, tab, newline, etc. |
| `[[:blank:]]` | `[ \t]` | Espacio y tabulador. |
| `[[:punct:]]` | — | Signos de puntuación. |
| `[[:xdigit:]]` | `[0-9A-Fa-f]` | Dígitos hexadecimales. |
| `[[:cntrl:]]` | — | Caracteres de control. |
| `[[:print:]]` | — | Caracteres imprimibles (incluye espacio). |
| `[[:graph:]]` | — | Imprimibles excepto espacio. |

---

# Equivalencias regex ↔ expansiones de Bash

Bash no usa regex en el *globbing* de ficheros sino **patrones glob** (y *extended globbing* con `shopt -s extglob`). El operador `[[ str =~ regex ]]` sí usa ERE. Esta tabla relaciona construcciones regex con su equivalente glob/extglob. Diferencia clave: los globs son **anclados al patrón completo** (coincide toda la cadena), mientras que una regex coincide por defecto con cualquier subcadena.

| Construcción regex | Glob / extglob de Bash | Significado | Ejemplo Bash |
|--------------------|------------------------|-------------|--------------|
| `.` | `?` | Un carácter cualquiera. | `ls archivo?.txt` |
| `.*` | `*` | Cero o más caracteres cualesquiera. | `ls *.log` |
| `[abc]` | `[abc]` | Uno de la lista. | `ls foto[123].png` |
| `[a-z]` | `[a-z]` | Rango de caracteres. | `ls [a-z]*.sh` |
| `[^abc]` / `[^...]` | `[!abc]` / `[!...]` | Cualquiera fuera de la lista. | `ls [!._]*` |
| `[[:digit:]]` | `[[:digit:]]` | Clase POSIX (idéntica en ambos). | `ls log[[:digit:]].txt` |
| `(foo\|bar)` | `@(foo\|bar)` | Exactamente una de las alternativas. | `rm @(tmp\|cache).db` |
| `(foo)?` | `?(foo)` | Cero o una ocurrencia del grupo. | `ls index?(.html)` |
| `(foo)*` | `*(foo)` | Cero o más ocurrencias del grupo. | `ls *(ab)c` |
| `(foo)+` | `+(foo)` | Una o más ocurrencias del grupo. | `ls +([0-9]).txt` |
| `(?!foo)` (negación) | `!(foo)` | Todo lo que **no** coincide con el patrón. | `ls !(*.bak)` |
| `^...$` (anclaje total) | (implícito) | Coincidencia completa de la cadena. | El glob ya exige match completo. |
| `{n,m}` (cuantificador) | `{a,b,c}` (brace exp.) | **No equivalen**: en regex es repetición; en Bash es expansión de lista literal. | `touch f{1,2,3}.txt` |
| `\d{1,3}` (rango num.) | `{1..255}` (brace exp.) | **No equivalen**: brace expansion genera secuencias literales antes del globbing. | `echo {1..10}` |

> [!warning] Brace expansion (`{1..5}`, `{a,b}`) y los cuantificadores de regex (`{1,5}`) **no son lo mismo**. La brace expansion ocurre antes y genera cadenas literales; no filtra por coincidencia. Para repetición real de patrones en Bash se usan los operadores extglob `*( )` / `+( )`.

Para usar **regex ERE auténtica** en Bash, no glob:

```bash
shopt -s extglob               # habilita @() ?() *() +() !()

if [[ "$ip" =~ ^([0-9]{1,3}\.){3}[0-9]{1,3}$ ]]; then
    echo "IP válida: ${BASH_REMATCH[0]}"   # grupos en BASH_REMATCH[n]
fi
```

---

# Protips
### Capturar IPs
```
([0-9]{1,3}\.){3}[0-9]{1,3}
```

### Otros patrones útiles
```bash
# Email (aproximado)
[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}

# URL http/https
https?://[^\s/$.?#].[^\s]*

# Fecha ISO 8601 (YYYY-MM-DD)
\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01])

# MAC address
([0-9A-Fa-f]{2}[:-]){5}[0-9A-Fa-f]{2}

# Hash MD5 (32 hex) / SHA-1 (40 hex)
\b[a-fA-F0-9]{32}\b
\b[a-fA-F0-9]{40}\b
```

![[regexs.png]]