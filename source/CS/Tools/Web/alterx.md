## Configuración de Entrada (Input)

Esta sección define cómo la herramienta recibe los dominios base para iniciar el proceso de permutación.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-l`, `-list`|Archivo que contiene una lista de dominios para procesar.|`alterx -l dominios.txt`|
|`-u`, `-url`|Especifica un dominio único directamente en la línea de comandos.|`alterx -u target.com`|

---

## Gestión de Patrones (Patterns)

Los patrones determinan las reglas de transformación que se aplicarán a los dominios de entrada.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-p`, `-pattern`|Define patrones de permutación personalizados manualmente.|`alterx -p "{{sub}}.{{word}}.{{suffix}}"`|
|`-pp`, `-pattern-path`|Archivo o directorio que contiene listas de patrones (YAML).|`alterx -pp ./patterns.yaml`|
|`-wp`, `-wordlist-path`|Archivo de lista de palabras (wordlist) para usar en las permutaciones.|`alterx -wp words.txt`|
|`-v`, `-variable`|Define variables personalizadas para usar en los patrones.|`alterx -v "env=dev,stg"`|

---

## Filtros y Transformación (Filtering)

Opciones para refinar los resultados generados y asegurar que cumplan con ciertos criterios.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-ec`, `-exclude-check`|Excluye resultados que coincidan exactamente con la entrada original.|`alterx -u dev.api.com -ec`|
|`-m`, `-match`|Muestra solo los resultados que coincidan con una expresión regular.|`alterx -m ".*-prod.*"`|
|`-f`, `-filter`|Filtra (elimina) los resultados que coincidan con una expresión regular.|`alterx -f "test$"`|

---

## Rendimiento y Optimización (Performance)

Parámetros para controlar el consumo de recursos y la velocidad de generación.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-c`, `-concurrency`|Número de dominios a procesar de forma concurrente (por defecto 100).|`alterx -c 200`|
|`-limit`|Limita el número máximo de variaciones generadas por cada dominio.|`alterx -limit 500`|

---

## Configuración de Salida (Output)

Define cómo y dónde se presentan los resultados obtenidos.

|**Flag / Parámetro**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-o`, `-output`|Archivo de texto donde se almacenarán los resultados.|`alterx -o variaciones.txt`|
|`-json`|Genera la salida en formato JSON estructurado.|`alterx -json`|
|`-silent`|Muestra únicamente los dominios generados, omitiendo banners y logs.|`alterx -silent`|
|`-no-color`|Desactiva el uso de colores en la salida por terminal.|`alterx -no-color`|

---

## Depuración y Mantenimiento (Debug & Updates)

Herramientas para la gestión de la aplicación y resolución de problemas.

| **Flag / Parámetro**        | **Descripción**                                                    | **Ejemplo Práctico**          |
| --------------------------- | ------------------------------------------------------------------ | ----------------------------- |
| `-up`, `-update`            | Actualiza la herramienta a la última versión disponible en GitHub. | `alterx -up`                  |
| `-version`                  | Muestra la versión actual de la herramienta instalada.             | `alterx -version`             |
| `-verbose`                  | Muestra información detallada durante la ejecución del proceso.    | `alterx -verbose`             |
| `-vtp`, `-validate-pattern` | Valida la sintaxis de los archivos de patrones proporcionados.     | `alterx -vtp ./patterns.yaml` |