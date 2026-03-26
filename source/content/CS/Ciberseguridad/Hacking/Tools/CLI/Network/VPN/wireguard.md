https://www.wireguard.com/install/

|**Utilidad / Parámetro**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`wg show`|Muestra el estado actual y la configuración de las interfaces.|`wg show`|
|`wg genkey`|Genera una clave privada aleatoria en base64.|`wg genkey > private.key`|
|`wg pubkey`|Genera una clave pública a partir de una clave privada.|`wg pubkey < private.key > public.key`|
|`wg set`|Cambia la configuración de una interfaz en tiempo real.|`wg set wg0 peer <key> endpoint 1.2.3.4:51820`|
|`wg-quick up`|Levanta una interfaz utilizando un archivo `.conf`.|`wg-quick up wg0`|
|`wg-quick down`|Apaga una interfaz y limpia las rutas/reglas creadas.|`wg-quick down wg0`|
|`set ... listen-port`|Especifica el puerto UDP de escucha.|`wg set wg0 listen-port 51820`|
|`set ... allowed-ips`|Define qué IPs se enrutan a través del túnel para un peer.|`wg set wg0 peer <key> allowed-ips 10.0.0.2/32`|