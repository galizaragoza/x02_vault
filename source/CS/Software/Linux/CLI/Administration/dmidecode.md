`dmidecode` es una herramienta que vuelca el contenido de la tabla DMI (Desktop Management Interface), también conocida como SMBIOS. Permite conocer detalles físicos del hardware (número de slots de RAM, versión de BIOS, fabricante de la placa base) sin necesidad de abrir el equipo.

| **Parámetro**        | **Función**                                                                                 | **Ejemplo de Sintaxis**          |
| -------------------- | ------------------------------------------------------------------------------------------- | -------------------------------- |
| `-t [tipo]`          | Filtra por tipo de componente (bios, system, baseboard, chassis, processor, memory, cache). | `dmidecode -t memory`            |
| `-s [keyword]`       | Extrae una cadena de texto específica (system-serial-number, bios-version, etc).            | `dmidecode -s bios-version`      |
| `-u`                 | No decodifica las entradas, muestra los datos en bruto (hexadecimal).                       | `dmidecode -u`                   |
| `--dump-bin [file]`  | Vuelca los datos DMI en un archivo binario.                                                 | `dmidecode --dump-bin data.bin`  |
| `--from-dump [file]` | Lee los datos DMI desde un archivo binario previamente volcado.                             | `dmidecode --from-dump data.bin` |