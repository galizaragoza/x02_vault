| **Comando**           | **Función**                          | **Sintaxis de ejemplo**        |
| --------------------- | ------------------------------------ | ------------------------------ |
| `list`                | Muestra controladores Bluetooth      | `list`                         |
| `show [ctrl]`         | Muestra información de controlador   | `show`                         |
| `select <ctrl>`       | Selecciona controlador por defecto   | `select hci0`                  |
| `power on/off`        | Enciende/apaga controlador           | `power on`                     |
| `pairable on/off`     | Habilita/deshabilita emparejamiento  | `pairable on`                  |
| `discoverable on/off` | Hace visible el dispositivo          | `discoverable on`              |
| `scan on/off`         | Escanea dispositivos cercanos        | `scan on`                      |
| `devices`             | Lista dispositivos conocidos         | `devices`                      |
| `pair <addr>`         | Empareja con dispositivo             | `pair 00:11:22:33:44:55`       |
| `trust <addr>`        | Marca dispositivo como confiable     | `trust 00:11:22:33:44:55`      |
| `connect <addr>`      | Conecta a dispositivo                | `connect 00:11:22:33:44:55`    |
| `disconnect <addr>`   | Desconecta dispositivo               | `disconnect 00:11:22:33:44:55` |
| `remove <addr>`       | Elimina dispositivo de lista         | `remove 00:11:22:33:44:55`     |
| `info <addr>`         | Información detallada de dispositivo | `info 00:11:22:33:44:55`       |
| `menu <submenu>`      | Cambia a submenú específico          | `menu agent`                   |
| `back`                | Regresa al menú anterior             | `back`                         |
| `quit`                | Sale de bluetoothctl                 | `quit`                         |

# Otras

|Utilidad|Función|Sintaxis de ejemplo|
|---|---|---|
|`btattach`|Conecta dispositivos Bluetooth UART|`btattach -B /dev/ttyUSB0 -P h4`|
|`btmon`|Monitor de paquetes Bluetooth|`btmon`|
|`hciconfig` (legacy)|Configura interfaz HCI|`hciconfig hci0 up`|
|`hcitool` (legacy)|Herramientas varias Bluetooth|`hcitool scan`|
|`sdptool`|Consulta servicios SDP|`sdptool browse 00:11:22:33:44:55`|