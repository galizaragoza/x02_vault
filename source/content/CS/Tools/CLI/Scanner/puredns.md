# Guía Técnica: puredns

`puredns` es una herramienta de resolución y fuerza bruta de DNS de alto rendimiento diseñada para manejar listas masivas de dominios con una precisión extrema. Utiliza una arquitectura que permite filtrar resultados falsos positivos mediante la validación contra resolvedores de confianza y la detección de _wildcards_. Su propósito principal es facilitar la enumeración de subdominios a gran escala, integrando mecanismos de limitación de tasa y reintentos para optimizar el uso del ancho de banda y la fiabilidad de los datos obtenidos.

---

## Subherramienta: `resolve`

Esta subherramienta se utiliza para resolver una lista de dominios y filtrar aquellos que no tienen registros DNS válidos.

### Configuración de Entrada y Salida

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-r`, `--resolvers`|Archivo que contiene una lista de resolvedores DNS públicos.|`puredns resolve dominios.txt -r resolvers.txt`|
|`-w`, `--write`|Archivo donde se guardarán los resultados resueltos.|`puredns resolve dominios.txt -w validos.txt`|
|`--wildcard-batch`|Número de subdominios a utilizar para la prueba de wildcard.|`puredns resolve list.txt --wildcard-batch 100`|

### Rendimiento

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-t`, `--threads`|Número de hilos simultáneos para la resolución.|`puredns resolve dominios.txt -t 500`|
|`--rate-limit`|Número máximo de paquetes por segundo (PPS) enviados.|`puredns resolve dominios.txt --rate-limit 1000`|

---

## Subherramienta: `bruteforce`

Diseñada para realizar ataques de fuerza bruta utilizando una lista de palabras (_wordlist_) sobre un dominio raíz.

### Operaciones de Fuerza Bruta

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-d`, `--domain`|El dominio objetivo para el ataque de fuerza bruta.|`puredns bruteforce words.txt -d example.com`|
|`-r`, `--resolvers`|Lista de resolvedores para realizar la consulta.|`puredns bruteforce words.txt -d example.com -r res.txt`|

### Validación y Técnicas

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`--trusted-resolvers`|Lista de resolvedores de confianza para validar resultados y filtrar wildcards.|`puredns bruteforce words.txt -d target.com --trusted-resolvers trusted.txt`|
|`--wildcard-tests`|Número de pruebas de detección de wildcard a realizar.|`puredns bruteforce words.txt -d target.com --wildcard-tests 25`|

---

## Opciones Globales (General Options)

Parámetros técnicos aplicables a múltiples modos de operación para el ajuste fino de la red y la visualización.

### Configuración de Red y Reintentos

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`--timeout`|Tiempo de espera máximo para una respuesta DNS.|`puredns resolve list.txt --timeout 2s`|
|`--max-retries`|Número máximo de reintentos para una consulta fallida.|`puredns resolve list.txt --max-retries 3`|
|`--internal-resolv`|Utiliza el resolvedor interno del sistema en lugar de la lógica de red propia.|`puredns resolve list.txt --internal-resolv`|

### Formateo y Visualización

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-v`, `--verbose`|Muestra información detallada durante la ejecución.|`puredns resolve list.txt -v`|
|`--quiet`|Desactiva toda la salida excepto los resultados finales.|`puredns resolve list.txt --quiet`|
|`--stats`|Muestra estadísticas periódicas del progreso en la terminal.|`puredns resolve list.txt --stats`|

---

## Técnicas de Evasión y Filtrado

Opciones específicas para garantizar la limpieza de los datos resultantes frente a configuraciones DNS complejas.

| **Flag / Opción**       | **Descripción**                                                  | **Ejemplo Práctico**                                    |
| ----------------------- | ---------------------------------------------------------------- | ------------------------------------------------------- |
| `--skip-wildcard-check` | Salta la detección de wildcards (no recomendado para precisión). | `puredns resolve list.txt --skip-wildcard-check`        |
| `--skip-sanitize`       | No limpia los caracteres inválidos de la lista de entrada.       | `puredns resolve list.txt --skip-sanitize`              |
| `--bin`                 | Ruta al ejecutable de `massdns` si no se encuentra en el PATH.   | `puredns resolve list.txt --bin /usr/local/bin/massdns` |