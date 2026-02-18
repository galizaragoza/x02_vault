Fuzzer web con distintas capacidades: Descubrimiento de dirs/archivos, bypass 403/401, fuzzing de parámetros, bruteforce de login/forms, enumeración VHost y GraphQL/API testing.

```bash
wfuzz -c -z tipo,payload --hc/hl/hh N URL/FUZZ
```

| Parámetro            | Función                  | Ejemplo de comando completo                            |
| -------------------- | ------------------------ | ------------------------------------------------------ |
| `-c`                 | Salida coloreada         | `wfuzz -c -z file,common.txt https://target/FUZZ`      |
| `-z tipo,payload`    | Payload                  | `-z file,/usr/share/wordlists/dirb/common.txt`         |
| `-w diccionario`     | Archivo wordlist         | `-w /usr/share/seclists/Discovery/Web-Content/big.txt` |
| `-t N`               | Threads                  | `wfuzz -t 50 ...`                                      |
| `--hc N`             | Ocultar código respuesta | `--hc 404`                                             |
| `--hl N`             | Ocultar líneas           | `--hl 15`                                              |
| `--hw N`             | Ocultar palabras         | `--hw 250`                                             |
| `--hh N`             | Ocultar caracteres       | `--hh 1200`                                            |
| `-b cookie=value`    | Enviar cookie            | `-b "PHPSESSID=abc123"`                                |
| `-H "Header: value"` | Header personalizado     | `-H "X-Forwarded-For: 127.0.0.1"`                      |
| `-X METODO`          | Método HTTP              | `-X POST`                                              |
| `-d postdata`        | Datos POST               | `-d "user=FUZZ&pass=FUZ2Z"`                            |
| `--proxy IP:PORT`    | Proxy HTTP               | `--proxy 127.0.0.1:8080`                               |
| `-R N`               | Profundidad recursiva    | `-R 2`                                                 |
| `-p proxy:mode`      | Proxy (socks, etc.)      | `-p 127.0.0.1:1080:socks5`                             |
