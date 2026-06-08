# Sintaxis Básica de los Filtros

| **Componente** | **Descripción**             | **Ejemplo**                                  |
| -------------- | --------------------------- | -------------------------------------------- |
| **Protocolo**  | Capa o protocolo específico | `ip`, `tcp`, `http`, `dns`                   |
| **Campo**      | Atributo del protocolo      | `ip.addr`, `tcp.port`, `http.request.method` |
| **Operador**   | Relación lógica             | `==`, `!=`, `>`, `contains`, `matches`       |
| **Valor**      | El dato que buscas          | `192.168.1.1`, `80`, `"GET"`                 |

# Common

| **Categoría** | **Filtro**                      | **Descripción**                            |
| ------------- | ------------------------------- | ------------------------------------------ |
| **IP**        | `ip.addr == 10.0.0.1`           | Tráfico desde o hacia esa IP               |
| **Subred**    | `ip.addr == 192.168.1.0/24`     | Tráfico de toda una red                    |
| **TCP**       | `tcp.port == 443`               | Tráfico HTTPS (puerto 443)                 |
| **UDP**       | `udp.port == 53`                | Consultas/respuestas DNS                   |
| **HTTP**      | `http.request.method == "POST"` | Solo peticiones de envío de datos          |
| **Flags**     | `tcp.flags.syn == 1`            | Paquetes de inicio de conexión (Handshake) |
# Logical ops

| **Operador** | **Alternativa** | **Significado**           | **Ejemplo**                         |
| ------------ | --------------- | ------------------------- | ----------------------------------- |
| `==`         | `eq`            | Igual a                   | `ip.src == 1.1.1.1`                 |
| `!=`         | `ne`            | No es igual a             | `tcp.port != 80`                    |
| `&&`         | `and`           | Y (ambas deben cumplirse) | `ip.addr == 10.0.0.1 && icmp`       |
| `\|`         | `or`            | O (una de las dos)        | `tcp.port == 80 \| tcp.port == 443` |
| `!`          | `not`           | Negación                  | `!dns` (Oculta todo el tráfico DNS) |
# Advanced

| **Técnica**    | **Filtro / Acción**                | **Uso**                                     |
| -------------- | ---------------------------------- | ------------------------------------------- |
| **Excluir IP** | `!(ip.addr == 192.168.1.5)`        | Ver todo menos lo de esa IP                 |
| **Contenido**  | `frame contains "password"`        | Busca la palabra en todo el paquete         |
| **Regex**      | `http.host matches "\.(com\|net)"` | Filtra dominios que terminen en .com o .net |
| **Rango**      | `tcp.port >= 1024`                 | Solo puertos efímeros / altos               |

# Protips
|**Función**|**Cómo hacerlo**|**Utilidad**|
|---|---|---|
|**Follow Stream**|Click derecho > `Follow` > `TCP Stream`|Reconstruye la conversación completa (útil para ver texto plano)|
|**Coloring Rules**|`View` > `Coloring Rules`|Personaliza colores para detectar errores (RST, Retransmissions)|
|**Endpoints**|`Statistics` > `Endpoints`|Lista de todas las IPs y puertos que están hablando|
|**Conversaciones**|`Statistics` > `Conversations`|Ver quién habla con quién y cuántos bytes intercambian|
|**GeoIP**|Configurar en `Preferences`|Localiza geográficamente las IPs en un mapa|