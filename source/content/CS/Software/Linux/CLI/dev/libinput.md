# Utilidades
## libinput-list-devices

| Parámetro          | Función                       | Ejemplo de sintaxis               |
| ------------------ | ----------------------------- | --------------------------------- |
| `--help`           | Muestra ayuda de uso          | `libinput-list-devices --help`    |
| `--verbose` o `-v` | Muestra información detallada | `libinput-list-devices --verbose` |
| Sin parámetros     | Lista todos los dispositivos  | `libinput-list-devices`           |

## libinput-debug-events

| Parámetro           | Función                                       | Ejemplo de sintaxis                                |
| ------------------- | --------------------------------------------- | -------------------------------------------------- |
| `--help`            | Muestra ayuda de uso                          | `libinput-debug-events --help`                     |
| `--device`          | Filtra por dispositivo específico             | `libinput-debug-events --device=/dev/input/event2` |
| `--show-keycodes`   | Muestra códigos de tecla en lugar de símbolos | `libinput-debug-events --show-keycodes`            |
| `--verbose`         | Muestra información adicional                 | `libinput-debug-events --verbose`                  |
| `--show-timestamps` | Muestra marcas de tiempo                      | `libinput-debug-events --show-timestamps`          |
| `--grab`            | Captura dispositivo exclusivamente            | `libinput-debug-events --grab`                     |

## libinput-record

| Parámetro         | Función                           | Ejemplo de sintaxis                          |
| ----------------- | --------------------------------- | -------------------------------------------- |
| `--help`          | Muestra ayuda de uso              | `libinput-record --help`                     |
| `--output` o `-o` | Especifica archivo de salida      | `libinput-record --output=eventos.yml`       |
| `--device`        | Graba solo dispositivo específico | `libinput-record --device=/dev/input/event3` |
| `--all`           | Graba todos los dispositivos      | `libinput-record --all`                      |
| `--duration`      | Duración de grabación en segundos | `libinput-record --duration=10`              |

## libinput-replay

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`--help`|Muestra ayuda de uso|`libinput-replay --help`|
|`--input` o `-i`|Archivo de entrada a reproducir|`libinput-replay --input=eventos.yml`|
|`--speed`|Velocidad de reproducción (1.0 = normal)|`libinput-replay --speed=2.0`|
|`--loop`|Reproduce en bucle|`libinput-replay --loop`|
|`--disable-touch`|Deshabilita eventos táctiles|`libinput-replay --disable-touch`|

## libinput-analyze

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`--help`|Muestra ayuda de uso|`libinput-analyze --help`|
|`--help-all`|Muestra ayuda completa|`libinput-analyze --help-all`|
|`tap`|Analiza comportamiento de taps|`libinput-analyze tap`|
|`dwt`|Analiza disable-while-typing|`libinput-analyze dwt`|
|`touchpad-pressure`|Analiza sensibilidad de presión|`libinput-analyze touchpad-pressure`|
|`touch-size`|Analiza tamaño de toques|`libinput-analyze touch-size`|

## libinput-measure

|Parámetro|Función|Ejemplo de sintaxis|
|---|---|---|
|`--help`|Muestra ayuda de uso|`libinput-measure --help`|
|`touchpad-size`|Mide dimensiones del touchpad|`libinput-measure touchpad-size`|
|`touchpad-tap`|Mide precisión de taps|`libinput-measure touchpad-tap`|
|`touchpad-dwt`|Mide disable-while-typing|`libinput-measure touchpad-dwt`|
|`--device`|Especifica dispositivo|`libinput-measure --device=/dev/input/event2 touchpad-size`|