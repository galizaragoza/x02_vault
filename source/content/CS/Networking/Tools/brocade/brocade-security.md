**Seguridad** en Fabric OS: gestión de cuentas de usuario y roles (RBAC), autenticación centralizada AAA (RADIUS/LDAP/TACACS+), filtrado IP del plano de gestión, políticas ACL de fabric (DCC/SCC/FCS), autenticación de switches y dispositivos por DH-CHAP, gestión de certificados/claves SSH y modo FIPS. Endurecer estos puntos es esencial porque un switch comprometido expone toda la SAN. Véase el índice en [[brocade-fos]]; el control de visibilidad entre dispositivos está en [[brocade-zoning]].

---

# Usuarios, roles y contraseñas

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `userConfig --show -a` | Lista todas las cuentas de usuario y sus roles. | `userConfig --show -a` |
| `userConfig --add` | Crea una cuenta con rol y ámbito. | `userConfig --add operador -r operator -l 1-128 -h 128` |
| `userConfig --change` | Modifica una cuenta (rol, ámbito, estado). | `userConfig --change operador -r admin` |
| `userConfig --delete` | Elimina una cuenta. | `userConfig --delete operador` |
| `passwd` | Cambia la contraseña del usuario actual o de otro. | `passwd operador` |
| `passwdCfg --set` | Configura la política de contraseñas (longitud, caducidad, historial). | `passwdCfg --set -minlength 12 -maxage 90` |
| `roleConfig --show` | Lista los roles RBAC y sus permisos. | `roleConfig --show -all` |
| `roleConfig --add` | Crea un rol personalizado. | `roleConfig --add zoner -class zoning -perm RW` |

---

# AAA: autenticación centralizada

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `aaaConfig --show` | Muestra la configuración AAA actual. | `aaaConfig --show` |
| `aaaConfig --add` | Añade un servidor RADIUS/LDAP/TACACS+. | `aaaConfig --add 10.0.0.5 -conf radius -p 1812 -s secreto` |
| `aaaConfig --change` | Modifica un servidor AAA. | `aaaConfig --change 10.0.0.5 -t 5` |
| `aaaConfig --remove` | Elimina un servidor AAA. | `aaaConfig --remove 10.0.0.5 -conf radius` |
| `aaaConfig --authspec` | Define el orden de autenticación (local/remoto). | `aaaConfig --authspec "radius;local"` |
| `ldapCfg` | Mapea grupos LDAP/AD a roles FOS. | `ldapCfg --maprole "SAN-Admins" admin` |

---

# Filtrado IP del plano de gestión

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `ipFilter --show` | Muestra las políticas de filtro IP y la activa. | `ipFilter --show` |
| `ipFilter --create` | Crea una política de filtro IP. | `ipFilter --create mgmt -type ipv4` |
| `ipFilter --addrule` | Añade una regla (permitir/denegar por IP/puerto/proto). | `ipFilter --addrule mgmt -rule 1 -sip 10.0.0.0/24 -dp 22 -proto tcp -act permit` |
| `ipFilter --activate` | Activa una política de filtro IP. | `ipFilter --activate mgmt` |
| `ipFilter --delete` | Elimina una política. | `ipFilter --delete mgmt` |

---

# Políticas ACL de fabric

Controlan qué switches y conexiones se admiten en la fabric (no confundir con zoning).

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `secPolicyShow` | Muestra las políticas de seguridad definidas y activas. | `secPolicyShow` |
| `secPolicyCreate` | Crea una política ACL: `DCC` (Device Connection), `SCC` (Switch Connection), `FCS` (Fabric Configuration Server). | `secPolicyCreate "SCC_POLICY", "10:00:..;10:00:.."` |
| `secPolicyActivate` | Activa las políticas definidas en toda la fabric. | `secPolicyActivate` |
| `secPolicyAdd` / `secPolicyRemove` | Añade/quita miembros de una política. | `secPolicyAdd "DCC_POLICY_001", "12", "10:00:.."` |
| `fddCfg --fabwideset` | Define qué políticas se distribuyen a toda la fabric. | `fddCfg --fabwideset "SCC:S;DCC:S"` |

---

# DH-CHAP: autenticación de switches y dispositivos

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `authUtil --show` | Muestra la configuración de autenticación de enlaces. | `authUtil --show` |
| `authUtil --set` | Configura grupo DH, protocolo y política de autenticación. | `authUtil --set -a dhchap -g 1` |
| `authUtil --policy` | Fija la política E_Port/F_Port (on/off/passive/active). | `authUtil --policy -sw active` |
| `secAuthSecret --set` | Establece el secreto compartido DH-CHAP con un peer. | `secAuthSecret --set` |
| `secAuthSecret --show` | Lista los WWN con secreto configurado. | `secAuthSecret --show` |

---

# Certificados, SSH y FIPS

| Comando | Función | Ejemplo |
|---------|---------|---------|
| `seccertmgmt show` | Muestra los certificados instalados (HTTPS/LDAP/Radius/syslog). | `seccertmgmt show -cert all` |
| `seccertmgmt import` | Importa un certificado/clave desde un host. | `seccertmgmt import -cert commoncert -ipaddr 10.0.0.9 ...` |
| `seccertmgmt generate` | Genera un CSR o certificado autofirmado. | `seccertmgmt generate -cert https` |
| `sshutil showhostkey` | Muestra la clave de host SSH. | `sshutil showhostkey` |
| `sshutil importpubkey` | Importa una clave pública para login por clave. | `sshutil importpubkey` |
| `sshutil allowuser` | Restringe el acceso SSH a un usuario. | `sshutil allowuser admin` |
| `fipscfg --show` | Estado del modo FIPS 140-2. | `fipscfg --show` |
| `fipscfg --enable fips` | Activa el modo FIPS (deshabilita algoritmos débiles). | `fipscfg --enable fips` |
| `ipfilter` | (ver sección filtrado IP) restringe servicios de gestión. | `ipFilter --show` |

> Endurecimiento mínimo recomendado: AAA con `authspec "radius;local"`, `passwdCfg` estricto, `ipFilter` limitando SSH/HTTPS a la red de gestión, syslog remoto ([[brocade-diagnostics]]) y políticas `SCC`/`DCC` activas para impedir switches/dispositivos no autorizados.
