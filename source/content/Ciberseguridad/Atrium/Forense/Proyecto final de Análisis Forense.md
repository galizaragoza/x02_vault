## Antes de analizar las evidencias
### Análisis de la amenaza en la sede Australiana
Sin lugar a dudas, el ataque sufrido en la sede Australiana es un ataque de phishing masivo. El phishing es un tipo de ciberataque basado en la ingeniería social que explota "vulnerabilidades humanas", como la avaricia, miedo, o en este caso falta de concienciación.

Los atacantes han aprovechando la falta de formación de los empleados para conducirlos, a partir de un email fraudulento, a una página de login que imita a la de un servicio legítimo de la empresa (Office  365), pero que envía sus credenciales a un servidor controlado por el atacante en lugar de autenticarlos en la plataforma real.

La fuente exacta de los correos no ha sido encontrada, puede que por encriptación del tráfico o porque los registros no cubren el momento en que esos correos fueron enviados/accedidos, sin embargo, la actividad originada desde la IP del adversario inicia a las 16:30

![[dns.png]]

Esa entrada de color negro es el primer registro de la IP del sitio de phishing a la que se realiza una conexión, posteriormente hay una serie de consultar DNS como se ve en la imagen, que finalmente son resueltas y se devuelve el dominio malicioso.

![[malURL01.png]]

Después, las víctimas inician sesión pensando que están autenticándose al portal de Office de la empresa y envían mediante HTTP sus credenciales al servidor del atacante, encodeadas en base64 (el contenido decodificado está en la imagen a la altura de las *Request URI*)

Posteriormente, el atacante se aprovecha de las débiles políticas de autenticación de la compañía (falta de autenticación MFA), para ganar acceso a las cuentas de los empleados que han caído previamente en el engaño. Una vez los atacantes tienen acceso a la infraestructura de la organización con las autorizaciones de aquellos empleados víctimas del ataque, los atacantes pueden acceder a todos los activos de la organización a los cuales dichos empleados tienen acceso legítimo, y proceder a su exfiltración, ya sea con fines de daño reputacional, económico o estratégico.

#### Análisis de los riesgos potenciales en base al incidente de Australia
Tras la brecha inicial en el ataque de phishing acontecido en la sede de Australia, hay varias vías mediante las cuales el atacante podría empeorar la situación o prolongar su intrusión en la infraestructura.
1. En primer lugar, si no se lidia con la amenaza de forma eficaz y rápida, la exfiltración podría continuar, resultando en mayor daño reputacional, económico o estratégico.
2. Además, dependiendo de la naturaleza de la exfiltración, la empresa podría estar cometiendo delitos de incumplimiento de protección de datos según la legislación vigente, resultando en aún más daños reputacionales y multas.
3. Además, si al atacante (aún infiltrado) logra dar con un vector de escalada y consigue elevar sus privilegios, podría llegar a cometer otros delitos como el caso de ransomware visto en España.
4. Si la respuesta al incidente no es suficiente, el atacante podría mantener su presencia en la organización y volver al ataque el en futuro
<div class="page-break" style="page-break-before: always;"></div>

### Análisis del ciberincidente en la sede de Madrid
El evento acontecido en la infraestructura de la sede Española es un caso de ransomware. Aprovechándose de un acceso inicial que está aún por diagnosticar, un atacante o grupo de atacantes han ganado acceso a los sistemas de la compañía. Una vez conseguido el acceso privilegiado, ya sea mediante escalada o una política de autorización débil, los atacantes han logrado conseguir permisos de administrador y han encriptado todos los archivos, haciéndolos totalmente irrecuperables sin la llave adecuada.

El objetivo del ataque es mantener toda la información valiosa contenida en esos archivos como "rehén", y ahora el atacante solicita un rescate a la compañía a cambio de la llave con la que recuperar esos archivos.

![[spain_recover_files_message.PNG|784x553]]

En este caso, se ha utilizado una doble capa de encriptado combinando AES256bit y RSA con llave 2048 bits, en la imagen se aprecia también la dirección a la que el atacante indica que debe realizarse el pago.

<div class="page-break" style="page-break-before: always;"></div>

## Sobre el incidente en Italia

Dependiendo de lo crítico que sea mantener las operaciones para las cuáles se requieren los sistemas afectados, puede no merecer la pena su desconexión. La decisión depende del plan de continuidad de negocio pertinente, en cualquier caso, lo recomendable sería desconectar el equipo para evitar que el atacante pueda pivotar por la red y expandir su alcance.
En caso de que tras evaluar el plan de continuidad de negocio, se decida desconectar el equipo, es necesario clonar sus unidades de almacenamiento, tanto permanente como volátil, así como las del servidor de logs, ya que pueden contener información valiosa sobre el incidente y si el atacante ya ha logrado pivotar la integridad de estos registros corre peligro.
<div class="page-break" style="page-break-before: always;"></div>

## Diagnóstico incidente Australia
Se ha desarrollado un script en `python` que parsea el logfile del tráfico de red y lo muestra por salida de CLI, está adjunto en la entrega al final de este documento.

### Correos afectados
Analizando el tráfico de la página, eventualmente se da con el dominio malicioso. Esto se consigue mediante el análisis manual del registro de la red vía *Wireshark*, se analiza este tráfico hasta dar con una petición ``GET`` muy sospechosa. Marcada en negro en la imagen

![[malo.png|1118x139]]

A partir de esa información se identifican el dominio del atacante junto con su IP, además de la información exilftrada.

```
suspicious.microsoft.logon.fadel.id
```

Asociado a la IP

```
49.50.8.230
```

![[malicious-url.png]]

A partir de la información contenida en el paquete encontrado, podemos deducir que la query que se está enviando mediante `GET` al servidor del atacante está encodeada en base64, al desencodearla, nos encontramos con lo siguiente:

```
mgarcia@invent.com:manzana123
https://pastebin.com/2R0Fem3C
```

De esta información se elabora la siguiente hipótesis: Los empleados víctima del phishing reciben un email que contiene un enlace aparentemente legítimo al portal de ofimática corporativo de Invent S.L.

El panel de login, es en realidad un sitio malicioso controlado por el atacante que envía por `GET` las credenciales del empleado al atacante con el formato `usuario:contraseña`. Además, el enlace contiene un enlace de `pastebin`, una web en la que se pueden compartir de forma anónima bloques de texto, al acceder a ese enlace vemos otros tres correos

```
hifid@invent.com:123dmr

hjerfs@invent.com:applepup

jdarwin@invent.com:redcar#
```

De esta investigación se deduce la siguiente lista de correos afectados:
- mgarcia@invent.com
- hifid@invent.com
- hjerfs@invent.com
- jdarwin@invent.com <div class="page-break" style="page-break-before: always;"></div>

## Diagnóstico incidente Madrid
### ¿Cómo funciona?
Tras una brecha inicial, sea cual sea el vector de ataque/escalada, el atacante consigue los privilegios necesarios para encriptar todos los archivos (o archivos necesarios para el funcionamiento del sistema) de uno o varios equipos. Esto hace que el equipo quede totalmente inservible y su información irrecuperable, entonces, el atacante pide un rescate a cambio de la llave para recuperar información.

### ¿Es posible la recuperación?

En todos los casos de ransomware, siempre existe la opción de pagar el rescate. Evidentemente, no es la mejor opción, ya que sin conocer la reputación del atacante no hay garantías de que se pueda recuperar la información aún después de pagar.

Basándome en la captura de la notificación del ransomware, se ha determinado que se trata de la distribución NM4, en base a comparar la captura proporcionada con otras investigaciones previas en Internet.
Gracias a identificar la familia de ransomware se averigua que se puede revertir el ataque con relativa sencillez siguiendo unos pasos o con software de terceros.
En lo que respecta a los archivos, lo ideal sería restaurarlos desde una copia de seguridad previamente hecha, de todas formas, en caso de que no haya tal cosa, hay otras opciones:
1. **Software de terceros para recuperación de archivos**
2. **System Restore Point**
3. **File History**

### Diagnóstico del vector de entrada

![[spain.jpg]]

Un servidor SMB en el puerto 139 ha quedado expuesto a Internet, el atacante probablemente ha recopilado toda la información posible de ese servidor y la ha aprovechado para ganar acceso no autorizado a los sistemas (probablemente mediante credenciales recicladas o alguna otra información similar).
<div class="page-break" style="page-break-before: always;"></div>

## Prevención
### Australia
Algunas medidas que podrían implementarse para evitar que vuelva a suceder algo así:
1. Formación periódica y concienciación de los empleados.
2. Implementación de firewalls que bloqueen dominios e IPs no confiados.
3. Aplicar el principio de cero confianza: Desconfiar de toda acción, solicitud, input o paquete hasta que se verifique su legitimidad o venga de una fuente confiada.
4. Implementación de firewalls host-based en cada equipo, operan en la capa 7 del modelo OSI y aumentan la protección frente a este tipo de ataques.
5. Auditar mediante terceros frecuentemente la organización

### Madrid
Respecto al incidente de Madrid:
1. Aplicar el principio de cero confianza: Desconfiar de toda acción, solicitud, input o paquete hasta que se verifique su legitimidad o venga de una fuente confiada.
2. No exponer a Internet todo servicio que no sea absolutamente necesario.
3. Implementar soluciones IDS (Intrusion Detection System) o IPS (Intrusion Prevention System) para proteger la infraestructura en caso de haber una brecha inicial.
4. Revisar y asegurar que se cumple con las políticas de empresa, el plan de respuesta a incidentes y continuidad de negocio.
5. Revisar y mejorar los sistemas para reforzar el principio AAA (Autenticación, Autorización y Responsabilidad).
6. Hacer copias de seguridad frecuentes y mantener el software parcheado y al día.
7. Auditar mediante terceros frecuentemente la organización.

<div class="page-break" style="page-break-before: always;"></div>

<div class="page-break" style="page-break-before: always;"></div>

# Script Python
```python
from scapy.all import *
import sys
from collections import defaultdict
import re

class AdvancedPCAPAnalyzer:
    def __init__(self):
        self.conversations = defaultdict(lambda: {
            'packets': 0,
            'bytes': 0,
            'protocols': set(),
            'ports': set()
        })
        self.http_requests = []
        self.http_responses = []
        self.protocol_stats = defaultdict(int)
    
    def parse_http_data(self, payload):
        """Analiza datos HTTP de la carga útil"""
        try:
            http_data = payload.decode('utf-8', errors='ignore')
            lines = http_data.split('\r\n')
            
            http_info = {
                'method': '',
                'url': '',
                'status': '',
                'host': '',
                'user_agent': '',
                'content_type': '',
                'content_length': ''
            }
            
            for line in lines:
                if line.startswith(('GET', 'POST', 'PUT', 'DELETE', 'HEAD', 'OPTIONS')):
                    parts = line.split(' ')
                    if len(parts) >= 2:
                        http_info['method'] = parts[0]
                        http_info['url'] = parts[1]
                elif line.startswith('HTTP/'):
                    http_info['status'] = line
                elif line.lower().startswith('host:'):
                    http_info['host'] = line[5:].strip()
                elif line.lower().startswith('user-agent:'):
                    http_info['user_agent'] = line[11:].strip()
                elif line.lower().startswith('content-type:'):
                    http_info['content_type'] = line[13:].strip()
                elif line.lower().startswith('content-length:'):
                    http_info['content_length'] = line[15:].strip()
            
            return http_info
        except Exception as e:
            return {'error': str(e)}
    
    def analyze_pcap(self, file_path):
        try:
            # Leer el archivo pcap
            packets = rdpcap(file_path)
            
            print("=== ANÁLISIS COMPLETO PCAP ===")
            print(f"Archivo: {file_path}")
            print(f"Total de paquetes: {len(packets)}")
            print("=" * 80)
            
            # Iterar sobre cada paquete
            for i, packet in enumerate(packets):
                print(f"\nPaquete {i+1}:")
                
                # Información básica del paquete
                if packet.haslayer(Ether):
                    eth = packet[Ether]
                    print(f"  MAC Origen: {eth.src}")
                    print(f"  MAC Destino: {eth.dst}")
                
                # Información IP
                if packet.haslayer(IP):
                    ip = packet[IP]
                    print(f"  IP Origen: {ip.src}")
                    print(f"  IP Destino: {ip.dst}")
                    print(f"  Protocolo: {ip.proto}")
                    print(f"  TTL: {ip.ttl}")
                    
                    # Actualizar estadísticas de conversaciones
                    key = f"{ip.src} -> {ip.dst}"
                    self.conversations[key]['packets'] += 1
                    self.conversations[key]['bytes'] += len(packet)
                
                # Información TCP
                tcp_info_printed = False
                if packet.haslayer(TCP):
                    tcp = packet[TCP]
                    print(f"  Puerto Origen: {tcp.sport}")
                    print(f"  Puerto Destino: {tcp.dport}")
                    print(f"  Flags: {tcp.flags}")
                    tcp_info_printed = True
                    
                    # Actualizar protocolos y puertos
                    if packet.haslayer(IP):
                        key = f"{packet[IP].src} -> {packet[IP].dst}"
                        self.conversations[key]['protocols'].add('TCP')
                        self.conversations[key]['ports'].add(f"{tcp.sport}->{tcp.dport}")
                    
                    self.protocol_stats['TCP'] += 1
                
                # Información UDP
                if packet.haslayer(UDP):
                    udp = packet[UDP]
                    print(f"  Puerto Origen: {udp.sport}")
                    print(f"  Puerto Destino: {udp.dport}")
                    
                    if packet.haslayer(IP):
                        key = f"{packet[IP].src} -> {packet[IP].dst}"
                        self.conversations[key]['protocols'].add('UDP')
                        self.conversations[key]['ports'].add(f"{udp.sport}->{udp.dport}")
                    
                    self.protocol_stats['UDP'] += 1
                
                # Información ICMP
                if packet.haslayer(ICMP):
                    icmp = packet[ICMP]
                    print(f"  Tipo ICMP: {icmp.type}")
                    print(f"  Código ICMP: {icmp.code}")
                    
                    if packet.haslayer(IP):
                        key = f"{packet[IP].src} -> {packet[IP].dst}"
                        self.conversations[key]['protocols'].add('ICMP')
                    
                    self.protocol_stats['ICMP'] += 1
                
                # ANÁLISIS HTTP
                http_analyzed = False
                if packet.haslayer(TCP) and packet.haslayer(Raw):
                    tcp = packet[TCP]
                    # Buscar tráfico HTTP (puertos comunes)
                    if tcp.dport in [80, 8080, 443, 8000] or tcp.sport in [80, 8080, 443, 8000]:
                        payload = packet[Raw].load
                        http_info = self.parse_http_data(payload)
                        
                        if http_info.get('method') or http_info.get('status'):
                            print("  *** TRÁFICO HTTP DETECTADO ***")
                            http_analyzed = True
                            
                            if http_info['method']:
                                print(f"    Método: {http_info['method']}")
                                print(f"    URL: {http_info['url']}")
                                self.http_requests.append({
                                    'packet': i+1,
                                    'method': http_info['method'],
                                    'url': http_info['url'],
                                    'host': http_info['host']
                                })
                            
                            if http_info['status']:
                                print(f"    Respuesta: {http_info['status']}")
                                self.http_responses.append({
                                    'packet': i+1,
                                    'status': http_info['status']
                                })
                            
                            if http_info['host']:
                                print(f"    Host: {http_info['host']}")
                            if http_info['user_agent']:
                                print(f"    User-Agent: {http_info['user_agent']}")
                            if http_info['content_type']:
                                print(f"    Content-Type: {http_info['content_type']}")
                            if http_info['content_length']:
                                print(f"    Content-Length: {http_info['content_length']}")
                
                # Si no se detectó HTTP pero hay datos, mostrar información básica
                if not http_analyzed and packet.haslayer(Raw):
                    payload = packet[Raw].load
                    if len(payload) > 0:
                        print(f"  Datos de aplicación: {len(payload)} bytes")
                        # Mostrar preview de los datos
                        try:
                            preview = payload[:100].decode('utf-8', errors='ignore')
                            if any(keyword in preview.upper() for keyword in ['GET', 'POST', 'HTTP', 'HTML', 'JSON']):
                                print(f"    Preview: {preview[:50]}...")
                        except:
                            pass
                
                # Tamaño del paquete
                print(f"  Tamaño total: {len(packet)} bytes")
                print("-" * 80)
            
            # Mostrar resumen al final
            self.print_summary()
            
        except Exception as e:
            print(f"Error analizando el archivo: {e}")
    
    def print_summary(self):
        """Muestra un resumen completo del análisis"""
        print("\n" + "=" * 80)
        print("RESUMEN DEL ANÁLISIS")
        print("=" * 80)
        
        # Estadísticas de protocolos
        print("\n--- ESTADÍSTICAS DE PROTOCOLOS ---")
        for protocol, count in self.protocol_stats.items():
            print(f"  {protocol}: {count} paquetes")
        
        # Conversaciones de red
        print("\n--- CONVERSACIONES DE RED ---")
        for conv, stats in self.conversations.items():
            print(f"  {conv}")
            print(f"    Paquetes: {stats['packets']}")
            print(f"    Bytes totales: {stats['bytes']}")
            print(f"    Protocolos: {', '.join(stats['protocols'])}")
            if stats['ports']:
                print(f"    Puertos: {', '.join(list(stats['ports'])[:3])}")  # Mostrar solo primeros 3
        
        # Resumen HTTP
        if self.http_requests or self.http_responses:
            print("\n--- RESUMEN HTTP ---")
            print(f"  Peticiones HTTP: {len(self.http_requests)}")
            print(f"  Respuestas HTTP: {len(self.http_responses)}")
            
            if self.http_requests:
                print("\n  Últimas peticiones HTTP:")
                for req in self.http_requests[-5:]:  # Mostrar últimas 5
                    print(f"    Paquete {req['packet']}: {req['method']} {req['url']}")

def main():
    if len(sys.argv) != 2:
        print("Uso: python pcap_analyzer.py <archivo.pcap>")
        print("Ejemplo: python pcap_analyzer.py captura.pcap")
        sys.exit(1)
    
    analyzer = AdvancedPCAPAnalyzer()
    analyzer.analyze_pcap(sys.argv[1])

if __name__ == "__main__":
    main()
```