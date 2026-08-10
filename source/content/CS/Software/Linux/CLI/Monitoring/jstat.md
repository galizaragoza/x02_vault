`jstat` (JVM Statistics Monitoring Tool) es una utilidad del JDK que muestra estadísticas de rendimiento de una máquina virtual HotSpot en ejecución, sin necesidad de instrumentar la aplicación. Se conecta a una JVM identificada por su `lvmid` (local VM id, normalmente el PID) y reporta métricas de recolección de basura, uso de generaciones del heap, carga de clases y compilación JIT. Permite muestreo periódico mediante un intervalo y un contador, lo que la hace útil para diagnóstico de GC y fugas de memoria. Es una herramienta experimental: la salida y los nombres de columna pueden variar entre versiones del JDK.

> Sintaxis: `jstat <opción-estadística> [-t] [-h<líneas>] <vmid> [intervalo[s|ms] [count]]`
> El `vmid` admite el formato `[protocolo:][//]lvmid[@host[:puerto]/servidor]` para JVMs remotas vía `jstatd`.

# Opciones generales

Mutuamente excluyentes con las opciones de estadística; muestran información y terminan.

| **Opción** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-help` | Muestra la ayuda de uso. | `jstat -help` |
| `-options` | Lista las opciones de estadística disponibles en esa JVM. | `jstat -options` |
| `-version` | Muestra la versión de la herramienta. | `jstat -version` |
| `-J<flag>` | Pasa un flag directamente al runtime de la JVM que ejecuta jstat. | `jstat -J-Xmx128m -gc 1234` |

# Modificadores de salida y muestreo

Se combinan con cualquier opción de estadística.

| **Opción** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-t` | Añade una primera columna `Timestamp` con los segundos desde el arranque de la JVM. | `jstat -t -gc 1234` |
| `-h<n>` | Reimprime la cabecera de columnas cada `n` líneas de muestreo. | `jstat -h10 -gcutil 1234 1s` |
| `intervalo[s\|ms]` | Periodo entre muestras (segundos por defecto, o milisegundos con `ms`). | `jstat -gc 1234 500ms` |
| `count` | Número de muestras a tomar antes de salir. | `jstat -gc 1234 1s 20` |

# Estadísticas de recolección de basura (GC)

| **Opción** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-gc` | Estadísticas del heap recolectado: capacidad y uso de cada espacio (Eden, Survivor, Old, Metaspace) y tiempos de GC. | `jstat -gc 1234 1s` |
| `-gccapacity` | Capacidades mínimas/máximas/actuales de cada generación y espacio. | `jstat -gccapacity 1234` |
| `-gcutil` | Resumen porcentual de utilización de cada espacio y tiempos de GC. | `jstat -gcutil 1234 1s 10` |
| `-gccause` | Igual que `-gcutil` añadiendo la causa del último GC y del GC en curso. | `jstat -gccause 1234 1s` |
| `-gcnew` | Comportamiento de la generación joven (Eden, Survivors, tenuring). | `jstat -gcnew 1234` |
| `-gcnewcapacity` | Tamaños de la generación joven y sus espacios. | `jstat -gcnewcapacity 1234` |
| `-gcold` | Comportamiento de la generación vieja y de Metaspace. | `jstat -gcold 1234` |
| `-gcoldcapacity` | Tamaños de la generación vieja. | `jstat -gcoldcapacity 1234` |
| `-gcmetacapacity` | Tamaños de Metaspace. | `jstat -gcmetacapacity 1234` |

# Carga de clases y compilación

| **Opción** | **Descripción** | **Ejemplo** |
| --- | --- | --- |
| `-class` | Estadísticas del cargador de clases: nº cargadas/descargadas, bytes y tiempo. | `jstat -class 1234` |
| `-compiler` | Estadísticas del compilador JIT de HotSpot (nº de compilaciones, fallos, tiempo). | `jstat -compiler 1234` |
| `-printcompilation` | Estadísticas del último método compilado por HotSpot. | `jstat -printcompilation 1234 1s` |

## Notas

- El `lvmid` suele coincidir con el PID del proceso; se obtiene con [[ps]] o con `jps`.
- Los tiempos `YGCT`/`FGCT`/`GCT` son acumulativos en segundos; el crecimiento sostenido de `FGC` (full GCs) indica presión de memoria.
- Para monitorizar una JVM remota debe ejecutarse `jstatd` en el host destino.
- Relacionada (monitorización de runtime): [[pidstat]], [[top]].
