
| **Subcomando**            | **Función**                          | **Sintaxis de ejemplo**                                                               |
| --------------------- | -------------------------------- | --------------------------------------------------------------------------------- |
| `general status`      | Estado general de NetworkManager | `nmcli general status`                                                            |
| `networking on/off`   | Activa/desactiva red globalmente | `nmcli networking off`                                                            |
| `radio wifi on/off`   | Controla radio WiFi              | `nmcli radio wifi on`                                                             |
| `connection show`     | Lista conexiones configuradas    | `nmcli connection show`                                                           |
| `connection up/down`  | Activa/desactiva conexión        | `nmcli connection up "MiWiFi"`                                                    |
| `connection add`      | Crea nueva conexión              | `nmcli connection add type wifi con-name "Casa" ifname wlan0 ssid "MiRed"`        |
| `connection delete`   | Elimina conexión                 | `nmcli connection delete "Casa"`                                                  |
| `connection modify`   | Modifica parámetros de conexión  | `nmcli connection modify "Casa" wifi-sec.key-mgmt wpa-psk wifi-sec.psk "miclave"` |
| `device status`       | Estado de dispositivos de red    | `nmcli device status`                                                             |
| `device wifi list`    | Escanea redes WiFi               | `nmcli device wifi list`                                                          |
| `device wifi connect` | Conecta a red WiFi               | `nmcli device wifi connect "MiRed" password "clave123"`                           |
| `device wifi rescan`  | Re-escanea redes WiFi            | `nmcli device wifi rescan`                                                        |
| `device disconnect`   | Desconecta dispositivo           | `nmcli device disconnect wlan0`                                                   |
| `-p` (pretty)         | Salida formateada legible        | `nmcli -p device status`                                                          |
| `-t` (terse)          | Salida en formato máquina        | `nmcli -t device status`                                                          |
| `-f` (fields)         | Especifica campos a mostrar      | `nmcli -f GENERAL,IP4 device show wlan0`                                          |