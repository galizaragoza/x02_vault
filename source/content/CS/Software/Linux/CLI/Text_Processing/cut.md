cut extrae secciones de cada línea de un fichero o stdin: por campos delimitados, por posición de caracteres o por posición de bytes. Es la herramienta más rápida para extraer columnas de ficheros CSV/TSV o de salidas de comandos con formato fijo. No puede reordenar ni modificar los campos extraídos; para eso usar `awk`.

```
cut [opciones] [fichero...]
```

---

## Modos de selección

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-f <lista>` / `--fields=<lista>` | Selecciona campos (columnas delimitadas). El delimitador por defecto es el tabulador. | `cut -f1,3 /etc/passwd` |
| `-c <lista>` / `--characters=<lista>` | Selecciona por posición de carácter (basado en 1). | `cut -c1-10 fichero.txt` |
| `-b <lista>` / `--bytes=<lista>` | Selecciona por posición de byte. Difiere de `-c` en codificaciones multibyte. | `cut -b1-50 binario` |

---

## Delimitador

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-d <delim>` / `--delimiter=<delim>` | Define el delimitador de campos para `-f`. Solo un carácter. Por defecto: tabulador. | `cut -d: -f1 /etc/passwd` |
| `--output-delimiter=<cadena>` | Cadena a usar como separador en la salida entre campos seleccionados (puede ser multilínea). | `cut -d: -f1,7 --output-delimiter=' -> ' /etc/passwd` |

---

## Comportamiento con líneas sin delimitador

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-s` / `--only-delimited` | Suprime líneas que no contienen el delimitador (solo con `-f`). | `cut -d, -f2 -s datos.csv` |

**Selección con complemento:**

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `--complement` | Invierte la selección: extrae todo lo que NO está en la lista. | `cut -d: -f1 --complement /etc/passwd` |

---

## Sintaxis de lista

Las listas de campos/caracteres/bytes aceptan:

| Formato | Descripción |
|---------|-------------|
| `N` | El campo/posición N. |
| `N-M` | Del campo N al M (inclusive). |
| `N-` | Del campo N hasta el final de línea. |
| `-M` | Desde el inicio hasta el campo M. |
| `N,M,P` | Campos N, M y P (separados por coma). |
| Combinaciones | `1,3-5,7-` = campos 1, 3, 4, 5, 7 en adelante. |

Los campos en la salida siempre siguen el orden original, independientemente del orden de la lista.

---

## Casos de uso comunes

```bash
# Extraer primer campo de /etc/passwd (usuario)
cut -d: -f1 /etc/passwd

# Extraer usuario y shell (campos 1 y 7)
cut -d: -f1,7 /etc/passwd

# Extraer campo 2 de CSV
cut -d, -f2 datos.csv

# Primeros 10 caracteres de cada línea
cut -c1-10 fichero.txt

# Desde el carácter 5 hasta el final
cut -c5- fichero.txt

# Extraer todo excepto el primer campo
cut -d: -f1 --complement /etc/passwd

# En pipeline: obtener lista de usuarios
getent passwd | cut -d: -f1

# Solo líneas que tienen el delimitador
cut -d, -f2 -s datos.csv

# Extraer columnas de salida de ps
ps aux | cut -c1-10,65-

# Con output-delimiter personalizado
cut -d: -f1,3 --output-delimiter=' uid=' /etc/passwd
```
