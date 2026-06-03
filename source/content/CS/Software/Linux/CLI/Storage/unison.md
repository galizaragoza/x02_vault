unison es una herramienta de sincronización de ficheros **bidireccional**: mantiene dos réplicas (locales o remotas vía SSH) en estado idéntico, propagando en ambos sentidos los cambios hechos desde la última sincronización. A diferencia de `rsync` (unidireccional, espejo), unison detecta cambios en ambos lados y solo marca como conflicto los ficheros modificados en los dos a la vez. Guarda un archivo de estado por réplica en `~/.unison/` para distinguir creaciones, modificaciones y borrados entre ejecuciones.

```
unison [opciones] raíz1 raíz2 [-path subruta]...
unison perfil [opciones]
```

---

## Raíces (roots) y transporte

| Forma de raíz | Descripción | Ejemplo |
|---------------|-------------|---------|
| Ruta local | Directorio del sistema de ficheros local. | `/home/user/docs` |
| `ssh://usuario@host//ruta` | Réplica remota por SSH (doble `/` para ruta absoluta). | `ssh://ana@srv//srv/data` |
| `socket://host:puerto/ruta` | Conexión a un servidor unison en modo socket. | `socket://host:7777/data` |
| `-root <raíz>` | Especifica una raíz (repetible; alternativa a los argumentos posicionales). | `unison -root /a -root ssh://h//b` |
| `-sshargs <args>` | Argumentos extra para `ssh`. | `unison r1 r2 -sshargs "-p 2222"` |
| `-servercmd <ruta>` | Ruta al binario `unison` en el host remoto. | `-servercmd /usr/bin/unison` |

---

## Modos de ejecución

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-batch` | Modo desatendido: aplica las acciones por defecto sin preguntar (los conflictos se omiten). | `unison perfil -batch` |
| `-auto` | Acepta automáticamente la acción por defecto en cambios no conflictivos; pregunta solo en conflictos. | `unison r1 r2 -auto` |
| `-ui <text\|graphic>` | Selecciona la interfaz de texto o gráfica (GTK). | `unison perfil -ui text` |
| `-repeat <intervalo>` | Repite la sincronización cada N segundos. | `unison perfil -repeat 60` |
| `-repeat watch` / `-watch` | Sincroniza continuamente vigilando cambios del sistema de ficheros (requiere `unison-fsmonitor`). | `unison perfil -repeat watch` |
| `-silent` | Suprime toda la salida salvo errores (implica `-batch`). | `unison perfil -silent` |
| `-terse` | Salida reducida. | `unison perfil -terse` |
| `-testserver` | Comprueba la conexión con la réplica remota y termina. | `unison r1 ssh://h//b -testserver` |
| `-contactquietly` | Omite el mensaje de "contactando servidor". | `unison perfil -contactquietly` |

---

## Resolución de conflictos y dirección

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-prefer <raíz>` | En un conflicto, conserva la versión de esa réplica. | `-prefer /home/user/docs` |
| `-prefer newer` | En conflicto, conserva el fichero más reciente (requiere `-times`). | `-prefer newer` |
| `-force <raíz>` | Hace de esa réplica la "maestra": propaga sus cambios e ignora los de la otra (sincronización casi unidireccional). | `-force ssh://h//b` |
| `-force newer` | Propaga siempre desde el fichero más nuevo. | `-force newer` |
| `-copyonconflict` | Ante un conflicto, copia ambas versiones en lugar de marcar y omitir. | `-copyonconflict` |
| `-merge <patrón -> cmd>` | Define un comando externo para fusionar ficheros en conflicto. | `-merge 'Name *.txt -> diff3 %1 %2 %3'` |
| `-confirmbigdel` | Pide confirmación si se va a borrar una réplica entera (por defecto activo). | `-confirmbigdel` |
| `-confirmmerge` | Pide confirmación antes de aplicar una fusión. | `-confirmmerge` |

---

## Filtros: qué sincronizar

| Opción                   | Descripción                                                                | Ejemplo                      |
| ------------------------ | -------------------------------------------------------------------------- | ---------------------------- |
| `-path <subruta>`        | Sincroniza solo esa subruta dentro de las raíces (repetible).              | `-path proyecto/src`         |
| `-ignore <patrón>`       | Excluye rutas que casen el patrón (`Name`, `Path` o `Regex`).              | `-ignore 'Name *.tmp'`       |
| `-ignorenot <patrón>`    | Sincroniza **solo** lo que case (inverso de `-ignore`).                    | `-ignorenot 'Name *.md'`     |
| `-follow <patrón>`       | Sigue los enlaces simbólicos que casen (sincroniza el contenido apuntado). | `-follow 'Path link'`        |
| `-immutable <patrón>`    | Marca rutas como inmutables: se crean pero nunca se actualizan ni borran.  | `-immutable 'Path cache'`    |
| `-immutablenot <patrón>` | Inverso de `-immutable`.                                                   | `-immutablenot 'Name *.log'` |

> Tipos de patrón: `Name x` (casa el nombre base con glob), `Path a/b/c` (ruta relativa exacta a la raíz), `Regex re` (expresión regular sobre la ruta).

---

## Restricción de operaciones (seguridad)

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-nodeletion <raíz>` | Impide que unison borre ficheros en esa réplica. | `-nodeletion /home/user/docs` |
| `-noupdate <raíz>` | Impide que se actualicen ficheros existentes en esa réplica. | `-noupdate ssh://h//b` |
| `-nocreation <raíz>` | Impide que se creen ficheros nuevos en esa réplica. | `-nocreation /backup` |

---

## Preservación de metadatos

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-times` | Sincroniza las fechas de modificación. | `-times` |
| `-perms <máscara>` | Bits de permiso a sincronizar (máscara octal; `0` desactiva). | `-perms 0o1777` |
| `-owner` | Sincroniza el propietario (UID). | `-owner` |
| `-group` | Sincroniza el grupo (GID). | `-group` |
| `-numericids` | Usa IDs numéricos de usuario/grupo en lugar de nombres. | `-numericids` |
| `-fat` | Preajustes para destinos FAT (sin permisos, sin symlinks, etc.). | `-fat` |
| `-fastcheck <true\|false\|default>` | Usa tamaño+fecha para detectar cambios (rápido) en vez de checksum. | `-fastcheck true` |

---

## Backups y registro

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-backup <patrón>` | Guarda copias de los ficheros antes de sobrescribir/borrar. | `-backup 'Name *'` |
| `-backupdir <dir>` | Directorio donde almacenar los backups. | `-backupdir ~/.unison/backups` |
| `-maxbackups <n>` | Número máximo de versiones de backup por fichero. | `-maxbackups 5` |
| `-log` | Activa el registro a fichero (por defecto a `~/.unison/unison.log`). | `-log` |
| `-logfile <ruta>` | Fichero de log alternativo. | `-logfile /var/log/unison.log` |
| `-debug <módulo>` | Información de depuración (`all`, `verbose`, o un módulo). | `-debug verbose` |

---

## Información y mantenimiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-version` | Muestra la versión. | `unison -version` |
| `-help` / `-?` | Muestra la ayuda. | `unison -help` |
| `-doc <tema>` | Muestra una sección de la documentación (`topics` lista los temas). | `unison -doc tutorial` |
| `-ignorearchives` | Ignora los archivos de estado y vuelve a escanear desde cero. | `unison perfil -ignorearchives` |
| `-killserver` | Termina el proceso servidor remoto al acabar. | `-killserver` |
| `-retry <n>` | Reintenta N veces ante fallos de propagación. | `-retry 3` |

---

## Perfiles (`~/.unison/<perfil>.prf`)

Cada opción de línea de comandos tiene su equivalente como directiva en un fichero de perfil. Invocar `unison <perfil>` carga `~/.unison/<perfil>.prf`.

```ini
# ~/.unison/docs.prf
root = /home/user/docs
root = ssh://ana@srv//srv/docs

path = proyecto
ignore = Name *.tmp
ignore = Name .git

times = true
perms = 0
batch = true
prefer = newer
backup = Name *
maxbackups = 3
```

---

## Casos de uso comunes

```bash
# Sincronización interactiva entre dos directorios locales
unison /home/user/docs /mnt/backup/docs

# Sincronizar con un host remoto por SSH
unison /home/user/docs ssh://ana@srv//srv/docs

# Ejecución desatendida desde un perfil (cron)
unison docs -batch

# Probar la conexión remota antes de sincronizar
unison /a ssh://h//b -testserver

# Sincronización continua vigilando cambios
unison docs -repeat watch

# Forzar una réplica como maestra (recuperar tras desastre)
unison /a ssh://h//b -force /a -batch

# Excluir directorios pesados
unison /a /b -ignore 'Name node_modules' -ignore 'Name .cache'
```
