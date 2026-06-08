`alias` es un *builtin* del shell que crea atajos de texto: una palabra que el shell expande a otro comando (con sus opciones) antes de ejecutarlo. La expansión solo ocurre como primera palabra de un comando simple y solo en shells interactivos por defecto. `unalias` elimina alias. Para que persistan entre sesiones deben declararse en los ficheros de inicio (`~/.bashrc`, `~/.zshrc`, `~/.bash_aliases`). `alias` no acepta argumentos posicionales en su definición; para eso se usan funciones de shell.

```
alias [-p] [nombre[=valor] ...]
unalias [-a] nombre ...
```

> La expansión de alias se desactiva si la palabra va entre comillas o con backslash (`\ls`), lo que permite invocar el comando original. Bash solo expande alias en shells interactivos salvo que se active `shopt -s expand_aliases`.

---

## alias (bash) — definición y listado

| Comando / Opción | Descripción | Ejemplo |
|------------------|-------------|---------|
| `alias` | Lista todos los alias definidos. | `alias` |
| `alias nombre` | Muestra la definición de un alias concreto. | `alias ll` |
| `alias -p` | Lista todos los alias en formato reutilizable (`alias x='...'`). | `alias -p` |
| `alias nombre='comando'` | Crea o redefine un alias. | `alias ll='ls -la'` |
| `alias a='c1' b='c2'` | Define varios alias en una sola llamada. | `alias gs='git status' gd='git diff'` |

---

## unalias — eliminación

| Comando / Opción | Descripción | Ejemplo |
|------------------|-------------|---------|
| `unalias nombre` | Elimina un alias concreto. | `unalias ll` |
| `unalias -a` | Elimina todos los alias de la sesión. | `unalias -a` |

---

## Extensiones de zsh

Zsh añade tipos de alias más allá del clásico de bash.

| Comando / Opción | Descripción | Ejemplo |
|------------------|-------------|---------|
| `alias -g nombre='valor'` | Alias **global**: se expande en cualquier posición de la línea, no solo al inicio. | `alias -g G='\| grep'` → `ls G txt` |
| `alias -s ext='programa'` | Alias de **sufijo**: ejecuta un programa al teclear un fichero con esa extensión. | `alias -s pdf=zathura` → `informe.pdf` |
| `alias -L` | Lista alias en formato reutilizable (equivalente a `-p` de bash). | `alias -L` |
| `alias -m 'patrón'` | Lista alias cuyo nombre casa un patrón glob. | `alias -m 'g*'` |
| `unalias -m 'patrón'` | Elimina alias por patrón glob. | `unalias -m 'g*'` |

---

## Control de la expansión

| Técnica | Descripción | Ejemplo |
|---------|-------------|---------|
| `\nombre` | Evita la expansión del alias y ejecuta el comando original. | `\ls` |
| `'nombre'` | Comillas también inhiben la expansión. | `'ls'` |
| `command nombre` | Salta alias y funciones, ejecuta el binario. | `command ls` |
| `\unalias` / `command unalias` | Quita el alias sin que un alias homónimo interfiera. | `command unalias rm` |
| `shopt -s expand_aliases` | (bash) Activa expansión de alias en scripts no interactivos. | `shopt -s expand_aliases` |
| Espacio final en el valor | Hace que el shell expanda también la palabra siguiente como alias. | `alias sudo='sudo '` |

---

## Persistencia

```bash
# Añadir alias permanentes (bash)
echo "alias ll='ls -lah'" >> ~/.bashrc
echo "alias gs='git status'" >> ~/.bashrc
source ~/.bashrc            # recargar sin reabrir terminal

# zsh
echo "alias ll='ls -lah'" >> ~/.zshrc && source ~/.zshrc

# Convención: fichero dedicado cargado desde ~/.bashrc
echo "alias ..='cd ..'" >> ~/.bash_aliases
```

---

## Casos de uso comunes

```bash
# Atajos típicos
alias ll='ls -lah'
alias la='ls -A'
alias ..='cd ..'
alias ...='cd ../..'
alias grep='grep --color=auto'

# Hacer interactivos los comandos destructivos
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Que sudo respete los alias del usuario (nota el espacio final)
alias sudo='sudo '

# Ejecutar el comando real ignorando el alias
\rm fichero      # rm sin -i
command grep ... # grep sin --color

# zsh: pipe a grep desde cualquier punto
alias -g G='| grep'
ps aux G ssh
```

Relacionado: [[type]], [[Bash_cheatsheet]], [[Spec_vars]].
