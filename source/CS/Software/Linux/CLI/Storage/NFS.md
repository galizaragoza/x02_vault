NFS es un protocolo de sistema de archivos distribuido que permite a un usuario acceder a archivos a través de una red como si fueran locales. Es común en entornos UNIX/Linux.

|Utilidad/Comando|Función|Ejemplo de sintaxis|
|---|---|---|
|`exportfs`|Controla tabla de exportaciones NFS|`sudo exportfs -av`|
|`showmount`|Muestra sistemas de archivos montados|`showmount -e servidor`|
|`mount.nfs`|Monta recurso NFS|`mount -t nfs servidor:/ruta /mnt/local`|
|`nfsstat`|Muestra estadísticas NFS|`nfsstat -c` (cliente) o `nfsstat -s` (servidor)|
|`rpcinfo`|Muestra servicios RPC registrados|`rpcinfo -p servidor`|

## Montaje NFS

|Parámetro|Función|Ejemplo|
|---|---|---|
|`vers`|Versión del protocolo NFS|`-o vers=4.1`|
|`proto`|Protocolo de transporte|`-o proto=tcp`|
|`port`|Puerto del servidor|`-o port=2049`|
|`timeo`|Tiempo de espera en décimas de segundo|`-o timeo=600`|
|`retrans`|Número de reintentos|`-o retrans=3`|
|`hard`|Reintentos persistentes|`-o hard`|
|`soft`|Fallo tras reintentos|`-o soft`|
|`rsize`|Tamaño de lectura en bytes|`-o rsize=8192`|
|`wsize`|Tamaño de escritura en bytes|`-o wsize=8192`|