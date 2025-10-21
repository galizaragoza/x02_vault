# ¿Qué es NTFS?
NTFS es un filesystem comúnmente utilizado en Windows. Crea clusters, que son la unidad mínima de espacio que se puede reservar para alojar un archivo

Los clusters en NTFS se numeran de forma secuencial desde el inicio de la partición en clusters lógicos, NTFS almacena estos objetos en el filesystem utilizando un registro llamado Master File Table (MFT).
Cada uno de estos registros representa un archivo, y contiene atributos como `$STANDARD_INFORMATION` o `$SECURITY_DESCRIPTOR`, y contienen información sobre el creador del archivo, sus permisos, nombre, fecha de creación/modificación...

|Attribute Name|Description|
|---|---|
|$ATTRIBUTE_LIST|Lists the location of all attribute records that do not fit in the MFT record|
|$BITMAP|Attribute for Bitmaps|
|$DATA|Contains the default file data|
|$EA|Extended the attribute index|
|$EA_INFORMATION|Extended attribute information|
|$FILE_NAME|File name|
|$INDEX_ALLOCATION|The type name for a Directory Stream. A string for the attribute code for index allocation|
|$INDEX_ROOT|Used to support folders and other indexes|
|$LOGGED_UTILITY_STREAM|Use by the encrypting file system|
|$OBJECT_ID|Unique [GUID](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-fscc/8ac44452-328c-4d7b-a784-d72afd19bd9f#gt_f49694cc-c350-462d-ab8e-816f0103c6c1) for every MFT record|
|$PROPERTY_SET|Obsolete|
|$REPARSE_POINT|Used for volume mount points|
|$SECURITY_DESCRIPTOR|Security descriptor stores [ACL](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-fscc/8ac44452-328c-4d7b-a784-d72afd19bd9f#gt_9f92aa05-dd0a-45f2-88d6-89f1fb654395) and [SIDs](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-fscc/8ac44452-328c-4d7b-a784-d72afd19bd9f#gt_83f2020d-0804-4840-a5ac-e06439d50f8d)|
|$STANDARD_INFORMATION|Standard information, such as file times and quota data|
|$SYMBOLIC_LINK|Obsolete|
|$TXF_DATA|Transactional NTFS data|
|$VOLUME_INFORMATION|Version and state of the volume|
|$VOLUME_NAME|Name of the volume|
|$VOLUME_VERSION|Obsolete. Volume version|
## Alternate Data Streams (ADS)
`$DATA` es el que contiene la información del archivo. Y una característica de NTFS es que permite multiples entradas del atributo `$DATA` a cada record.

Por ejemplo, los avisos que Windows emite al intentar ejecutar un archivo descargado que Internet que no está firmado, saltan porque el registro X tiene un atributo `$DATA` que contiene la información del archivo, y otro que actúa como tag para indicar a Windows que muestre el aviso.

Utilizando esta técnica, se pueden "inyectar" ADSs en un archivo benigno que a todas luces no contiene más que un "Hola" en texto plano, pero que en realidad contiene un payload malicios ejecutable en un ADS
## Recursos

[Hiding Data Using NTFS Alternate Data Streams (Defence Evasion)](https://youtu.be/S4MBzeni9Eo)
[# How NTFS Works](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc781134(v=ws.10))
[# Sneaky Tips and Tricks with Alternate Data Streams (ADS’s)](https://www.youtube.com/watch?v=3qWzmJWfDpQ&pp=ygVHU2VhbiBQaWVyY2Ugb24g4oCcU25lYWt5IFRpcHMgYW5kIFRyaWNrcyB3aXRoIEFsdGVybmF0ZSBEYXRhIFN0cmVhbXPigJ0%3D)