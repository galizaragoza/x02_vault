`hwinfo` es una herramienta de introspección de hardware que sondea los dispositivos presentes en el sistema y genera un informe detallado. Se utiliza frecuentemente para extraer datos que otras herramientas omiten, como detalles de la BIOS, particiones y monitores.1

| **Parámetro**  | **Función**                                                                 | **Ejemplo de Sintaxis**   |
| -------------- | --------------------------------------------------------------------------- | ------------------------- |
| `--short`      | Muestra un resumen técnico omitiendo detalles extensos.                     | `hwinfo --short`          |
| `--[item]`     | Muestra información específica (cpu, disk, network, memory, pci, usb, etc). | `hwinfo --cpu`            |
| `--all`        | Intenta sondear todo el hardware conocido (salida muy extensa).             | `hwinfo --all`            |
| `--log [file]` | Escribe el informe directamente en un archivo de log.                       | `hwinfo --log report.txt` |
| `--map`        | Muestra un mapa de dispositivos detectados y sus controladores.             | `hwinfo --map`            |
| `--version`    | Muestra la versión instalada de la herramienta.                             | `hwinfo --version`        |