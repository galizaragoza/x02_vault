`make` es una herramienta que automatiza el proceso de compilación de programas. Lee un archivo llamado `Makefile` que contiene reglas que definen cómo construir objetivos (targets) a partir de dependencias. Make determina automáticamente qué partes de un programa necesitan ser recompiladas y ejecuta los comandos necesarios.

| Parámetro       | Función                                        | Ejemplo de sintaxis      |
| --------------- | ---------------------------------------------- | ------------------------ |
| `-f`            | Especifica archivo Makefile alternativo        | `make -f Makefile.debug` |
| `-C`            | Cambia de directorio antes de ejecutar         | `make -C src/`           |
| `-j`            | Ejecuta trabajos en paralelo (N jobs)          | `make -j4`               |
| `-n`            | Modo "dry run" (muestra pero no ejecuta)       | `make -n all`            |
| `-s`            | Modo silencioso (no muestra comandos)          | `make -s clean`          |
| `-k`            | Continúa aunque haya errores                   | `make -k`                |
| `-B`            | Fuerza recompilación completa                  | `make -B`                |
| `--always-make` | Trata todos los objetivos como desactualizados | `make --always-make`     |
| `-r`            | Deshabilita reglas implícitas incorporadas     | `make -r`                |
| `-R`            | Deshabilita variables incorporadas             | `make -R`                |
| `-d`            | Muestra información de depuración              | `make -d`                |
| `--debug`       | Nivel de depuración (v, vv, vvv)               | `make --debug=v`         |
| `-e`            | Da prioridad a variables de entorno            | `make -e`                |
| `-i`            | Ignora errores (equivalente a .IGNORE)         | `make -i`                |
## Comandos
| Comando   | Función                               | Ejemplo en Makefile                      |
| --------- | ------------------------------------- | ---------------------------------------- |
| `all`     | Target por defecto para compilar todo | `all: programa`                          |
| `clean`   | Elimina archivos generados            | `clean: rm -f *.o programa`              |
| `install` | Instala el programa                   | `install: cp programa /usr/local/bin`    |
| `test`    | Ejecuta tests                         | `test: ./programa --test`                |
| `dist`    | Crea distribución                     | `dist: tar -czf programa.tar.gz *.c *.h` |

## Variables especiales
| Variable | Función                                    | Ejemplo                                 |
| -------- | ------------------------------------------ | --------------------------------------- |
| `$@`     | Nombre del objetivo                        | `programa: $(OBJS) $(CC) -o $@ $(OBJS)` |
| `$<`     | Primera dependencia                        | `.c.o: $(CC) -c $<`                     |
| `$^`     | Todas las dependencias                     | `programa: $(OBJS) $(CC) -o $@ $^`      |
| `$?`     | Dependencias más recientes que el objetivo | `$(CC) $(CFLAGS) $? -o $@`              |
| `$*`     | Stem del patrón de regla                   | `%.o: %.c $(CC) -c $*.c`                |
|          |                                            |                                         |


# Makefile template
```c
CC = clang
CFLAGS = -Wall -O2 -g
LDFLAGS = 
SRCS = main.c utils.c math.c
OBJS = $(SRCS:.c=.o)
TARGET = programa

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $@ $(OBJS) $(LDFLAGS)

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS) $(TARGET)

install: $(TARGET)
	cp $(TARGET) /usr/local/bin

.PHONY: all clean install
```