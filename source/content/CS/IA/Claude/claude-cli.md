# Claude CLI — Guía Técnica

Claude Code (`claude`) es la interfaz de línea de comandos oficial de Anthropic para interactuar con modelos Claude directamente desde el terminal. Proporciona un entorno conversacional con capacidades de agente: lectura y edición de ficheros, ejecución de comandos shell y acceso a herramientas externas vía MCP. Soporta tanto sesiones interactivas como ejecución no interactiva para pipelines y scripting.

---

## Modos de invocación

| Forma | Descripción | Ejemplo |
|---|---|---|
| `claude` | Abre sesión interactiva sin prompt inicial | `claude` |
| `claude "prompt"` | Abre sesión interactiva con prompt inicial | `claude "explica este repo"` |
| `claude -p "prompt"` | Modo no interactivo: imprime respuesta y termina | `claude -p "resume este fichero"` |
| `echo "..." \| claude -p` | Lee prompt desde stdin en modo no interactivo | `cat error.log \| claude -p "qué falla aquí"` |

---

## Opciones generales

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--help`, `-h` | Muestra ayuda y lista de flags disponibles | `claude --help` |
| `--version` | Muestra la versión instalada del CLI | `claude --version` |
| `--verbose` | Activa logging detallado de operaciones internas | `claude --verbose` |
| `--debug` | Activa modo debug con trazas completas de red y herramientas | `claude --debug` |

---

## Modelo y sesión

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--model MODEL` | Especifica el modelo a utilizar | `claude --model claude-opus-4-7` |
| `--continue`, `-c` | Retoma la conversación más reciente | `claude -c` |
| `--resume SESSION_ID`, `-r SESSION_ID` | Retoma una conversación específica por ID | `claude -r abc123` |
| `--max-turns N` | Limita el número de turnos agénticos automáticos | `claude -p "refactoriza todo" --max-turns 10` |

**Modelos disponibles (mayo 2026):**

| ID de modelo | Nombre |
|---|---|
| `claude-opus-4-7` | Claude Opus 4.7 |
| `claude-sonnet-4-6` | Claude Sonnet 4.6 |
| `claude-haiku-4-5-20251001` | Claude Haiku 4.5 |

---

## Prompts y contexto

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--system-prompt PROMPT`, `-s PROMPT` | Reemplaza el system prompt por defecto | `claude -s "eres un revisor de código estricto"` |
| `--append-system-prompt PROMPT` | Añade texto al final del system prompt existente | `claude --append-system-prompt "responde siempre en inglés"` |
| `--add-dir DIR` | Añade un directorio adicional al contexto de trabajo | `claude --add-dir /opt/shared-libs` |

---

## Formato de salida

Aplica principalmente en modo no interactivo (`-p`).

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--output-format FORMAT` | Formato de la respuesta de salida | `claude -p "lista deps" --output-format json` |
| `--no-stream` | Desactiva el streaming; entrega la respuesta completa de una vez | `claude -p "analiza" --no-stream` |

**Valores de `--output-format`:**

| Valor | Descripción |
|---|---|
| `text` | Texto plano (valor por defecto) |
| `json` | JSON con el resultado completo de la conversación |
| `stream-json` | Secuencia de objetos JSON por línea (útil para pipelines) |

---

## Permisos y herramientas

| Opción | Descripción | Ejemplo |
|---|---|---|
| `--allowedTools TOOLS` | Lista de herramientas permitidas (separadas por coma) | `claude --allowedTools Bash,Read,Edit` |
| `--disallowedTools TOOLS` | Lista de herramientas bloqueadas (separadas por coma) | `claude --disallowedTools Bash` |
| `--dangerously-skip-permissions` | Omite todos los prompts de confirmación de permisos sin preguntar al usuario | `claude -p "ejecuta tests" --dangerously-skip-permissions` |

> **Advertencia de seguridad:** `--dangerously-skip-permissions` permite al modelo ejecutar cualquier acción sin confirmación. Úsalo solo en entornos aislados (CI, contenedores) con prompts de confianza verificada.

**Herramientas nativas disponibles para `--allowedTools` / `--disallowedTools`:**

| Nombre | Función |
|---|---|
| `Bash` | Ejecuta comandos shell |
| `Read` | Lee ficheros del sistema |
| `Edit` | Edita ficheros existentes |
| `Write` | Escribe/crea ficheros |
| `Grep` | Búsqueda de patrones en ficheros |
| `Glob` | Búsqueda de ficheros por patrón |
| `WebFetch` | Obtiene contenido de URLs |
| `WebSearch` | Realiza búsquedas web |
| `Agent` | Lanza subagentes |

---

## Comandos de barra (slash commands)

Disponibles dentro de la sesión interactiva. Se escriben directamente en el prompt.

### Sesión y navegación

| Comando | Descripción | Ejemplo |
|---|---|---|
| `/help` | Muestra lista de comandos disponibles | `/help` |
| `/exit`, `/quit` | Termina la sesión | `/exit` |
| `/clear` | Limpia el historial de conversación actual | `/clear` |
| `/compact [instrucciones]` | Compacta el contexto de conversación con resumen; acepta instrucciones opcionales para guiar el resumen | `/compact enfócate en los cambios de API` |
| `/cost` | Muestra el uso de tokens y coste estimado de la sesión | `/cost` |
| `/status` | Muestra estado del sistema, modelo activo y configuración | `/status` |

### Modelo y configuración

| Comando | Descripción | Ejemplo |
|---|---|---|
| `/model [MODEL_ID]` | Cambia el modelo activo en la sesión | `/model claude-opus-4-7` |
| `/config` | Abre la interfaz de configuración interactiva | `/config` |
| `/fast` | Activa/desactiva Fast Mode (Opus con salida acelerada) | `/fast` |
| `/vim` | Activa/desactiva keybindings de vim en el prompt | `/vim` |
| `/terminal-setup` | Configura integraciones con el terminal (fuentes, colores) | `/terminal-setup` |

### Proyecto y memoria

| Comando | Descripción | Ejemplo |
|---|---|---|
| `/init` | Genera un fichero `CLAUDE.md` con documentación del proyecto actual | `/init` |
| `/memory` | Abre los ficheros de memoria del proyecto para edición | `/memory` |

### Autenticación

| Comando | Descripción | Ejemplo |
|---|---|---|
| `/login` | Cambia de cuenta o API key activa | `/login` |
| `/logout` | Cierra sesión y elimina credenciales locales | `/logout` |

### Herramientas de código

| Comando | Descripción | Ejemplo |
|---|---|---|
| `/review` | Ejecuta revisión de código del diff actual | `/review` |
| `/pr_comments` | Muestra comentarios de la PR abierta en el branch actual | `/pr_comments` |
| `/doctor` | Diagnóstico del entorno: API key, conectividad, versión | `/doctor` |
| `/bug` | Abre el flujo para reportar un bug al equipo de Anthropic | `/bug` |

---

## Atajos interactivos

| Atajo / Prefijo | Descripción | Ejemplo |
|---|---|---|
| `! comando` | Ejecuta un comando shell directamente; la salida entra en el contexto de la conversación | `! git status` |
| `# texto` | Añade un comentario o nota a la conversación sin enviarlo al modelo | `# revisar esto después` |
| `@fichero` | Referencia un fichero del proyecto en el mensaje | `@src/main.py qué hace esta función` |
| `Ctrl+C` | Interrumpe la respuesta en curso | — |
| `Ctrl+D` | Cierra la sesión (equivalente a `/exit`) | — |
| Flecha arriba / abajo | Navega por el historial de prompts de la sesión | — |

---

## Configuración — `settings.json`

El fichero de configuración se encuentra en:
- **Global:** `~/.claude/settings.json`
- **Proyecto:** `.claude/settings.json` (en el directorio raíz del proyecto)

La configuración de proyecto tiene precedencia sobre la global.

### Opciones principales

| Clave | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| `model` | `string` | Modelo por defecto para nuevas sesiones | `"claude-sonnet-4-6"` |
| `theme` | `string` | Tema visual de la UI en terminal | `"dark"` / `"light"` / `"solarized-dark"` / `"solarized-light"` |
| `verbose` | `boolean` | Activa logging detallado por defecto | `true` |
| `permissions` | `object` | Reglas de permisos para herramientas | ver abajo |
| `env` | `object` | Variables de entorno inyectadas en cada sesión | `{"NODE_ENV": "development"}` |
| `hooks` | `object` | Comandos shell ejecutados en respuesta a eventos del ciclo de vida | ver abajo |

### `permissions`

Define qué herramientas se permiten o deniegan automáticamente sin prompt al usuario.

```json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Read",
      "Edit"
    ],
    "deny": [
      "Bash(rm *)"
    ]
  }
}
```

Los valores del array aceptan el nombre de la herramienta solo (`"Read"`) o con un patrón de argumento entre paréntesis (`"Bash(npm run *)"`) para control granular.

### `hooks`

Comandos shell ejecutados en eventos específicos del CLI. Útiles para automatizar flujos o enriquecer el contexto.

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'prompt enviado'"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/local/bin/audit-bash-use.sh"
          }
        ]
      }
    ]
  }
}
```

**Eventos disponibles:**

| Evento | Cuándo se dispara |
|---|---|
| `UserPromptSubmit` | Justo antes de enviar el prompt del usuario al modelo |
| `PreToolUse` | Antes de ejecutar cualquier herramienta |
| `PostToolUse` | Tras completar la ejecución de una herramienta |
| `Stop` | Cuando el modelo termina de responder |
| `Notification` | Cuando el CLI emite una notificación al usuario |

---

## Variables de entorno

| Variable | Descripción | Ejemplo |
|---|---|---|
| `ANTHROPIC_API_KEY` | API key de Anthropic. Requerida si no se ha hecho `/login` | `export ANTHROPIC_API_KEY=sk-ant-...` |
| `ANTHROPIC_BASE_URL` | URL base alternativa para la API (proxies, entornos privados) | `export ANTHROPIC_BASE_URL=https://proxy.internal/v1` |
| `CLAUDE_MODEL` | Modelo por defecto; sobreescribe `settings.json` | `export CLAUDE_MODEL=claude-opus-4-7` |
| `CLAUDE_CONFIG_DIR` | Ruta alternativa al directorio de configuración global | `export CLAUDE_CONFIG_DIR=/etc/claude` |
| `CLAUDE_DEBUG` | Activa modo debug equivalente a `--debug` | `export CLAUDE_DEBUG=1` |
| `NO_COLOR` | Desactiva toda la salida de color (estándar POSIX) | `export NO_COLOR=1` |
| `BASH_DEFAULT_TIMEOUT_MS` | Timeout en ms para comandos Bash ejecutados por el agente | `export BASH_DEFAULT_TIMEOUT_MS=30000` |
