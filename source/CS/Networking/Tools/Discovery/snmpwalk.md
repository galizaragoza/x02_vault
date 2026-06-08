snmpwalk realiza consultas SNMP iterativas usando el mensaje GETNEXT para recorrer el árbol MIB (Management Information Base) de un dispositivo de red. A partir de un OID inicial, avanza automáticamente por todos los objetos del subárbol hasta llegar al final. Es la herramienta principal para enumerar configuración de red, interfaces, tablas de rutas, software instalado y credenciales en equipos que exponen SNMP. Forma parte del paquete `net-snmp`.

```
snmpwalk [opciones] <agente> [OID]
```

---

## Versión y autenticación SNMPv1/v2c

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v <versión>` | Versión del protocolo SNMP: `1`, `2c` o `3`. | `snmpwalk -v 2c -c public 192.168.1.1` |
| `-c <comunidad>` | Community string para SNMPv1/v2c (read-only suele ser `public`). | `snmpwalk -v 2c -c public 192.168.1.1` |

---

## Autenticación SNMPv3

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-u <usuario>` | Nombre de usuario SNMPv3. | `snmpwalk -v 3 -u admin 192.168.1.1` |
| `-l <nivel>` | Nivel de seguridad: `noAuthNoPriv`, `authNoPriv`, `authPriv`. | `snmpwalk -v 3 -u admin -l authPriv 192.168.1.1` |
| `-a <protocolo>` | Protocolo de autenticación: `MD5` o `SHA` (o `SHA-256`, `SHA-512` en versiones recientes). | `snmpwalk -v 3 -u admin -l authNoPriv -a SHA 192.168.1.1` |
| `-A <contraseña>` | Contraseña de autenticación. | `snmpwalk -v 3 -u admin -l authPriv -a SHA -A auth_pass 192.168.1.1` |
| `-x <protocolo>` | Protocolo de cifrado (privacidad): `DES` o `AES`. | `snmpwalk -v 3 -u admin -l authPriv -a SHA -A pass -x AES 192.168.1.1` |
| `-X <contraseña>` | Contraseña de privacidad/cifrado. | `snmpwalk -v 3 -u admin -l authPriv -a SHA -A authpass -x AES -X privpass 192.168.1.1` |
| `-e <engineID>` | Engine ID del agente SNMP en hexadecimal. | `snmpwalk -v 3 -e 0x80001f8880... 192.168.1.1` |
| `-n <contexto>` | Nombre de contexto SNMPv3. | `snmpwalk -v 3 -n "ctx1" 192.168.1.1` |

---

## Conexión y transporte

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t <segundos>` | Timeout de respuesta en segundos. Por defecto: 5. | `snmpwalk -t 10 -v 2c -c public 192.168.1.1` |
| `-r <reintentos>` | Número de reintentos en caso de timeout. Por defecto: 5. | `snmpwalk -r 2 -v 2c -c public 192.168.1.1` |
| `-p <puerto>` | Puerto UDP del agente SNMP. Por defecto: 161. | `snmpwalk -p 10161 -v 2c -c public 192.168.1.1` |

---

## Formato de salida (flags `-O`)

Los modificadores de salida se combinan tras `-O` sin separación (ej. `-OsQ`).

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-Os` | Muestra solo la última parte del OID (nombre corto), sin la ruta completa. | `snmpwalk -Os -v 2c -c public 192.168.1.1` |
| `-On` | Muestra OIDs en formato numérico en lugar de nombres. | `snmpwalk -On -v 2c -c public 192.168.1.1` |
| `-Oe` | No añade la descripción del enum; muestra el valor numérico directamente. | `snmpwalk -Oe -v 2c -c public 192.168.1.1` |
| `-Oa` | Imprime strings como ASCII (muestra cadenas de bytes como texto). | `snmpwalk -Oa -v 2c -c public 192.168.1.1 system` |
| `-Ox` | Imprime strings como hex en lugar de ASCII. | `snmpwalk -Ox -v 2c -c public 192.168.1.1` |
| `-OX` | Usa notación de índices estilo SNMP extendido (`[idx]` en lugar de `.idx`). | `snmpwalk -OX -v 2c -c public 192.168.1.1` |
| `-Oq` | Salida compacta: omite el tipo de dato. Solo nombre = valor. | `snmpwalk -Oq -v 2c -c public 192.168.1.1` |
| `-OQ` | Como `-Oq` pero sin unidades ni prefijos. | `snmpwalk -OQ -v 2c -c public 192.168.1.1` |

---

## OIDs de enumeración frecuentes en pentesting

| OID | Descripción |
|-----|-------------|
| `system` / `1.3.6.1.2.1.1` | Información del sistema: descripción, uptime, contacto, nombre, localización. |
| `interfaces` / `1.3.6.1.2.1.2` | Tabla de interfaces de red (nombre, MTU, velocidad, MAC, estado). |
| `ipAddrTable` / `1.3.6.1.2.1.4.20` | Direcciones IP configuradas en el dispositivo. |
| `ipRouteTable` / `1.3.6.1.2.1.4.21` | Tabla de rutas IP. |
| `tcpConnTable` / `1.3.6.1.2.1.6.13` | Conexiones TCP activas con puertos y estados. |
| `udpTable` / `1.3.6.1.2.1.7.5` | Puertos UDP en escucha. |
| `hrSWInstalledTable` / `1.3.6.1.2.1.25.6.3` | Software instalado (Host Resources MIB). |
| `hrSWRunTable` / `1.3.6.1.2.1.25.4.2` | Procesos en ejecución. |
| `hrStorageTable` / `1.3.6.1.2.1.25.2.3` | Sistemas de ficheros y almacenamiento. |
| `sysDescr` / `1.3.6.1.2.1.1.1.0` | Descripción del sistema operativo y versión. |
| `sysContact` / `1.3.6.1.2.1.1.4.0` | Contacto del administrador. |
| `sysName` / `1.3.6.1.2.1.1.5.0` | Nombre del host. |

---

## Casos de uso comunes

```bash
# Enumeración completa con SNMPv2c
snmpwalk -v 2c -c public 192.168.1.1

# Solo información del sistema
snmpwalk -v 2c -c public 192.168.1.1 system

# Interfaces de red
snmpwalk -v 2c -c public 192.168.1.1 interfaces

# Procesos en ejecución
snmpwalk -v 2c -c public 192.168.1.1 hrSWRunTable

# Software instalado
snmpwalk -v 2c -c public 192.168.1.1 hrSWInstalledTable

# Salida numérica (para análisis posterior o cuando MIBs no están instaladas)
snmpwalk -On -v 2c -c public 192.168.1.1

# SNMPv3 con autenticación y privacidad
snmpwalk -v 3 -u netadmin -l authPriv -a SHA -A "AuthPass123" -x AES -X "PrivPass123" 192.168.1.1

# Guardar toda la enumeración en fichero
snmpwalk -v 2c -c public 192.168.1.1 > snmp_enum.txt

# Community string alternativas comunes
snmpwalk -v 2c -c private 192.168.1.1
snmpwalk -v 2c -c manager 192.168.1.1
```
