Detecta el formato de un hash $X$, ideal para combinar con [[John The Ripper]] o [[hashcat]] para conocer el tipo de hash con el que se está trabajando para poder crackearlo.
Detecta +640 tipos de hash y está al día con algoritmos modernos.

| Parámetro        | Función                                                  |
| ---------------- | -------------------------------------------------------- |
| `<hash>`         | Cadena de hash a identificar (obligatorio).              |
| `-e, --extended` | Lista todos los algoritmos posibles, incluyendo con sal. |
| `--short`        | Formato corto: omite referencias a Hashcat/John.         |
| `--hashcat-only` | Muestra solo referencias de Hashcat.                     |
| `--john-only`    | Muestra solo referencias de John the Ripper.             |
| `--no-color`     | Desactiva salida coloreada.                              |
| `--debug`        | Muestra argumentos internos.                             |
| `-h, --help`     | Muestra ayuda.                                           |
| `--version`      | Muestra versión.                                         |