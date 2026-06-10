`bc` (Basic Calculator) es un lenguaje de programación de línea de comandos para cálculo numérico de **precisión arbitraria**. A diferencia de la aritmética de coma flotante del shell o de `expr`, opera con decimales exactos controlados por la variable `scale` y soporta bases de entrada/salida configurables, variables, funciones definidas por el usuario y estructuras de control. Lee expresiones desde ficheros y/o desde la entrada estándar, evaluándolas secuencialmente. Esta guía documenta **GNU bc** (versión instalada 1.08.2); al final se anota la implementación alternativa de Gavin Howard.

# Opciones de línea de comandos
GNU bc admite únicamente estas opciones. Los argumentos posicionales se tratan como ficheros de código que se procesan antes de leer la entrada estándar.

| **Flag**             | **Descripción**                                                                                       | **Ejemplo**                          |
| -------------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------ |
| `-h`, `--help`       | Muestra el uso y termina.                                                                             | `bc --help`                          |
| `-i`, `--interactive`| Fuerza el modo interactivo aunque la entrada no sea un terminal.                                       | `bc -i < script.bc`                  |
| `-l`, `--mathlib`    | Carga la biblioteca matemática estándar y fija `scale=20`.                                            | `echo 's(1)' \| bc -l`               |
| `-q`, `--quiet`      | No imprime el banner inicial de GNU bc.                                                               | `bc -q`                              |
| `-s`, `--standard`   | Modo POSIX estricto: las construcciones no estándar son errores.                                      | `bc -s programa.bc`                  |
| `-w`, `--warn`       | Avisa (sin error) cuando se usan extensiones no POSIX de GNU.                                          | `bc -w programa.bc`                  |
| `-v`, `--version`    | Muestra la versión y termina.                                                                         | `bc --version`                       |
| `file ...`           | Ficheros de código a evaluar antes de leer stdin (argumento posicional).                              | `bc -l calculos.bc`                  |

# Variables de entorno
Controlan el comportamiento al arrancar y el formateo de salida.

| **Variable**       | **Descripción**                                                                                       | **Ejemplo**                          |
| ------------------ | ----------------------------------------------------------------------------------------------------- | ------------------------------------ |
| `BC_ENV_ARGS`      | Opciones/ficheros aplicados automáticamente al iniciar, como si fueran argumentos de línea de comandos.| `export BC_ENV_ARGS='-l'`            |
| `BC_LINE_LENGTH`   | Nº de columnas para el ajuste de líneas de salida (por defecto 70); `0` desactiva el ajuste.           | `export BC_LINE_LENGTH=0`            |
| `POSIXLY_CORRECT`  | Equivale a `-s`: fuerza el modo POSIX estándar.                                                        | `POSIXLY_CORRECT=1 bc`               |

# Variables especiales
Variables internas reservadas que alteran cómo se interpretan y muestran los números.

| **Variable**       | **Descripción**                                                                                       | **Ejemplo**                          |
| ------------------ | ----------------------------------------------------------------------------------------------------- | ------------------------------------ |
| `scale`            | Nº de dígitos decimales tras la coma en los resultados (por defecto 0; `-l` lo pone en 20).            | `scale=10; 1/3`                      |
| `ibase`            | Base de entrada (2–16; por defecto 10). Afecta a cómo se interpretan los números tecleados.            | `ibase=16; FF`                       |
| `obase`            | Base de salida (2 a `BC_BASE_MAX`; por defecto 10). `obase=2` muestra binario, `16` hexadecimal.       | `obase=16; 255`                      |
| `last`, `.`        | Último valor impreso (extensión GNU). `.` es alias de `last`.                                          | `3*4; last+1`                        |

> Orden recomendado: fijar `obase` **después** de `ibase`, ya que `ibase` cambia cómo se leen los dígitos siguientes (incluido el propio valor de `obase`).

# Operadores
Operadores del lenguaje, ordenados por categoría. Los marcados (GNU) son extensiones no POSIX.

| **Operador**             | **Descripción**                                                          | **Ejemplo**            |
| ------------------------ | ------------------------------------------------------------------------ | ---------------------- |
| `+` `-` `*` `/`          | Suma, resta, multiplicación, división (división truncada según `scale`).  | `7/2` → `3` (scale=0)  |
| `%`                      | Resto. Depende de `scale` (resto de la división con esa precisión).       | `scale=0; 7%3` → `1`   |
| `^`                      | Potenciación (exponente entero).                                          | `2^10` → `1024`        |
| `-` (unario)             | Negación.                                                                 | `-5`                   |
| `++` `--`                | Incremento/decremento, prefijo o postfijo (GNU).                          | `i=5; i++` ; `++i`     |
| `=`                      | Asignación.                                                               | `x = 4`                |
| `+=` `-=` `*=` `/=` `%=` `^=` | Asignación compuesta.                                                | `x *= 2`               |
| `==` `!=` `<` `<=` `>` `>=`   | Operadores relacionales (devuelven 1/0).                             | `if (a >= b) ...`      |
| `!`                      | NOT lógico (GNU).                                                          | `!0` → `1`             |
| `&&` `\|\|`              | AND / OR lógico (GNU).                                                     | `a>0 && b>0`           |
| `( )`                    | Agrupación / precedencia.                                                  | `(2+3)*4`              |

# Sentencias y estructuras de control
Construcciones del lenguaje para flujo y E/S. Varias son extensiones GNU.

| **Sentencia**            | **Descripción**                                                          | **Ejemplo**                          |
| ------------------------ | ------------------------------------------------------------------------ | ------------------------------------ |
| `expr`                   | Evalúa e imprime la expresión (salvo asignaciones).                       | `2+2`                                |
| `"cadena"`               | Imprime un literal de cadena.                                            | `"Resultado: "; x`                   |
| `print` (GNU)            | Imprime lista de expresiones/cadenas sin salto de línea automático; admite escapes (`\n`).| `print x, "\n"`     |
| `{ stmt; ... }`          | Bloque de sentencias.                                                    | `{ a=1; b=2 }`                       |
| `if (cond) s [else s]`   | Condicional (`else` es extensión GNU).                                   | `if (x>0) "pos" else "neg"`          |
| `while (cond) s`         | Bucle mientras la condición sea cierta.                                  | `while (i<5) { i+=1 }`               |
| `for (init; cond; upd) s`| Bucle for clásico.                                                       | `for (i=0;i<3;i++) i`                |
| `break`                  | Sale del bucle `while`/`for` actual.                                     | `if (i>9) break`                     |
| `continue` (GNU)         | Salta a la siguiente iteración del bucle.                                | `if (i%2) continue`                  |
| `halt` (GNU)             | Detiene la ejecución de bc (como una sentencia).                         | `if (err) halt`                      |
| `return`, `return(expr)` | Devuelve un valor desde una función definida.                            | `return(a+b)`                        |
| `quit`                   | Termina bc inmediatamente al **leerse** (incluso dentro de un bloque no ejecutado). | `quit`                    |
| `define f(params) {...}` | Define una función (ver sección de funciones).                           | `define sq(x){return x*x}`           |
| `auto v1, v2`            | Declara variables locales dentro de una función.                         | `auto i, tmp`                        |
| `limits` (GNU)           | Muestra los límites internos del intérprete (BASE_MAX, SCALE_MAX, ...).  | `limits`                             |
| `warranty` (GNU)         | Muestra la cláusula de garantía.                                         | `warranty`                           |

# Funciones internas
Funciones disponibles sin cargar la biblioteca matemática.

| **Función**        | **Descripción**                                                                  | **Ejemplo**            |
| ------------------ | -------------------------------------------------------------------------------- | ---------------------- |
| `length(expr)`     | Nº total de dígitos significativos del valor.                                     | `length(123.45)` → `5` |
| `scale(expr)`      | Nº de dígitos tras la coma del valor.                                             | `scale(1.234)` → `3`   |
| `sqrt(expr)`       | Raíz cuadrada (precisión según `scale`).                                          | `sqrt(2)`              |
| `read()` (GNU)     | Lee un número desde la entrada estándar durante la ejecución.                     | `x = read()`           |

# Funciones definidas por el usuario
`define` permite crear funciones; `auto` declara variables locales. Las funciones pueden ser recursivas y aceptar arrays por referencia con la sintaxis `nombre[]`.

```bc
define fact(n) {
    if (n <= 1) return (1)
    return (n * fact(n-1))
}
fact(5)   /* 120 */
```

# Biblioteca matemática (`-l`)
Activada con `-l`/`--mathlib`. Fija `scale=20` y añade funciones trascendentes. Los ángulos se expresan en **radianes**.

| **Función**   | **Descripción**                                  | **Ejemplo**            |
| ------------- | ------------------------------------------------ | ---------------------- |
| `s(x)`        | Seno de `x`.                                     | `s(0)` → `0`           |
| `c(x)`        | Coseno de `x`.                                   | `c(0)` → `1`           |
| `a(x)`        | Arcotangente de `x` (útil: `4*a(1)` = π).         | `4*a(1)`               |
| `l(x)`        | Logaritmo natural (ln) de `x`.                   | `l(e(1))` → `1`        |
| `e(x)`        | Exponencial `e^x`.                               | `e(1)`                 |
| `j(n,x)`      | Función de Bessel de orden `n` en `x`.           | `j(0,1)`               |

# Comentarios
| **Sintaxis**   | **Descripción**                                  | **Ejemplo**            |
| -------------- | ------------------------------------------------ | ---------------------- |
| `/* ... */`    | Comentario de bloque (POSIX).                    | `x=1 /* contador */`   |
| `# ...`        | Comentario de línea (extensión GNU).             | `x=1  # contador`      |

# Patrones de uso comunes
| **Objetivo**                       | **Comando**                                       |
| ---------------------------------- | ------------------------------------------------- |
| Cálculo rápido no interactivo      | `echo "3.14 * 2" \| bc`                            |
| División con decimales             | `echo "scale=4; 22/7" \| bc`                       |
| Conversión decimal → hexadecimal   | `echo "obase=16; 255" \| bc`                       |
| Conversión hexadecimal → decimal   | `echo "ibase=16; FF" \| bc`                        |
| Conversión decimal → binario       | `echo "obase=2; 10" \| bc`                         |
| Funciones trigonométricas          | `echo "s(3.14159/2)" \| bc -l`                     |
| Valor de π                         | `echo "scale=20; 4*a(1)" \| bc -l`                 |
| Uso dentro de script (resultado a variable) | `r=$(echo "$a + $b" \| bc -l)`           |

# Nota: implementación alternativa (Gavin Howard `bc`)
Algunas distribuciones (toybox, FreeBSD, ciertos Linux) embarcan el `bc` de Gavin Howard, compatible con POSIX y GNU pero con flags adicionales. **No están disponibles en GNU bc.**

| **Flag**                 | **Descripción**                                                          |
| ------------------------ | ------------------------------------------------------------------------ |
| `-e <expr>`, `--expression` | Evalúa la expresión indicada (sin entrar en modo interactivo).        |
| `-f <file>`, `--file`    | Procesa el fichero indicado.                                             |
| `-g`, `--global-stacks`  | `ibase`/`obase`/`scale`/`seed` con pila global por función.              |
| `-z`, `--leading-zeroes` | Imprime ceros a la izquierda en valores `\|x\|<1`.                       |
| `-P`, `--no-prompt`      | Desactiva el prompt interactivo.                                         |
| `-R`, `--no-read-prompt` | Desactiva el prompt de `read()`.                                         |
| `-c`/`-C`                | Activa/desactiva el corte de líneas (`clamp`/digit clamping).            |

Para scripts portables, evitar las extensiones de cualquiera de las dos implementaciones y usar `-s` para validar conformidad POSIX.
