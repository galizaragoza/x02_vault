# olevba (macros)
Es la herramienta más utilizada para detectar código malicioso.

| **Parámetro** | **Función**                                                       | **Ejemplo de Sintaxis**            |
| ------------- | ----------------------------------------------------------------- | ---------------------------------- |
| `-a`          | Ejecuta un análisis de las macros y muestra keywords sospechosas. | `olevba -a "documento.doc"`        |
| `-c`          | Extrae el código fuente VBA completo sin analizarlo.              | `olevba -c "archivo.xls"`          |
| `-r`          | Escanea directorios de forma recursiva.                           | `olevba -r ./muestras_malware/`    |
| `--decode`    | Intenta decodificar cadenas ofuscadas (Hex, Base64, etc.).        | `olevba --decode "muestra.doc"`    |
| `--reveal`    | Desofusca el código y lo muestra "limpio".                        | `olevba --reveal "virus.doc"`      |
| `-z`          | Permite analizar archivos dentro de un ZIP (usar con contraseña). | `olevba -z "infected" malware.zip` |

# oleid
Utilidad rápida para determinar si un archivo merece un análisis profundo.

|**Parámetro**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`-v`|Muestra información detallada sobre el análisis.|`oleid -v "factura.doc"`|
|`-p`|Especifica una contraseña si el archivo está cifrado.|`oleid -p "1234" "protegido.doc"`|
#  oleobj / rftobj
Se usan para extraer archivos que el atacante ha "embebido" dentro del documento original.

| **Parámetro** | **Función**                                                        | **Ejemplo de Sintaxis**                 |
| ------------- | ------------------------------------------------------------------ | --------------------------------------- |
| `-d` `DIR`    | Especifica el directorio donde se guardarán los objetos extraídos. | `oleobj -d ./salida/ "doc_con_exe.doc"` |
| `-i`          | Muestra información sobre los objetos sin extraerlos.              | `rtfobj -i "archivo.rtf"`               |
| `-s`          | Guarda los objetos extraídos con nombres secuenciales.             | `oleobj -s "muestra.doc"`               |

# mraptor
Evalúa si una macro es sospechosa basándose en tres criterios: **A** (Auto-ejecución), **W** (Escritura en disco), **N** (Conexión a red).

| **Parámetro** | **Función**                                             | **Ejemplo de Sintaxis**               |
| ------------- | ------------------------------------------------------- | ------------------------------------- |
| `-m`          | Muestra el resultado de la detección (Suspicious/Safe). | `mraptor "archivo.vbs"`               |
| `--csv`       | Genera el reporte en formato CSV para análisis masivo.  | `mraptor --csv ./lote/ > reporte.csv` |

# olemeta / oletimes
Útiles para determinar el origen del archivo o cuándo fue creado.

| **Parámetro** | **Función**                                                      | **Ejemplo de Sintaxis**     |
| ------------- | ---------------------------------------------------------------- | --------------------------- |
| `--json`      | Exporta los metadatos en formato JSON.                           | `olemeta --json "doc.doc"`  |
| `-t`          | (En `oletimes`) Muestra las marcas de tiempo en formato legible. | `oletimes -t "archivo.ole"` |