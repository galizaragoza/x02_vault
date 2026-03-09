Herramienta con la que hacer consultas DNS, permite enumeración de subdominios, detección de servers DNS, zone transfer, búsqueda de registros...

```bash
nslookup [opción] [nombre|IP] [servidor]
```

| Parámetro                              | Función                               |
| -------------------------------------- | ------------------------------------- |
| `nslookup`                             | Modo interactivo                      |
| `nslookup dominio`                     | Consulta rápida (usa DNS por defecto) |
| `nslookup -type=AXFR dominio servidor` | Intento de zone transfer              |
| `-type=A`                              | Registro A (IPv4)                     |
| `-type=AAAA`                           | Registro AAAA (IPv6)                  |
| `-type=MX`                             | Mail servers                          |
| `-type=TXT`                            | Registros TXT (SPF, keys, etc.)       |
| `-type=NS`                             | Name servers del dominio              |
| `-type=SOA`                            | Start of Authority                    |
| `-type=PTR`                            | Reverse lookup (IP → nombre)          |
| `-query=port=53`                       | Puerto DNS personalizado              |
| `-debug`                               | Muestra respuesta completa y flags    |
| `server IP`                            | Cambia al servidor DNS especificado   |
| `set timeout=N`                        | Timeout en segundos (interactivo)     |
