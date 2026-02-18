Herramienta criptográfica todo-en-uno: TLS/SSL, generación/claves/certificados, hashing, cifrado simétrico/asimétrico.

```
openssl comando [opciones]
```

# SSL/TLS
| **SSL/TLS Testing**      | **Commands**                        | **Ejemplo / Uso típico**                                             |
| ------------------------ | ----------------------------------- | -------------------------------------------------------------------- |
| `s_client`               | Conexión TLS/SSL manual             | `openssl s_client -connect target:443`                               |
| `-servername`            | SNI                                 | `-servername domain.com`                                             |
| `-tls1_2` / `-tls1`      | Forzar versión TLS                  | `-tls1` (solo TLS 1.0)                                               |
| `-quiet` / `-crlf`       | Silencioso + CRLF                   | `-quiet`                                                             |
| `< /dev/null`            | Banner rápido sin espera            | `openssl s_client -connect 10.10.10.10:443 -quiet < /dev/null`       |
| `-showcerts`             | Mostrar toda la cadena              | `-showcerts`                                                         |
| `-tlsextdebug`           | Detectar Heartbleed (TLS extension) | `-tlsextdebug`                                                       |
# Certificados
| **Certificados**         | **Commands**                  | **Ejemplo / Uso típico**                                             |
| ------------------------ | ----------------------------- | -------------------------------------------------------------------- |
| `x509`                   | Ver certificado               | `openssl x509 -in cert.crt -text -noout`                             |
| `-dates`                 | Solo fechas                   | `-dates`                                                             |
| `-subject` / `-issuer`   | Subject/Issuer                | `-subject`                                                           |
| `-fingerprint`           | Fingerprint SHA1/SHA256       | `-fingerprint -sha256`                                               |
| `req`                    | Ver CSR                       | `openssl req -in request.csr -text -noout`                           |
| `verify`                 | Verificar cadena              | `openssl verify -CAfile ca.crt cert.crt`                             |
# Claves y CLR
| **Claves & CSR**         | **Commands**                  | **Ejemplo / Uso típico**                                             |
| ------------------------ | ----------------------------- | -------------------------------------------------------------------- |
| `genpkey`                | Generar clave privada moderna | `openssl genpkey -algorithm ed25519 -out priv.key`                   |
| `rsa`                    | Convertir/Extraer RSA         | `openssl rsa -in priv.pem -out rsa.key`                              |
| `req -new`               | Crear CSR                     | `openssl req -new -key priv.key -out new.csr -subj "/CN=target"`     |
| `pkcs12`                 | Extraer de .pfx/.p12          | `openssl pkcs12 -in cert.pfx -nodes -out key+cert.pem`               |
| `-export`                | Crear .pfx desde clave+cert   | `openssl pkcs12 -export -out new.pfx -inkey key.pem -in cert.pem`    |
# Cifrado/descifrado
| **Cifrado / Descifrado** | **Commands**                 | **Ejemplo / Uso típico**                                |      |
| ------------------------ | ---------------------------- | ------------------------------------------------------- | ---- |
| `enc`                    | Cifrado simétrico AES/Base64 | `openssl enc -aes-256-cbc -salt -in file -out file.enc` |      |
| `-d -a`                  | Descifrar Base64             | `openssl enc -d -a -aes-256-cbc -in file.enc`           |      |
| `-pbkdf2`                | PBKDF2 (más seguro)          | `-pbkdf2 -iter 100000`                                  | **** |
|                          |                              |                                                         |      |
|                          |                              |                                                         |      |
# Hashing y passwords
| **Hashing & Passwords** | **Commands**               | **Ejemplo / Uso típico**                                             |
| ----------------------- | -------------------------- | -------------------------------------------------------------------- |
| `dgst`                  | Hash rápido (SHA1/MD5/etc) | `openssl dgst -sha256 malware.exe`                                   |
| `passwd`                | Generar hashes crypt       | `openssl passwd -6 -salt xyz123 password` → SHA-512                  |
| `-1` / `-5` / `-6`      | MD5 / SHA-256 / SHA-512    | `-6`                                                                 |
# Otros
| **Random & Payloads** | **Commands**              | **Ejemplo / Uso típico**                                             |
| --------------------- | ------------------------- | -------------------------------------------------------------------- |
| `rand`                | Generar bytes aleatorios  | `openssl rand -hex 32`                                               |
| `-base64`             | Base64 aleatorio          | `openssl rand -base64 48`                                            |
|                       |                           |                                                                      |
| **Otros útiles**      | **Commands**              | **Ejemplo / Uso típico**                                             |
| `version`             | Versión OpenSSL           | `openssl version`                                                    |
| `speed`               | Benchmark criptográfico   | `openssl speed aes-256-cbc`                                          |
| `ocsp`                | Comprobar revocación OCSP | `openssl ocsp -issuer ca.crt -cert cert.crt -url http://ocsp.ca.com` |
# Cheats
```bash
# Banner + certificado rápido
openssl s_client -connect target:443 -servername domain.com < /dev/null

# Test SSLv3 / TLS 1.0 (downgrade)
openssl s_client -connect target:443 -ssl3

# Extraer clave privada de .pfx sin password prompt
openssl pkcs12 -in cert.pfx -nodes -nocerts -out private.key

# Hash para john/hashcat
openssl passwd -6 -salt SALT123 Pass123
```