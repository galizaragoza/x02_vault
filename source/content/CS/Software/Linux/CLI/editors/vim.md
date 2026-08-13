oVim (Vi IMproved) es un editor de texto modal de terminal, sucesor de `vi`, presente en prácticamente cualquier sistema Unix. Su modelo de edición se basa en **modos** (normal, inserción, visual, comando) y en una gramática componible de *operador + movimiento/objeto de texto* que permite expresar ediciones complejas con pocas pulsaciones. Esta guía cubre los comandos por modo, los motions, operadores, registros, macros y la configuración esencial.

```
vim [opciones] [fichero...]
```

---

## Los modos

| Modo | Cómo se entra | Propósito |
|------|---------------|-----------|
| **Normal** | `Esc` (estado por defecto al abrir) | Navegación y comandos de edición. |
| **Inserción** | `i` `a` `o` `I` `A` `O` `s` `c` | Escribir texto literal. |
| **Visual** | `v` (carácter), `V` (línea), `Ctrl-v` (bloque) | Seleccionar texto. |
| **Comando / Ex** | `:` desde normal | Comandos de línea (`:w`, `:s`, `:g`...). |
| **Reemplazo** | `R` | Sobrescribe en lugar de insertar. |

---

## Opciones de la línea de comandos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `+<n>` | Abre el fichero y sitúa el cursor en la línea `<n>`. | `vim +25 fichero.c` |
| `+/<patrón>` | Abre y salta a la primera coincidencia del patrón. | `vim +/main fichero.c` |
| `-o[N]` / `-O[N]` | Abre los ficheros en ventanas horizontales / verticales. | `vim -O a.txt b.txt` |
| `-p[N]` | Abre cada fichero en una pestaña. | `vim -p *.md` |
| `-R` | Modo solo lectura. | `vim -R /etc/passwd` |
| `-d` | Modo diff (equivale a `vimdiff`). | `vim -d v1.c v2.c` |
| `-u <vimrc>` | Usa el fichero de configuración indicado (`NONE` = ninguno). | `vim -u NONE fichero` |
| `-c <cmd>` | Ejecuta el comando Ex tras cargar. | `vim -c 'set nu' fichero` |
| `-` | Lee el texto desde stdin. | `cmd \| vim -` |
| `-b` | Modo binario (para editar con `xxd`). | `vim -b binario` |
| `-r` | Lista o recupera ficheros de swap (tras un cierre abrupto). | `vim -r fichero` |
| `--version` | Muestra versión y características compiladas (`+clipboard`, etc.). | `vim --version` |

---

## Modo Inserción — entrada

| Tecla | Acción | 
|-------|--------|
| `i` / `a` | Inserta antes / después del cursor. |
| `I` / `A` | Inserta al inicio (primer no-blanco) / final de la línea. |
| `o` / `O` | Abre línea nueva debajo / encima e inserta. |
| `s` / `S` | Borra carácter / línea y entra en inserción. |
| `gi` | Vuelve al último punto de inserción. |
| `Ctrl-w` | Borra la palabra anterior (en inserción). |
| `Ctrl-u` | Borra hasta el inicio de la línea. |
| `Ctrl-r <reg>` | Inserta el contenido de un registro. |
| `Ctrl-a` | Inserta el texto insertado por última vez. |
| `Ctrl-o <cmd>` | Ejecuta un comando normal sin salir de inserción. |
| `Ctrl-n` / `Ctrl-p` | Autocompletado de palabra (siguiente / anterior). |
| `Ctrl-t` / `Ctrl-d` | Aumenta / disminuye la indentación de la línea. |
| `Esc` / `Ctrl-[` | Vuelve a modo normal. |

---

## Movimientos básicos (motions)

| Tecla | Movimiento |
|-------|-----------|
| `h` `j` `k` `l` | Izquierda, abajo, arriba, derecha. |
| `0` / `^` / `$` | Inicio de línea / primer no-blanco / fin de línea. |
| `w` / `W` | Inicio de la siguiente palabra (puntuación / separada por espacios). |
| `e` / `E` | Final de la siguiente palabra. |
| `b` / `B` | Inicio de la palabra anterior. |
| `ge` | Final de la palabra anterior. |
| `gg` / `G` | Primera / última línea del fichero. |
| `<n>G` / `:<n>` | Ir a la línea `<n>`. |
| `{` / `}` | Párrafo anterior / siguiente (bloque entre líneas vacías). |
| `(` / `)` | Frase anterior / siguiente. |
| `%` | Salta al paréntesis/llave/corchete pareja. |
| `H` / `M` / `L` | Cima / medio / fondo de la pantalla visible. |
| `Ctrl-d` / `Ctrl-u` | Media página abajo / arriba. |
| `Ctrl-f` / `Ctrl-b` | Página completa adelante / atrás. |
| `zz` / `zt` / `zb` | Centra / arriba / abajo la línea actual en pantalla. |

---

## Búsqueda de carácter en línea

| Tecla | Acción |
|-------|--------|
| `f<c>` / `F<c>` | Salta al siguiente / anterior carácter `<c>`. |
| `t<c>` / `T<c>` | Salta justo antes del siguiente / anterior `<c>`. |
| `;` / `,` | Repite la última `f`/`t` en la misma / opuesta dirección. |

---

## Operadores

Se combinan con un motion o un objeto de texto: `<operador><motion>`.

| Operador | Acción | Ejemplo |
|----------|--------|---------|
| `d` | Borra (delete). | `dw` borra una palabra; `dd` borra la línea. |
| `c` | Cambia (borra y entra en inserción). | `ciw` cambia la palabra; `cc` la línea. |
| `y` | Copia (yank). | `yy` copia la línea; `y$` hasta fin de línea. |
| `>` / `<` | Indenta / des-indenta. | `>}` indenta el párrafo. |
| `=` | Reindenta según las reglas del lenguaje. | `gg=G` reindenta todo el fichero. |
| `gu` / `gU` / `g~` | A minúsculas / mayúsculas / alterna caso. | `gUiw` pone la palabra en mayúsculas. |
| `gq` / `gw` | Reformatea (justifica) el texto al ancho `textwidth`. | `gqap` formatea el párrafo. |
| `!` | Filtra las líneas por un comando externo. | `!}sort` ordena el párrafo. |
| `zf` | Crea un *fold* (pliegue) sobre el motion. | `zf}` pliega el párrafo. |

Duplicar el operador actúa sobre la línea entera (`dd`, `yy`, `>>`). Un contador lo repite: `3dd` borra 3 líneas.

---

## Objetos de texto (text objects)

Se usan tras un operador o en modo visual. `i` = *inner* (contenido), `a` = *a/around* (contenido + delimitadores).

| Objeto | Selección | Ejemplo |
|--------|-----------|---------|
| `iw` / `aw` | Palabra / palabra + espacio. | `daw` borra palabra y su espacio. |
| `iW` / `aW` | Palabra delimitada por espacios. | `ciW` cambia el "WORD". |
| `is` / `as` | Frase. | `das` borra la frase. |
| `ip` / `ap` | Párrafo. | `dap` borra el párrafo. |
| `i"` `a"` / `i'` `a'` | Contenido entre comillas. | `ci"` cambia el texto entre comillas. |
| `i(` `a(` / `ib` `ab` | Entre paréntesis. | `di(` borra el interior de los paréntesis. |
| `i{` `a{` / `iB` `aB` | Entre llaves. | `ci{` cambia el cuerpo del bloque. |
| `i[` `a[` | Entre corchetes. | `yi[` copia el contenido del array. |
| `i<` `a<` | Entre `<` `>`. | `cit` para tags HTML. |
| `it` / `at` | Etiqueta XML/HTML (interior / con tags). | `dit` borra el contenido del tag. |

---

## Edición rápida en modo normal

| Tecla | Acción |
|-------|--------|
| `x` / `X` | Borra el carácter bajo / antes del cursor. |
| `r<c>` | Reemplaza el carácter por `<c>`. |
| `~` | Alterna mayús/minús del carácter y avanza. |
| `J` / `gJ` | Une la línea siguiente (con / sin espacio). |
| `p` / `P` | Pega después / antes del cursor. |
| `]p` | Pega ajustando la indentación. |
| `.` | Repite el último cambio. |
| `u` / `Ctrl-r` | Deshace / rehace. |
| `Ctrl-a` / `Ctrl-x` | Incrementa / decrementa el número bajo el cursor. |
| `>>` / `<<` | Indenta / des-indenta la línea. |
| `==` | Reindenta la línea. |

---

## Modo Visual

| Tecla | Acción |
|-------|--------|
| `v` / `V` / `Ctrl-v` | Selección por carácter / línea / bloque. |
| `o` | Mueve el cursor al otro extremo de la selección. |
| `gv` | Reselecciona la última selección visual. |
| `d` / `y` / `c` | Borra / copia / cambia la selección. |
| `>` / `<` | Indenta / des-indenta la selección. |
| `u` / `U` / `~` | A minúsculas / mayúsculas / alterna. |
| `r<c>` | Reemplaza toda la selección por `<c>`. |
| `:` | Aplica un comando Ex sobre el rango seleccionado (`:'<,'>`). |
| `I` / `A` (en bloque) | Inserta al inicio / final de todas las líneas del bloque. |
| `$` (en bloque) | Extiende la selección al final de cada línea. |

---

## Registros (copy/paste)

| Sintaxis | Descripción |
|----------|-------------|
| `"<reg><op>` | Usa un registro concreto. `"ayy` copia la línea al registro `a`. |
| `"Ayy` | Mayúscula = **añade** al registro `a` en vez de sobrescribir. |
| `"0` | Registro de la última copia (`y`), no afectado por borrados. |
| `"+` / `"*` | Portapapeles del sistema (X11: `+` clipboard, `*` selección primaria). |
| `"_` | Registro "agujero negro": borra sin afectar a otros registros (`"_dd`). |
| `"%` | Nombre del fichero actual. |
| `".` | Último texto insertado. |
| `":` | Último comando Ex ejecutado. |
| `"/` | Último patrón de búsqueda. |
| `:reg` | Muestra el contenido de todos los registros. |

---

## Marcas y saltos

| Tecla | Acción |
|-------|--------|
| `m<a-z>` | Coloca una marca local (en el fichero). |
| `m<A-Z>` | Marca global (entre ficheros). |
| `` `<marca> `` | Salta a la posición exacta de la marca. |
| `'<marca>` | Salta al inicio de la línea de la marca. |
| `` `` `` (dos backticks) | Vuelve a la posición previa al último salto. |
| `` `. `` | Salta al último punto editado. |
| `Ctrl-o` / `Ctrl-i` | Retrocede / avanza en la lista de saltos (jumplist). |
| `g;` / `g,` | Retrocede / avanza en la lista de cambios (changelist). |
| `:marks` | Lista las marcas. |

---

## Búsqueda y sustitución

| Comando                   | Descripción                                             | Ejemplo                             |
| ------------------------- | ------------------------------------------------------- | ----------------------------------- |
| `/<patrón>` / `?<patrón>` | Busca hacia adelante / atrás.                           | `/error`                            |
| `n` / `N`                 | Siguiente / anterior coincidencia.                      |                                     |
| `*` / `#`                 | Busca la palabra bajo el cursor hacia adelante / atrás. |                                     |
| `:noh`                    | Desactiva el resaltado de la última búsqueda.           | `:noh`                              |
| `:s/old/new/`             | Sustituye en la línea actual (primera ocurrencia).      | `:s/foo/bar/`                       |
| `:s/old/new/g`            | Sustituye todas las ocurrencias de la línea.            | `:s/foo/bar/g`                      |
| `:%s/old/new/g`           | Sustituye en todo el fichero.                           | `:%s/foo/bar/g`                     |
| `:%s/old/new/gc`          | Igual, pero pide confirmación en cada una.              | `:%s/foo/bar/gc`                    |
| `:'<,'>s/.../.../`        | Sustituye solo en el rango visual seleccionado.         |                                     |
| `:g/patrón/cmd`           | Ejecuta `cmd` en cada línea que coincide.               | `:g/TODO/d` (borra líneas con TODO) |
| `:v/patrón/cmd`           | Ejecuta `cmd` en cada línea que **no** coincide.        | `:v/^#/d`                           |

---

## Comandos Ex de ficheros y ventanas

| Comando | Descripción |
|---------|-------------|
| `:w` / `:w <fichero>` | Guarda / guarda con otro nombre. |
| `:wa` / `:xa` | Guarda todos / guarda todos y sale. |
| `:q` / `:q!` | Sale / sale descartando cambios. |
| `:wq` / `:x` / `ZZ` | Guarda y sale. |
| `:e <fichero>` | Abre/recarga un fichero. `:e!` descarta cambios. |
| `:r <fichero>` | Inserta el contenido de un fichero. |
| `:r !<cmd>` | Inserta la salida de un comando shell. |
| `:!<cmd>` | Ejecuta un comando shell. |
| `:sp` / `:vsp` | Divide la ventana horizontal / verticalmente. |
| `Ctrl-w h/j/k/l` | Mueve el foco entre ventanas. |
| `Ctrl-w q` / `Ctrl-w o` | Cierra la ventana / cierra las demás. |
| `Ctrl-w =` / `Ctrl-w _` | Iguala / maximiza el tamaño de ventanas. |

---

## Buffers y pestañas

| Comando | Descripción |
|---------|-------------|
| `:ls` / `:buffers` | Lista los buffers abiertos. |
| `:b <n/nombre>` | Cambia al buffer indicado. |
| `:bn` / `:bp` | Buffer siguiente / anterior. |
| `:bd` | Cierra (descarga) el buffer. |
| `:tabnew` / `:tabe <f>` | Nueva pestaña / abre fichero en pestaña. |
| `gt` / `gT` | Pestaña siguiente / anterior. |
| `<n>gt` | Va a la pestaña `<n>`. |

---

## Folds (pliegues)

| Comando | Descripción |
|---------|-------------|
| `zf<motion>` | Crea un fold manual. |
| `zo` / `zc` / `za` | Abre / cierra / alterna el fold bajo el cursor. |
| `zR` / `zM` | Abre / cierra todos los folds. |
| `zj` / `zk` | Salta al siguiente / anterior fold. |
| `:set foldmethod=indent` | Pliega automáticamente por indentación (o `syntax`, `marker`). |

---

## Macros

| Tecla | Acción |
|-------|--------|
| `q<reg>` | Inicia la grabación en el registro `<reg>`. |
| `q` | Detiene la grabación. |
| `@<reg>` | Reproduce la macro. |
| `@@` | Repite la última macro ejecutada. |
| `<n>@<reg>` | Ejecuta la macro `<n>` veces. |
| `:%normal @a` | Aplica la macro `a` a todas las líneas. |

---

## Opciones de configuración esenciales (`:set` / `.vimrc`)

| Opción | Efecto |
|--------|--------|
| `number` / `relativenumber` | Numeración absoluta / relativa de líneas. |
| `ignorecase` / `smartcase` | Búsqueda insensible a caso (salvo si hay mayúsculas). |
| `hlsearch` / `incsearch` | Resalta / busca incrementalmente. |
| `expandtab` / `tabstop=N` / `shiftwidth=N` | Tabs como espacios / ancho del tab / ancho de indentación. |
| `autoindent` / `smartindent` | Indentación automática. |
| `wrap` / `nowrap` | Ajuste visual de línea. |
| `syntax on` | Resaltado de sintaxis. |
| `clipboard=unnamedplus` | Usa el portapapeles del sistema por defecto. |
| `mouse=a` | Habilita el ratón en todos los modos. |
| `undofile` | Persiste el historial de undo entre sesiones. |

---

## Protips y trucos

```vim
" Editar como root un fichero abierto sin privilegios
:w !sudo tee % > /dev/null

" Reindentar todo el fichero
gg=G

" Ordenar líneas (rango visual o todo el fichero)
:sort
:sort u            " ordena y elimina duplicados
:sort n            " orden numérico

" Borrar todas las líneas en blanco
:g/^$/d

" Numerar/insertar texto en columna en bloques verticales:
"   Ctrl-v -> seleccionar columna -> I texto Esc

" Insertar un número incremental en líneas seleccionadas (Vim 8+)
:'<,'>g/^/exec 'normal! A' . line('.')

" Convertir a hex y volver (modo binario)
:%!xxd
:%!xxd -r

" Pegar sin que la autoindentación rompa el formato
:set paste      " (o usar registros del sistema con "+p)

" Repetir un cambio en varias coincidencias: cambia con cgn
/patrón<Enter>  cgn  nuevo  Esc   " luego . repite en la siguiente

" Volver al punto de inserción anterior y seguir escribiendo
gi

" Abrir el fichero bajo el cursor (gf) o seguir un enlace
gf

" Guardar la sesión actual (ventanas, buffers, folds)
:mksession ~/sesion.vim
" y restaurarla:  vim -S ~/sesion.vim
```

> [!tip] La potencia de Vim está en **componer**: `operador + contador + motion/objeto`. `d2w` (borra 2 palabras), `ci(` (cambia dentro de paréntesis), `>ap` (indenta el párrafo). Aprender los objetos de texto rinde más que memorizar comandos sueltos.

Ver también [[lazyvim]] para la distribución de Neovim, y `:help <tema>` dentro del editor para la documentación completa.
