## Alternate Data Streams (ADS)
`$DATA` es el que contiene la información del archivo. Y una característica de NTFS es que permite multiples entradas del atributo `$DATA` a cada record.

Por ejemplo, los avisos que Windows emite al intentar ejecutar un archivo descargado que Internet que no está firmado, saltan porque el registro X tiene un atributo `$DATA` que contiene la información del archivo, y otro que actúa como tag para indicar a Windows que muestre el aviso.

Utilizando esta técnica, se pueden "inyectar" ADSs en un archivo benigno que a todas luces no contiene más que un "Hola" en texto plano, pero que en realidad contiene un payload malicios ejecutable en un ADS
## Recursos

[Hiding Data Using NTFS Alternate Data Streams (Defence Evasion)](https://youtu.be/S4MBzeni9Eo)
[# How NTFS Works](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc781134(v=ws.10))
[# Sneaky Tips and Tricks with Alternate Data Streams (ADS’s)](https://www.youtube.com/watch?v=3qWzmJWfDpQ&pp=ygVHU2VhbiBQaWVyY2Ugb24g4oCcU25lYWt5IFRpcHMgYW5kIFRyaWNrcyB3aXRoIEFsdGVybmF0ZSBEYXRhIFN0cmVhbXPigJ0%3D)