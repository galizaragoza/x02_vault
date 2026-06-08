```
man (ID) commmand
```

| **Utilidad** | **Función**                                                                             | **Ejemplo de sintaxis** |
| ------------ | --------------------------------------------------------------------------------------- | ----------------------- |
| `mandb`      | Crea o actualiza las bases de datos de las páginas de manual.                           | `sudo mandb`            |
| `whatis`     | Busca en la base de datos de nombres de páginas y muestra descripciones cortas.         | `whatis rm`             |
| `apropos`    | Busca términos en los nombres y descripciones (útil si no sabes el nombre del comando). | `apropos firewall`      |
| `manpath`    | Determina la ruta de búsqueda de las páginas de manual.                                 | `manpath`               |
# ID

| **Sección** | **Categoría**          | **Descripción**                                                    |
| ----------- | ---------------------- | ------------------------------------------------------------------ |
| **1**       | Programas ejecutables  | Comandos de usuario estándar (ej: `ls`, `cat`).                    |
| **2**       | Llamadas al sistema    | Funciones proporcionadas por el kernel (ej: `open`, `read`).       |
| **3**       | Llamadas a bibliotecas | Funciones dentro de bibliotecas de C (ej: `printf`, `sleep`).      |
| **4**       | Archivos especiales    | Normalmente dispositivos encontrados en `/dev`.                    |
| **5**       | Formatos de archivos   | Documentación de archivos de configuración (ej: `/etc/passwd`).    |
| **6**       | Juegos                 | Juegos y salvapantallas de terminal.                               |
| **7**       | Miscelánea             | Paquetes de macros, convenciones, protocolos (ej: `man`, `utf-8`). |
| **8**       | Administración         | Comandos para superusuario/root (ej: `iptables`, `fdisk`).         |

# Params

| **Parámetro**           | **Función**                                                                       | **Ejemplo de sintaxis**  |
| ----------------------- | --------------------------------------------------------------------------------- | ------------------------ |
| `tldr`                  | Manual resumido                                                                   |                          |
| `[sección]`             | Especifica la sección exacta donde buscar.                                        | `man 5 passwd`           |
| `-a`, `--all`           | Muestra todas las páginas que coincidan con el nombre, una tras otra.             | `man -a intro`           |
| `-k`, `--apropos`       | Busca el término en las descripciones breves (equivalente a `apropos`).           | `man -k "network"`       |
| `-f`, `--whatis`        | Muestra una descripción breve de lo que hace el comando.                          | `man -f ls`              |
| `-l`, `--local-file`    | Abre un archivo de manual local (un archivo `.1` o `.gz`).                        | `man -l ./mi_archivo.1`  |
| `-w`, `--path`          | Muestra la ubicación física del archivo de la página de manual.                   | `man -w grep`            |
| `-W`, `--where`         | Similar a `-w`, pero solo imprime el nombre del archivo sin mensajes adicionales. | `man -W printf`          |
| `-K`, `--global-approx` | Busca una cadena de texto en **todas** las páginas de manual (lento).             | `man -K "error message"` |
| `-p`, `--preprocessor`  | Indica qué preprocesador usar antes de mostrar la página.                         | `man -p tbl ls`          |
| `-t`, `--troff`         | Prepara la página para ser impresa o convertida a PostScript.                     | `man -t bash > bash.ps`  |
| `-u`, `--update`        | Fuerza una actualización de la base de datos de búsqueda.                         | `man -u`                 |
| `-C`, `--config-file`   | Utiliza un archivo de configuración de `man` específico.                          | `man -C /ruta/man.conf`  |
| `-D`, `--default`       | Restablece el comportamiento de `man` a los valores por defecto.                  | `man -D`                 |
| `-L`, `--locale`        | Define el idioma en el que se desea leer el manual.                               | `man -L es ls`           |
| `-X`, `--gxditview`     | Muestra la página de manual en una ventana de interfaz gráfica (X11).             | `man -X ls`              |