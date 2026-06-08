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

# Protips
### Capturar IPs
```
([0-9]{1,3}\.){3}[0-9]{1,3}
```


![[regexs.png]]