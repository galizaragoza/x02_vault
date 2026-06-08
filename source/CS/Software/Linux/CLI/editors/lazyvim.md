LazyVim es una distribución (preconfiguración) de Neovim creada por *folke*, construida sobre el gestor de plugins **lazy.nvim**. Proporciona un conjunto opinado de plugins, ajustes y atajos listos para usar, manteniéndose totalmente personalizable mediante ficheros en `~/.config/nvim/lua/`. Integra **which-key** (menús emergentes que muestran los atajos disponibles), LSP, Treesitter, Telescope/fzf-lua, Neo-tree, Lazygit y flash.nvim. Esta guía recopila los atajos por defecto; la tecla líder (`<leader>`) es **`Espacio`** y la local (`<localleader>`) es `\`.

```
nvim                      # abre LazyVim
:Lazy                     # gestor de plugins
:LazyExtras               # activa/desactiva módulos extra (langs, dap, etc.)
:LazyHealth               # diagnóstico de la instalación
```

> [!note] Pulsa `<Espacio>` y espera: which-key muestra todos los grupos de atajos disponibles. Es la mejor forma de descubrir comandos sin memorizarlos.

---

## General y gestión

| Atajo | Acción |
|-------|--------|
| `<C-s>` | Guarda el fichero (normal e inserción). |
| `<leader>qq` | Cierra todo (quit all). |
| `<leader>l` | Abre **Lazy** (gestor de plugins). |
| `<leader>fn` | Nuevo fichero. |
| `<Esc>` | Limpia el resaltado de búsqueda. |
| `<leader>ur` | Redibuja / limpia búsqueda y actualiza diff. |
| `<leader>K` | Muestra ayuda de la palabra (keywordprg). |
| `gco` / `gcO` | Añade comentario debajo / encima. |
| `gcc` / `gc` (visual) | Comenta línea / selección (mini.comment). |

---

## Movimiento y edición (mejoras sobre Vim)

| Atajo | Acción |
|-------|--------|
| `j` / `k` | Bajan/suben respetando líneas con wrap (`gj`/`gk`). |
| `<A-j>` / `<A-k>` | Mueve la línea (o selección visual) abajo / arriba. |
| `<` / `>` (visual) | Indenta manteniendo la selección. |
| `n` / `N` | Siguiente / anterior búsqueda, centrada en pantalla. |
| `gg` / `G` | Inicio / fin del fichero (estándar Vim). |

---

## Ventanas (windows)

| Atajo | Acción |
|-------|--------|
| `<C-h>` `<C-j>` `<C-k>` `<C-l>` | Mueve el foco entre ventanas (compatible con tmux). |
| `<C-Up>` / `<C-Down>` | Aumenta / reduce la altura. |
| `<C-Left>` / `<C-Right>` | Aumenta / reduce la anchura. |
| `<leader>w-` / `<leader>-` | Divide la ventana horizontalmente. |
| `<leader>w\|` / `<leader>\|` | Divide la ventana verticalmente. |
| `<leader>wd` | Cierra la ventana. |
| `<leader>ww` | Va a la otra ventana. |
| `<leader>wm` | Modo zen / maximizar (toggle). |

---

## Buffers

| Atajo | Acción |
|-------|--------|
| `<S-h>` / `[b` | Buffer anterior. |
| `<S-l>` / `]b` | Buffer siguiente. |
| `` <leader>` `` / `<leader>bb` | Alterna con el último buffer. |
| `<leader>bd` | Cierra el buffer (conserva la ventana). |
| `<leader>bD` | Cierra buffer y ventana. |
| `<leader>bo` | Cierra los demás buffers. |
| `<leader>bp` | Fija (pin) el buffer. |
| `<leader>bP` | Cierra los buffers no fijados. |

---

## Pestañas (tabs)

| Atajo | Acción |
|-------|--------|
| `<leader><tab><tab>` | Nueva pestaña. |
| `<leader><tab>]` / `<leader><tab>[` | Pestaña siguiente / anterior. |
| `<leader><tab>f` / `<leader><tab>l` | Primera / última pestaña. |
| `<leader><tab>d` | Cierra la pestaña. |

---

## Explorador de ficheros (Neo-tree)

| Atajo | Acción |
|-------|--------|
| `<leader>e` | Explorador en el directorio raíz del proyecto. |
| `<leader>E` | Explorador en el directorio de trabajo (cwd). |
| `<leader>fe` / `<leader>fE` | Igual que `<leader>e` / `<leader>E`. |
| `<leader>ge` | Explorador de cambios de Git. |
| `<leader>be` | Explorador de buffers. |

Dentro de Neo-tree: `a` añade, `d` borra, `r` renombra, `c` copia, `m` mueve, `H` muestra ocultos, `P` previsualiza, `<CR>` abre, `S`/`s` abre en split horizontal/vertical, `t` en pestaña, `?` ayuda.

---

## Búsqueda de ficheros y texto (Telescope / fzf-lua)

| Atajo | Acción |
|-------|--------|
| `<leader><space>` / `<leader>ff` | Busca ficheros (raíz del proyecto). |
| `<leader>fF` | Busca ficheros (cwd). |
| `<leader>fr` / `<leader>fR` | Ficheros recientes (raíz / cwd). |
| `<leader>fb` | Lista de buffers. |
| `<leader>fc` | Busca un fichero de configuración. |
| `<leader>,` | Cambia de buffer. |
| `<leader>/` / `<leader>sg` | **Grep** en vivo (raíz del proyecto). |
| `<leader>sG` | Grep (cwd). |
| `<leader>sw` | Busca la palabra bajo el cursor (o selección visual). |
| `<leader>sb` | Busca dentro del buffer actual (fuzzy). |
| `<leader>sr` | Buscar y reemplazar en ficheros (Grug-far). |
| `<leader>:` / `<leader>sc` | Historial de comandos. |
| `<leader>sC` | Lista de comandos. |
| `<leader>sh` | Páginas de ayuda. |
| `<leader>sk` | Lista de atajos de teclado. |
| `<leader>sm` | Salta a una marca. |
| `<leader>so` | Opciones de Vim. |
| `<leader>sd` / `<leader>sD` | Diagnósticos del documento / workspace. |
| `<leader>ss` / `<leader>sS` | Símbolos LSP (documento / workspace). |
| `<leader>sR` | Reabre la última búsqueda (resume). |
| `<leader>uC` | Cambia de colorscheme con previsualización. |

---

## LSP: navegación de código

| Atajo | Acción |
|-------|--------|
| `gd` | Ir a la definición. |
| `gD` | Ir a la declaración. |
| `gr` | Referencias. |
| `gI` | Ir a la implementación. |
| `gy` | Ir a la definición del tipo. |
| `K` | Documentación flotante (hover). |
| `gK` | Ayuda de firma (signature help). |
| `<C-k>` (inserción) | Ayuda de firma. |
| `]]` / `[[` | Siguiente / anterior referencia del símbolo. |

---

## LSP: acciones de código (`<leader>c`)

| Atajo | Acción |
|-------|--------|
| `<leader>ca` | Acción de código (code action). |
| `<leader>cr` | Renombra el símbolo. |
| `<leader>cR` | Renombra el fichero (vía LSP). |
| `<leader>cf` | Formatea el fichero o la selección. |
| `<leader>cd` | Diagnóstico de la línea. |
| `<leader>cc` / `<leader>cC` | Ejecuta / refresca codelens. |
| `<leader>cl` | Información del LSP (`:LspInfo`). |
| `<leader>cm` | Abre **Mason** (gestor de servidores LSP/herramientas). |
| `<leader>cs` | Símbolos del documento (outline). |

---

## Diagnósticos, quickfix y Trouble

| Atajo | Acción |
|-------|--------|
| `]d` / `[d` | Siguiente / anterior diagnóstico. |
| `]e` / `[e` | Siguiente / anterior error. |
| `]w` / `[w` | Siguiente / anterior advertencia. |
| `]q` / `[q` | Siguiente / anterior entrada de quickfix. |
| `<leader>xx` | Diagnósticos (Trouble). |
| `<leader>xX` | Diagnósticos del buffer (Trouble). |
| `<leader>xL` | Lista de localización (Trouble). |
| `<leader>xQ` | Lista de quickfix (Trouble). |
| `<leader>cs` | Símbolos (Trouble). |
| `<leader>cS` | Definiciones / referencias LSP (Trouble). |

---

## Git

| Atajo | Acción |
|-------|--------|
| `<leader>gg` | **Lazygit** (raíz del proyecto). |
| `<leader>gG` | Lazygit (cwd). |
| `<leader>gf` | Historial Lazygit del fichero actual. |
| `<leader>gl` / `<leader>gL` | Log de Lazygit (raíz / cwd). |
| `<leader>gb` | Blame de la línea. |
| `<leader>gB` | Abre la línea/fichero en el navegador (git browse). |
| `]h` / `[h` | Siguiente / anterior hunk (gitsigns). |
| `<leader>ghs` / `<leader>ghr` | *Stage* / *reset* del hunk. |
| `<leader>ghS` / `<leader>ghR` | Stage / reset del buffer entero. |
| `<leader>ghu` | Deshace el stage del hunk. |
| `<leader>ghp` | Previsualiza el hunk. |
| `<leader>ghb` | Blame completo de la línea. |
| `<leader>ghd` / `<leader>ghD` | Diff del fichero / diff contra `~`. |

---

## Toggles de interfaz (`<leader>u`)

| Atajo | Acción |
|-------|--------|
| `<leader>uf` / `<leader>uF` | Auto-formato global / del buffer. |
| `<leader>us` | Corrección ortográfica (spell). |
| `<leader>uw` | Ajuste de línea (wrap). |
| `<leader>ul` / `<leader>uL` | Número de línea / número relativo. |
| `<leader>ud` | Diagnósticos. |
| `<leader>uc` | Conceal (ocultar sintaxis). |
| `<leader>uh` | Inlay hints (LSP). |
| `<leader>uT` | Resaltado de Treesitter. |
| `<leader>ub` | Fondo claro / oscuro. |
| `<leader>un` | Descarta notificaciones. |
| `<leader>uC` | Selector de colorscheme. |

---

## Terminal

| Atajo | Acción |
|-------|--------|
| `<C-/>` / `<leader>ft` | Abre/cierra terminal (raíz del proyecto). |
| `<leader>fT` | Terminal en el cwd. |
| `<C-/>` (en terminal) | Oculta el terminal. |
| `<Esc><Esc>` (en terminal) | Pasa a modo normal del terminal. |
| `<C-h/j/k/l>` (en terminal) | Cambia de ventana. |

---

## Movimientos rápidos (flash.nvim)

| Atajo | Acción |
|-------|--------|
| `s` | Salto rápido (Flash jump) a cualquier punto visible. |
| `S` | Salto por nodos de Treesitter. |
| `r` (operador) | Flash remoto (operar sobre texto lejano). |
| `R` (visual/op) | Búsqueda por Treesitter. |
| `<C-s>` (modo comando) | Activa/desactiva Flash en la búsqueda `/`. |

---

## Autocompletado (blink.cmp / nvim-cmp)

| Atajo | Acción |
|-------|--------|
| `<C-Space>` | Dispara el completado / muestra documentación. |
| `<CR>` / `<C-y>` | Confirma la selección. |
| `<Tab>` / `<S-Tab>` | Siguiente / anterior elemento (y saltos de snippet). |
| `<C-n>` / `<C-p>` | Siguiente / anterior elemento. |
| `<C-e>` | Cierra el menú de completado. |
| `<C-b>` / `<C-f>` | Desplaza la documentación arriba / abajo. |

---

## Personalización

| Fichero | Propósito |
|---------|-----------|
| `~/.config/nvim/lua/config/options.lua` | Sobrescribe opciones de Vim (`vim.opt`). |
| `~/.config/nvim/lua/config/keymaps.lua` | Atajos propios (se añaden a los por defecto). |
| `~/.config/nvim/lua/config/autocmds.lua` | Autocomandos. |
| `~/.config/nvim/lua/config/lazy.lua` | Arranque de lazy.nvim y los *extras*. |
| `~/.config/nvim/lua/plugins/*.lua` | Añade o modifica plugins (especificaciones de lazy.nvim). |

```vim
" Sincronizar/actualizar plugins
:Lazy sync
" Activar módulos extra (lenguajes, DAP, test runners, etc.)
:LazyExtras
" Comprobar salud y dependencias
:LazyHealth
```

> [!tip] LazyVim hereda toda la gramática de Vim (operadores, motions, objetos de texto). Ver [[vim]] para esa base; estos atajos son la capa que LazyVim añade encima.

Documentación completa de atajos: <https://www.lazyvim.org/keymaps>
