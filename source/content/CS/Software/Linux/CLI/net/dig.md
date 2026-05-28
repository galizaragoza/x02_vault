dig (Domain Information Groper) es la herramienta estándar para consultas DNS desde línea de comandos. Permite consultar cualquier tipo de registro DNS contra servidores específicos, seguir la cadena de delegación completa, realizar transferencias de zona y diagnosticar problemas de resolución. Produce salida detallada con las secciones de la respuesta DNS (ANSWER, AUTHORITY, ADDITIONAL) y las estadísticas de la consulta.

```
dig [@servidor] [nombre] [tipo] [+opciones] [-opciones]
dig -f <archivo_de_consultas>
```

---

## Objetivo de consulta

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `@<servidor>` | Servidor DNS a consultar (IP o nombre). Por defecto usa `/etc/resolv.conf`. | `dig @8.8.8.8 ejemplo.com` |
| `-p <puerto>` | Puerto del servidor DNS. Por defecto: 53. | `dig -p 5353 @localhost ejemplo.com` |
| `<nombre>` | Nombre de dominio a resolver. | `dig ejemplo.com` |
| `<tipo>` | Tipo de registro: `A`, `AAAA`, `MX`, `NS`, `TXT`, `CNAME`, `SOA`, `PTR`, `SRV`, `CAA`, `ANY`... Por defecto: `A`. | `dig ejemplo.com MX` |
| `-x <IP>` | Consulta DNS inversa (registro PTR). Construye automáticamente el nombre `in-addr.arpa`. | `dig -x 8.8.8.8` |
| `-4` | Usa solo IPv4 para el transporte. | `dig -4 ejemplo.com` |
| `-6` | Usa solo IPv6 para el transporte. | `dig -6 ejemplo.com AAAA` |
| `-f <archivo>` | Lee nombres a consultar desde un archivo (uno por línea). | `dig -f dominios.txt` |

---

## Opciones de comportamiento (`+`)

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `+short` | Muestra solo la respuesta final (sin cabeceras ni secciones). | `dig ejemplo.com +short` |
| `+noall +answer` | Muestra solo la sección ANSWER. | `dig ejemplo.com +noall +answer` |
| `+trace` | Traza la resolución desde los servidores raíz, siguiendo delegaciones. | `dig ejemplo.com +trace` |
| `+recurse` / `+norecurse` | Activa/desactiva el bit RD (recursión deseada). Por defecto activo. | `dig @ns1.ejemplo.com ejemplo.com +norecurse` |
| `+tcp` | Usa TCP en lugar de UDP. Necesario para respuestas grandes (>512 bytes sin EDNS). | `dig ejemplo.com +tcp` |
| `+time=<N>` | Timeout en segundos por intento. Por defecto: 5. | `dig ejemplo.com +time=2` |
| `+tries=<N>` | Número de intentos. Por defecto: 3. | `dig ejemplo.com +tries=1` |
| `+retry=<N>` | Número de reintentos (excluyendo el primer intento). | `dig ejemplo.com +retry=2` |
| `+dnssec` | Solicita registros DNSSEC (activa bit DO en la consulta). | `dig ejemplo.com +dnssec` |
| `+multiline` | Muestra registros en formato multilínea (más legible para SOA, DNSKEY, etc.). | `dig ejemplo.com SOA +multiline` |
| `+stats` / `+nostats` | Muestra/oculta las estadísticas de la consulta (tiempo, servidor, tamaño). | `dig ejemplo.com +stats` |
| `+question` / `+noquestion` | Muestra/oculta la sección QUESTION. | `dig ejemplo.com +noquestion` |
| `+authority` / `+noauthority` | Muestra/oculta la sección AUTHORITY. | `dig ejemplo.com +noauthority` |
| `+additional` / `+noadditional` | Muestra/oculta la sección ADDITIONAL. | `dig ejemplo.com +noadditional` |
| `+comments` / `+nocomments` | Muestra/oculta los comentarios de cabecera. | `dig ejemplo.com +nocomments` |
| `+all` / `+noall` | Activa/desactiva todas las secciones de salida. Base para combinaciones con `+answer`. | `dig ejemplo.com +noall +answer +authority` |

---

## Transferencia de zona y consultas especiales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `AXFR` | Transferencia de zona completa (requiere que el servidor lo permita). | `dig @ns1.ejemplo.com ejemplo.com AXFR` |
| `IXFR=<serial>` | Transferencia de zona incremental desde el serial indicado. | `dig @ns1.ejemplo.com ejemplo.com IXFR=2024010101` |
| `ANY` | Solicita todos los registros disponibles (servidores modernos suelen ignorarlo). | `dig ejemplo.com ANY` |

---

## Tipos de registro frecuentes

| Tipo | Descripción |
|------|-------------|
| `A` | Dirección IPv4. |
| `AAAA` | Dirección IPv6. |
| `MX` | Servidores de correo (con prioridad). |
| `NS` | Servidores de nombres autoritativos. |
| `TXT` | Registros de texto (SPF, DKIM, verificación de dominio). |
| `CNAME` | Alias canónico. |
| `SOA` | Start of Authority (info del servidor primario y parámetros de zona). |
| `PTR` | Resolución inversa (IP → nombre). |
| `SRV` | Localización de servicios (puerto, peso, prioridad). |
| `CAA` | Autoridades de certificación permitidas. |
| `DNSKEY` | Clave pública DNSSEC. |
| `DS` | Delegation Signer (hash de DNSKEY del hijo). |

---

## Casos de uso comunes

```bash
# Resolución básica
dig ejemplo.com

# Respuesta corta (solo IP)
dig ejemplo.com +short

# Servidor de nombres específico
dig @1.1.1.1 ejemplo.com

# Registros MX
dig ejemplo.com MX +short

# Registros TXT (SPF, DKIM)
dig ejemplo.com TXT

# DNS inverso
dig -x 8.8.8.8 +short

# Trazar delegación completa
dig ejemplo.com +trace

# Transferencia de zona (si está permitida)
dig @ns1.ejemplo.com ejemplo.com AXFR

# Solo sección de respuesta, sin ruido
dig ejemplo.com +noall +answer

# SOA legible
dig ejemplo.com SOA +multiline

# Verificar DNSSEC
dig ejemplo.com +dnssec

# Consultar via TCP
dig ejemplo.com +tcp

# Todos los NS del TLD
dig com NS +short
```
