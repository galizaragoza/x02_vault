**snmpwalk** es una aplicación SNMP que utiliza solicitudes `GETNEXT` para consultar una red de entidades en busca de un árbol de información (MIBs).

| **Parámetro** | **Función**                                                    | **Ejemplo de sintaxis**                    |
| ------------- | -------------------------------------------------------------- | ------------------------------------------ |
| `-v`          | Especifica la versión de SNMP a usar (1, 2c, 3)                | `snmpwalk -v 2c -c public 192.168.1.1`     |
| `-c`          | Define la cadena de comunidad (community string)               | `snmpwalk -c public 192.168.1.1`           |
| `-Os`         | Muestra solo los últimos elementos de los OIDs (formato corto) | `snmpwalk -Os -c public 192.168.1.1`       |
| `-t`          | Establece el tiempo de espera (timeout) en segundos            | `snmpwalk -t 5 -v 1 192.168.1.1`           |
| `-r`          | Define el número de reintentos en caso de fallo                | `snmpwalk -r 3 192.168.1.1`                |
| `-On`         | Muestra los OIDs en formato numérico                           | `snmpwalk -On -v 2c -c public 192.168.1.1` |