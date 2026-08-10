# dsniff
| Parámetro            | Función                           | Ejemplo                        |
| -------------------- | --------------------------------- | ------------------------------ |
| `-c`                 | Activa verificación de suma       | `dsniff -c`                    |
| `-d`                 | Habilita modo debug               | `dsniff -d`                    |
| `-m`                 | Habilita autopromiscuo automático | `dsniff -m`                    |
| `-n`                 | No resuelve nombres de host       | `dsniff -n`                    |
| `-i interface`       | Especifica interfaz de red        | `dsniff -i eth0`               |
| `-p archivo.pcap`    | Lee desde archivo pcap            | `dsniff -p captura.pcap`       |
| `-s longitud`        | Longitud máxima de paquetes       | `dsniff -s 2048`               |
| `-f servicios`       | Archivo con servicios adicionales | `dsniff -f mis_servicios`      |
| `-t trigger[=valor]` | Filtra por triggers               | `dsniff -t host=192.168.1.100` |
| `-w archivo`         | Escribe salida a archivo          | `dsniff -w output.log`         |

# filesnarf
| Parámetro         | Función             | Ejemplo                     |
| ----------------- | ------------------- | --------------------------- |
| `-i interface`    | Especifica interfaz | `filesnarf -i eth0`         |
| `-p archivo.pcap` | Lee desde pcap      | `filesnarf -p trafico.pcap` |
| `-v`              | Modo verbose        | `filesnarf -v`              |
| `expresión`       | Filtro BPF          | `filesnarf port 2049`       |

# mailsnarf
| Parámetro         | Función         | Ejemplo                                     |
| ----------------- | --------------- | ------------------------------------------- |
| `-i interface`    | Interfaz de red | `mailsnarf -i wlan0`                        |
| `-p archivo.pcap` | Lee desde pcap  | `mailsnarf -p email.pcap`                   |
| `-v`              | Modo verbose    | `mailsnarf -v`                              |
| `expresión`       | Filtro BPF      | `mailsnarf port 25 or port 110 or port 143` |

# msgsnarf
|Parámetro|Función|Ejemplo|
|---|---|---|
|`-i interface`|Interfaz de red|`msgsnarf -i eth0`|
|`-p archivo.pcap`|Lee desde pcap|`msgsnarf -p chat.pcap`|
|`-d`|Incluye fecha/hora|`msgsnarf -d`|
|`-n`|No resuelve nombres|`msgsnarf -n`|

# urlsnarf
| Parámetro         | Función                 | Ejemplo                       |
| ----------------- | ----------------------- | ----------------------------- |
| `-i interface`    | Interfaz de red         | `urlsnarf -i eth0`            |
| `-p archivo.pcap` | Lee desde pcap          | `urlsnarf -p web.pcap`        |
| `-n`              | No resuelve nombres     | `urlsnarf -n`                 |
| `-r`              | Solo URLs referenciadas | `urlsnarf -r`                 |
| `expresión`       | Filtro BPF              | `urlsnarf host 192.168.1.100` |

# webspy
| Parámetro         | Función         | Ejemplo                   |
| ----------------- | --------------- | ------------------------- |
| `-i interface`    | Interfaz de red | `webspy -i eth0`          |
| `-p archivo.pcap` | Lee desde pcap  | `webspy -p web.pcap`      |
| `-n host`         | Host objetivo   | `webspy -n 192.168.1.100` |
| `-v`              | Modo verbose    | `webspy -v`               |

# sshmitm
| Parámetro      | Función           | Ejemplo        |
| -------------- | ----------------- | -------------- |
| `-d`           | Modo debug        | `sshmitm -d`   |
| `-h`           | Muestra ayuda     | `sshmitm -h`   |
| `puerto_local` | Puerto de escucha | `sshmitm 2222` |

# macof
| Parámetro      | Función            | Ejemplo                      |
| -------------- | ------------------ | ---------------------------- |
| `-i interface` | Interfaz de red    | `macof -i eth0`              |
| `-s src`       | IP origen          | `macof -s 192.168.1.1`       |
| `-d dst`       | IP destino         | `macof -d 192.168.1.100`     |
| `-e tha`       | MAC objetivo       | `macof -e 00:11:22:33:44:55` |
| `-x times`     | Número de paquetes | `macof -x 1000`              |
| `-n times`     | Número de paquetes | `macof -n 500`               |

# tcpkill
| Parámetro      | Función                | Ejemplo           |
| -------------- | ---------------------- | ----------------- |
| `-i interface` | Interfaz de red        | `tcpkill -i eth0` |
| `-num`         | Número de paquetes RST | `tcpkill -3`      |
| `expresión`    | Filtro BPF             | `tcpkill port 22` |

# tcpnice
| Parámetro       | Función             | Ejemplo           |
| --------------- | ------------------- | ----------------- |
| `-i interface`  | Interfaz de red     | `tcpnice -i eth0` |
| `-n incremento` | Incremento de delay | `tcpnice -n 50`   |
| `expresión`     | Filtro BPF          | `tcpnice port 80` |