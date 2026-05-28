**Bettercap** es el framework definitivo para reconocimiento y ataques de red (WiFi, Bluetooth Low Energy, redes Ethernet e IPv4/IPv6). A diferencia de otras, funciona mediante un shell interactivo y módulos.

|**Parámetro**|**Función**|**Ejemplo de Sintaxis**|
|---|---|---|
|`-iface`|Define la interfaz de red a utilizar.|`bettercap -iface eth0`|
|`-eval`|Ejecuta comandos inmediatamente después de iniciar.|`bettercap -eval "net.probe on"`|
|`-caplet`|Carga un archivo de comandos (`.cap`) preconfigurado.|`bettercap -caplet http-auth-sniffer`|
|`-silent`|Suprime todos los mensajes que no sean errores.|`bettercap -silent`|
|`-no-history`|No guarda el historial de comandos ejecutados.|`bettercap -no-history`|

# Subcomandos
| **Módulo / Comando** | **Función**                                                     | **Ejemplo de Sintaxis (Intra-shell)**             |
| -------------------- | --------------------------------------------------------------- | ------------------------------------------------- |
| `help`               | Muestra la ayuda general o de un módulo específico.             | `help net.probe`                                  |
| `net.probe on/off`   | Activa el descubrimiento de hosts mediante peticiones ARP/ICMP. | `net.probe on`                                    |
| `net.show`           | Muestra la tabla de hosts descubiertos en la red.               | `net.show`                                        |
| `arp.spoof on/off`   | Inicia el ataque de envenenamiento ARP (MITM).                  | `set arp.spoof.targets 192.168.1.5; arp.spoof on` |
| `any.proxy on/off`   | Activa un proxy transparente para interceptar tráfico.          | `any.proxy on`                                    |
| `net.sniff on/off`   | Inicia el sniffer de red para capturar paquetes.                | `net.sniff on`                                    |
| `wifi.recon on/off`  | Inicia el escaneo de redes WiFi cercanas.                       | `wifi.recon on`                                   |
| `ticker on/off`      | Ejecuta una lista de comandos cada N segundos.                  | `ticker on`                                       |