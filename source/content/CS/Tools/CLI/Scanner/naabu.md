# Guía Técnica: naabu

`naabu` es un escáner de puertos de alto rendimiento escrito en Go, diseñado para la velocidad y la fiabilidad en entornos de red masivos. Permite realizar escaneos rápidos de tipo SYN, CONNECT y UDP sobre miles de hosts de forma concurrente, optimizando el uso de recursos del sistema. Su propósito principal es facilitar la enumeración de servicios en activos de infraestructura, integrándose eficazmente en flujos de trabajo de seguridad mediante su capacidad de salida estructurada y soporte para múltiples protocolos.

---

## Configuración de Entrada (Input)

Define los objetivos, hosts y redes sobre los cuales se realizará el escaneo de puertos.

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-host`, `-u`|Especifica un host individual o una dirección IP para escanear.|`naabu -host example.com`|
|`-list`, `-l`|Archivo que contiene una lista de hosts o rangos CIDR.|`naabu -l targets.txt`|
|`-exclude-hosts`, `-eh`|Lista de hosts o IPs que deben ser omitidos del escaneo.|`naabu -eh 192.168.1.1`|
|`-exclude-file`, `-ef`|Archivo con la lista de hosts a excluir.|`naabu -ef exclude.txt`|

---

## Configuración de Puertos (Port Configuration)

Determina el alcance de los puertos que serán analizados durante la sesión.

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-port`, `-p`|Puertos específicos a escanear (soporta rangos y listas).|`naabu -p 80,443,1000-2000`|
|`-top-ports`, `-tp`|Escanea los puertos más comunes (soporta 100 y 1000).|`naabu -tp 100`|
|`-exclude-ports`, `-ep`|Puertos que deben ser ignorados durante el escaneo.|`naabu -ep 21,22`|
|`-ports-file`, `-pf`|Archivo que contiene la lista de puertos a escanear.|`naabu -pf ports.txt`|

---

## Escaneo y Técnicas (Scan Techniques)

Define el método de conexión y la lógica técnica aplicada para determinar el estado de un puerto.

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-s`|Tipo de escaneo a realizar: `s` (SYN) o `c` (CONNECT).|`naabu -s s`|
|`-udp`|Habilita el escaneo de puertos UDP.|`naabu -p 53,161 -udp`|
|`-discovery`|Intenta descubrir hosts activos antes del escaneo de puertos.|`naabu -discovery`|
|`-ping`|Utiliza peticiones ICMP Echo para verificar si el host está vivo.|`naabu -ping`|
|`-arp-ping`|Utiliza ARP para el descubrimiento de hosts en redes locales.|`naabu -arp-ping`|
|`-top-ports-parallel`|Ejecuta el escaneo de top ports en paralelo con el escaneo normal.|`naabu -tpp`|

---

## Rendimiento y Optimización (Performance)

Ajustes técnicos para controlar la velocidad del escaneo y evitar la saturación de la red.

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-rate`|Número de paquetes por segundo enviados.|`naabu -rate 1000`|
|`-threads`, `-t`|Número de hilos concurrentes para el escaneo (por defecto 10).|`naabu -t 50`|
|`-timeout`|Tiempo de espera en milisegundos para cada conexión.|`naabu -timeout 500`|
|`-retries`|Número de reintentos para un puerto si no hay respuesta.|`naabu -retries 2`|
|`-warm-up-time`|Tiempo de espera entre escaneos de hosts para evitar bloqueos.|`naabu -warm-up-time 5`|

---

## Evasión y Red (Evasion & Network)

Configuraciones avanzadas para la gestión de interfaces de red y detección.

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-interface`, `-i`|Especifica la interfaz de red a utilizar.|`naabu -i eth0`|
|`-source-ip`|Define la dirección IP de origen de los paquetes.|`naabu -source-ip 10.0.0.5`|
|`-source-port`|Define el puerto de origen de los paquetes.|`naabu -source-port 4444`|
|`-proxy`|Utiliza un proxy SOCKS5 para el escaneo (solo modo CONNECT).|`naabu -proxy 127.0.0.1:9050`|
|`-cnd`, `-check-nodeath`|Verifica si el nodo ha muerto antes de continuar el escaneo.|`naabu -cnd`|

---

## Formateo de Salida (Output)

Opciones para estructurar y guardar los resultados del escaneo.

|**Flag / Opción**|**Descripción**|**Ejemplo Práctico**|
|---|---|---|
|`-o`, `-output`|Archivo donde se guardarán los resultados.|`naabu -o results.txt`|
|`-json`|Escribe los resultados en formato JSON.|`naabu -json`|
|`-csv`|Escribe los resultados en formato CSV.|`naabu -csv`|
|`-silent`|Muestra únicamente los puertos encontrados en la terminal.|`naabu -silent`|
|`-no-color`|Desactiva los colores en la salida estándar.|`naabu -nc`|
|`-version`|Muestra la versión actual de la herramienta.|`naabu -version`|

---

## Servicios y Enumeración (Services)

Opciones adicionales para identificar qué corre detrás de cada puerto abierto.

| **Flag / Opción**     | **Descripción**                                                            | **Ejemplo Práctico**         |
| --------------------- | -------------------------------------------------------------------------- | ---------------------------- |
| `-verify`             | Valida si el puerto está realmente abierto mediante un handshake completo. | `naabu -verify`              |
| `-nmap-cli`           | Ejecuta comandos de Nmap sobre los puertos abiertos detectados.            | `naabu -nmap-cli "nmap -sV"` |
| `-nc`, `-nmap-config` | Especifica un archivo de configuración de Nmap personalizado.              | `naabu -nc nmap.conf`        |