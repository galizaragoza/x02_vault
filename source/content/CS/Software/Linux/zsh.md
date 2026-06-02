#reference #Software

**zsh** (Z shell) = shell interactiva compatible con Bash, con mejor autocompletado, globbing avanzado, corrección de comandos y un ecosistema de plugins/temas (Oh My Zsh, zinit). Shell por defecto en macOS y muy usada en Kali.

# Configuración

`~/.zshrc` = fichero de configuración principal (alias, funciones, plugins, prompt).

```bash
chsh -s $(which zsh)        # poner zsh como shell por defecto
source ~/.zshrc            # recargar config
```

# Sintaxis útil (vs Bash)

| Característica | zsh |
|---------------|-----|
| Glob recursivo | `ls **/*.txt` (todos los .txt en subdirs) |
| Glob por tipo | `ls *(.)` ficheros, `*(/)` dirs, `*(*)` ejecutables |
| Modificadores | `${file:r}` sin extensión, `${file:t}` basename, `${file:h}` dirname |
| Historial compartido | `setopt SHARE_HISTORY` |
| Autocd | escribir un dir lo cambia (`setopt AUTO_CD`) |
| Corrección | `setopt CORRECT` sugiere typos |

# Frameworks y plugins

| Herramienta | Uso |
|-------------|-----|
| **Oh My Zsh** | Framework de config con temas y plugins. |
| **Powerlevel10k** | Tema de prompt rápido y configurable. |
| **zsh-autosuggestions** | Sugerencias en gris según el historial. |
| **zsh-syntax-highlighting** | Resalta comandos válidos/erróneos al escribir. |
| **fzf** | Búsqueda fuzzy de historial/ficheros (`Ctrl+R`). |

```bash
# Oh My Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Plugins en ~/.zshrc
plugins=(git sudo docker zsh-autosuggestions zsh-syntax-highlighting)
```

# Recursos
### [[Kali]]
### [Oh My Zsh](https://ohmyz.sh/) · [Powerlevel10k](https://github.com/romkatv/powerlevel10k)
### [Setup zsh](https://www.howtoforge.com/tutorial/how-to-setup-zsh-and-oh-my-zsh-on-linux/) · [Customizar ZSH](https://medium.com/technology-com/how-i-made-my-kali-linux-terminal-look-awesome-in-5-minutes-32a666f99301)
