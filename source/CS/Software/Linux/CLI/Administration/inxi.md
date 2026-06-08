`inxi` es una herramienta de información de sistema "todo en uno". Es famosa por ser extremadamente detallada y por su capacidad para detectar componentes de hardware, controladores, versiones de kernel y estados de batería.

| **Parámetro** | **Función**                                                        | **Ejemplo de Sintaxis** |
| ------------- | ------------------------------------------------------------------ | ----------------------- |
| `-F`          | Genera un reporte completo y detallado (Full).                     | `inxi -F`               |
| `-G`          | Muestra información específica de la tarjeta gráfica y monitores.  | `inxi -G`               |
| `-N`          | Muestra información de las interfaces de red y sus drivers.        | `inxi -N`               |
| `-D`          | Detalla los discos duros, incluyendo modelo y capacidad.           | `inxi -D`               |
| `-m`          | Proporciona información detallada sobre la memoria RAM (slots).    | `inxi -m`               |
| `-t`          | Muestra los procesos que más recursos consumen (`c` CPU, `m` RAM). | `inxi -t cm`            |