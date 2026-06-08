Hydra (THC-Hydra) es un cracker de autenticación paralelizado que soporta múltiples protocolos de red para ataques de diccionario y fuerza bruta. Está diseñado para pruebas de penetración autorizadas y verificación de la robustez de credenciales en servicios como SSH, FTP, HTTP, SMB, RDP, MySQL, LDAP y decenas más. Su arquitectura multihilo permite velocidades de ataque elevadas.

```
hydra [opciones] [-s puerto] [-l login|-L archivo] [-p pass|-P archivo] [-x MIN:MAX:CHARSET] [-e nsr] [servicio://servidor[:puerto][/OPC]]
hydra [opciones] [-s puerto] [-l login|-L archivo] [-p pass|-P archivo] [-x MIN:MAX:CHARSET] [-e nsr] -M archivo [servicio]
```

---

## Especificación de objetivo

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `<servicio://servidor>` | Objetivo directo en la línea de comandos. | `hydra ssh://10.0.0.1` |
| `-M <archivo>` | Lista de objetivos desde un archivo (uno por línea). | `hydra -M hosts.txt ssh` |
| `-s <puerto>` | Puerto alternativo al por defecto del protocolo. | `hydra -s 2222 10.0.0.1 ssh` |
| `-S` | Conexión SSL. | `hydra -S -s 443 10.0.0.1 https-get /` |
| `-6` | Modo IPv6. | `hydra -6 ssh://[::1]` |
| `-m <opciones>` | Opciones adicionales específicas del módulo. Separadas por `/`. | `hydra -m "/login.php:user=^USER^&pass=^PASS^:Invalid" http-post-form` |

---

## Credenciales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l <login>` | Un solo nombre de usuario. | `hydra -l admin 10.0.0.1 ssh` |
| `-L <archivo>` | Lista de usuarios desde archivo. | `hydra -L users.txt 10.0.0.1 ftp` |
| `-p <pass>` | Una sola contraseña. | `hydra -l root -p toor 10.0.0.1 ssh` |
| `-P <archivo>` | Lista de contraseñas desde archivo. | `hydra -l admin -P rockyou.txt 10.0.0.1 ssh` |
| `-C <archivo>` | Pares usuario:contraseña desde archivo (formato `login:pass` una entrada por línea). | `hydra -C credentials.txt 10.0.0.1 ftp` |
| `-e n` | Prueba contraseña vacía (ninguna). | `hydra -l admin -e n 10.0.0.1 ssh` |
| `-e s` | Prueba el login como contraseña (same). | `hydra -l admin -e s 10.0.0.1 ssh` |
| `-e r` | Prueba el login en reverso como contraseña. | `hydra -l admin -e r 10.0.0.1 ssh` |
| `-e nsr` | Prueba las tres variantes anteriores combinadas. | `hydra -L users.txt -e nsr 10.0.0.1 ssh` |
| `-x MIN:MAX:CHARS` | Generación de contraseñas por fuerza bruta de longitud MIN a MAX con CHARS. Chars: `a`=alfa, `A`=ALFA, `1`=dígitos, o caracteres literales. | `hydra -l root -x 4:6:aA1 10.0.0.1 ssh` |
| `-y` | Deshabilita el uso de símbolos en la generación con `-x`. | `hydra -l root -x 4:6:aA1 -y 10.0.0.1 ssh` |

---

## Parámetros de ataque

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-f` | Para el ataque al encontrar las primeras credenciales válidas. | `hydra -l admin -P rockyou.txt -f 10.0.0.1 ftp` |
| `-F` | Para el ataque en todos los hosts al encontrar las primeras credenciales (con `-M`). | `hydra -M hosts.txt -L users.txt -P pass.txt -F ssh` |
| `-u` | Itera primero por usuarios para cada contraseña (por defecto: contraseña por usuario). | `hydra -L users.txt -P pass.txt -u 10.0.0.1 ssh` |
| `-R` | Reanuda una sesión interrumpida (lee el fichero de sesión `hydra.restore`). | `hydra -R` |
| `-I` | Ignora el fichero de sesión existente (no espera los 10 segundos de aviso). | `hydra -l admin -P pass.txt -I 10.0.0.1 ssh` |

---

## Rendimiento

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-t <n>` | Número de tareas paralelas por objetivo (por defecto: 16). | `hydra -l admin -P pass.txt -t 4 10.0.0.1 ssh` |
| `-T <n>` | Número total de tareas paralelas (para uso con `-M`). | `hydra -M hosts.txt -L users.txt -P pass.txt -T 64 ssh` |
| `-w <n>` | Timeout de espera de respuesta en segundos (por defecto: 32). | `hydra -l admin -P pass.txt -w 10 10.0.0.1 ssh` |
| `-W <n>` | Tiempo de espera entre conexiones por hilo en segundos. | `hydra -l admin -P pass.txt -W 2 10.0.0.1 ftp` |
| `-c <n>` | Tiempo de espera global por intento de login en segundos (tasa de conexión global). | `hydra -l admin -P pass.txt -c 5 10.0.0.1 ssh` |

---

## Salida y logging

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-v` | Verbose: muestra intentos de login. | `hydra -l admin -P pass.txt -v 10.0.0.1 ssh` |
| `-V` | Verbose máximo: muestra usuario y contraseña en cada intento. | `hydra -l admin -P pass.txt -V 10.0.0.1 ftp` |
| `-d` | Debug: muestra información de depuración. | `hydra -l admin -P pass.txt -d 10.0.0.1 ssh` |
| `-q` | No imprime mensajes de error de conexión. | `hydra -l admin -P pass.txt -q 10.0.0.1 ssh` |
| `-o <archivo>` | Guarda las credenciales válidas encontradas en un archivo. | `hydra -l admin -P pass.txt -o found.txt 10.0.0.1 ftp` |
| `-b <formato>` | Formato del archivo de salida: `text` (por defecto), `jsonv1`, `json`. | `hydra -l admin -P pass.txt -o found.json -b json 10.0.0.1 ssh` |
| `-r <archivo>` | Fichero de restauración de sesión personalizado (por defecto: `hydra.restore`). | `hydra -l admin -P pass.txt -r mysession.restore 10.0.0.1 ssh` |

---

## Módulo HTTP — http-get / http-post-form

### http-get y https-get (HTTP Basic Auth)

```bash
# GET con autenticación básica
hydra -l admin -P pass.txt http-get://10.0.0.1/recurso

# HTTPS Basic Auth
hydra -l admin -P pass.txt https-get://10.0.0.1/admin/
```

### http-post-form (formularios de login)

La sintaxis del módulo es: `"/ruta:parámetros:string_de_fallo"` o `"/ruta:parámetros:F=string_de_fallo"` / `"S=string_de_exito"`.

```bash
# Formulario POST con detección por cadena de error
hydra -l admin -P rockyou.txt 10.0.0.1 http-post-form "/login:username=^USER^&password=^PASS^:Invalid credentials"

# Formulario POST con detección por cadena de éxito
hydra -l admin -P rockyou.txt 10.0.0.1 http-post-form "/login:user=^USER^&pass=^PASS^:S=Dashboard"

# Con cookie de sesión requerida
hydra -l admin -P pass.txt 10.0.0.1 http-post-form "/login:user=^USER^&pass=^PASS^:F=error:H=Cookie: PHPSESSID=abc123"

# Con encabezado personalizado
hydra -l admin -P pass.txt 10.0.0.1 http-post-form "/login:user=^USER^&pass=^PASS^:F=error:H=X-Forwarded-For: 127.0.0.1"
```

---

## Protocolos soportados (selección)

| Protocolo | Sintaxis | Ejemplo |
|-----------|----------|---------|
| SSH | `ssh` | `hydra -l root -P pass.txt ssh://10.0.0.1` |
| FTP | `ftp` | `hydra -l admin -P pass.txt ftp://10.0.0.1` |
| SMB | `smb` | `hydra -l administrator -P pass.txt smb://10.0.0.1` |
| RDP | `rdp` | `hydra -l administrator -P pass.txt rdp://10.0.0.1` |
| Telnet | `telnet` | `hydra -l admin -P pass.txt telnet://10.0.0.1` |
| MySQL | `mysql` | `hydra -l root -P pass.txt mysql://10.0.0.1` |
| MSSQL | `mssql` | `hydra -l sa -P pass.txt mssql://10.0.0.1` |
| PostgreSQL | `postgres` | `hydra -l postgres -P pass.txt postgres://10.0.0.1` |
| LDAP | `ldap2` / `ldap3` | `hydra -l cn=admin -P pass.txt ldap3://10.0.0.1` |
| SMTP | `smtp` | `hydra -l user@domain -P pass.txt smtp://10.0.0.1` |
| IMAP | `imap` | `hydra -l user -P pass.txt imap://10.0.0.1` |
| POP3 | `pop3` | `hydra -l user -P pass.txt pop3://10.0.0.1` |
| Redis | `redis` | `hydra -l "" -P pass.txt redis://10.0.0.1` |
| VNC | `vnc` | `hydra -l "" -P pass.txt vnc://10.0.0.1` |
| HTTP Basic Auth | `http-get` | `hydra -l admin -P pass.txt http-get://10.0.0.1/protected/` |
| HTTP Form | `http-post-form` | Ver sección anterior. |

---

## Casos de uso comunes

```bash
# SSH con lista de usuarios y contraseñas, 4 threads, parar al primer éxito
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt -t 4 -f ssh://10.0.0.1

# FTP con usuario fijo
hydra -l ftpuser -P pass.txt -t 8 ftp://192.168.1.100

# HTTP Basic Auth en puerto no estándar
hydra -l admin -P rockyou.txt -s 8080 10.0.0.1 http-get /admin

# SMB con pares usuario:contraseña predefinidos
hydra -C creds.txt smb://10.0.0.1

# Múltiples hosts + guardar resultados
hydra -M targets.txt -L users.txt -P pass.txt -o results.txt -b jsonv1 ssh
```
