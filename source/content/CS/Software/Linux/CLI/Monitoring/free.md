free muestra el uso de memoria RAM y swap del sistema en tiempo real, leyendo de `/proc/meminfo`. Muestra memoria total, usada, libre, compartida, bufferes/caché y disponible. La columna `available` (introducida en versiones recientes) es la más relevante para estimar cuánta memoria puede usar una nueva aplicación sin necesitar swap.

```
free [opciones]
```

---

## Unidades de visualización

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-b` / `--bytes` | Muestra en bytes. | `free -b` |
| `-k` / `--kilo` | Muestra en kibibytes (por defecto). | `free -k` |
| `-m` / `--mega` | Muestra en mebibytes. | `free -m` |
| `-g` / `--giga` | Muestra en gibibytes. | `free -g` |
| `--tera` | Muestra en tebibytes. | `free --tera` |
| `-h` / `--human` | Formato legible automático (K/M/G/T). | `free -h` |
| `--si` | Usa potencias de 1000 en lugar de 1024 con `-h`. | `free -h --si` |

---

## Comportamiento y salida

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-s <segundos>` / `--seconds=<segundos>` | Actualiza la salida cada N segundos de forma continua. | `free -h -s 2` |
| `-c <N>` / `--count=<N>` | Muestra N veces y termina. Requiere `-s`. | `free -h -s 1 -c 5` |
| `-t` / `--total` | Añade una línea de totales (RAM + swap) al final. | `free -ht` |
| `-w` / `--wide` | Modo ancho: separa buffers y caché en columnas distintas. | `free -hw` |
| `-l` / `--lohi` | Muestra estadísticas de memoria baja y alta (relevante en sistemas 32-bit antiguos). | `free -l` |
| `-v` / `--verbose` | Muestra información adicional. | `free -v` |

---

## Casos de uso comunes

```bash
# Vista rápida en formato legible
free -h

# Con totales y columnas anchas
free -hw

# Monitoreo continuo cada 2 segundos
free -h -s 2

# 5 muestras cada segundo
free -h -s 1 -c 5

# Solo el disponible (en scripts)
free -m | awk '/^Mem:/ {print $7 " MB disponibles"}'

# Ver si el sistema está usando swap
free -h | grep -E "Mem|Swap"
```
