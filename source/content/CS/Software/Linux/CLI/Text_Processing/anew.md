`anew` es una herramienta de utilidad para la línea de comandos diseñada para filtrar y añadir líneas a un archivo basándose en su existencia previa. Su propósito principal es procesar flujos de datos (stdin) y asegurar que solo las líneas que no están presentes en el archivo de destino sean añadidas o impresas. Es una herramienta fundamental en flujos de trabajo de automatización y _recon_ para evitar la duplicidad de datos sin necesidad de reordenar el archivo original.

---

## Operaciones de Flujo y Archivo

Esta categoría comprende las opciones fundamentales que dictan cómo `anew` interactúa con el sistema de archivos y el flujo de datos estándar.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`[file]`|Especifica el archivo de destino contra el cual se comparará la entrada. Si la línea no existe, se añade al archivo.|`cat subs.txt \| anew master_list.txt`|
|`-q`|Modo silencioso (quiet). No imprime las nuevas líneas añadidas a la salida estándar (stdout).|`cat nuevas_urls.txt \| anew -q urls_vistas.txt`|

---

## Comportamiento de Comparación y Lógica

Opciones que alteran la lógica de filtrado y cómo se procesan las líneas antes de ser evaluadas o escritas.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-d`|Modo "dry run". Imprime las líneas que se añadirían pero no modifica el archivo de destino.|`cat ips.txt \| anew -d master.txt`|
|`-t`|Trim. Elimina los espacios en blanco al inicio y al final de cada línea antes de realizar la comparación.|`echo " ejemplo.com " \| anew -t sitios.txt`|

---

## Técnicas de Procesamiento de Salida

Parámetros que controlan la visualización de los resultados y la interacción con otros procesos en el pipeline.

| **Flag / Parámetro** | **Descripción**                                                                                   | **Ejemplo Práctico**                      |
| -------------------- | ------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| Sin flag (Default)   | Por defecto, `anew` escribe las líneas únicas en el archivo y simultáneamente las envía a stdout. | `cat raw.txt \| anew clean.txt \| notify` |