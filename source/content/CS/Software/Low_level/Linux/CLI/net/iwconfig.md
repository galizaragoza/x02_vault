| **Parámetro/Comando**          | **Función**                                   | **Ejemplo de Sintaxis**               |
| ------------------------------ | --------------------------------------------- | ------------------------------------- |
| Sin parámetros                 | Muestra info de interfaces wireless           | `iwconfig`                            |
| `[interfaz]`                   | Muestra info de interfaz wireless específica  | `iwconfig wlan0`                      |
| `[interfaz] essid "[nombre]"`  | Conecta a red con ESSID específico            | `iwconfig wlan0 essid "MiWiFi"`       |
| `[interfaz] mode [modo]`       | Configura modo de operación                   | `iwconfig wlan0 mode Managed`         |
| `[interfaz] channel [canal]`   | Selecciona canal específico                   | `iwconfig wlan0 channel 6`            |
| `[interfaz] freq [frecuencia]` | Configura frecuencia específica               | `iwconfig wlan0 freq 2.422G`          |
| `[interfaz] ap [mac]`          | Conecta a punto de acceso específico          | `iwconfig wlan0 ap 00:11:22:33:44:55` |
| `[interfaz] key [clave]`       | Configura clave de cifrado WEP                | `iwconfig wlan0 key 12345-67890-12`   |
| `[interfaz] key off`           | Desactiva cifrado                             | `iwconfig wlan0 key off`              |
| `[interfaz] enc [clave]`       | Configura clave WEP (alternativa)             | `iwconfig wlan0 enc 1234567890`       |
| `[interfaz] nwid "[id]"`       | Configura ID de red (obsoleto)                | `iwconfig wlan0 nwid "MyNet"`         |
| `[interfaz] sens [umbral]`     | Configura sensibilidad                        | `iwconfig wlan0 sens 2`               |
| `[interfaz] txpower [nivel]`   | Configura potencia de transmisión             | `iwconfig wlan0 txpower 15`           |
| `[interfaz] rate [velocidad]`  | Configura tasa de transferencia               | `iwconfig wlan0 rate 11M`             |
| **Parámetro**                      | **Función**                                       | **Sintaxis de ejemplo**                   |
| `scan`                         | Escanea redes inalámbricas disponibles        | `iwlist wlan0 scan`                   |
| `scanning`                     | Muestra resultados del último escaneo         | `iwlist wlan0 scanning`               |
| `frequency`                    | Muestra información de frecuencias            | `iwlist wlan0 frequency`              |
| `channel`                      | Lista canales disponibles                     | `iwlist wlan0 channel`                |
| `rate`                         | Muestra velocidades de transmisión soportadas | `iwlist wlan0 rate`                   |
| `power`                        | Muestra información de gestión de energía     | `iwlist wlan0 power`                  |
| `txpower`                      | Muestra niveles de potencia de transmisión    | `iwlist wlan0 txpower`                |
| `retry`                        | Muestra límites de reintento de transmisión   | `iwlist wlan0 retry`                  |
| `ap`                           | Muestra puntos de acceso asociados            | `iwlist wlan0 ap`                     |
| `auth`                         | Muestra parámetros de autenticación           | `iwlist wlan0 auth`                   |
| `encryption`                   | Muestra parámetros de encriptación            | `iwlist wlan0 encryption`             |
| `keys`                         | Muestra claves de encriptación configuradas   | `iwlist wlan0 keys`                   |