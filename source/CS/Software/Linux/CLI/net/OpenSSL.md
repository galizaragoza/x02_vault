Herramienta criptográfica todo-en-uno: TLS/SSL, generación/claves/certificados, hashing, cifrado simétrico/asimétrico.

```
openssl comando [opciones]
```

# SSL/TLS — s_client
| **s_client**             | **Descripción**                     | **Ejemplo / Uso típico**                                                    |
| ------------------------ | ----------------------------------- | --------------------------------------------------------------------------- |
| `s_client`               | Conexión TLS/SSL manual             | `openssl s_client -connect target:443`                                      |
| `-connect`               | Host:puerto                         | `-connect target:443`                                                       |
| `-servername`            | SNI                                 | `-servername domain.com`                                                    |
| `-tls1_3`                | Solo TLS 1.3                        | `-tls1_3`                                                                   |
| `-tls1_2`                | Solo TLS 1.2                        | `-tls1_2`                                                                   |
| `-tls1_1` / `-tls1`      | TLS 1.1 / TLS 1.0                   | `-tls1`                                                                     |
| `-no_tls1_3`             | Deshabilitar TLS 1.3                | `-no_tls1_3`                                                                |
| `-ssl3`                  | Solo SSLv3 (muy legacy)             | `-ssl3`                                                                     |
| `-quiet` / `-crlf`       | Silencioso + CRLF                   | `-quiet`                                                                    |
| `< /dev/null`            | Banner rápido sin espera            | `openssl s_client -connect 10.10.10.10:443 -quiet < /dev/null`              |
| `-showcerts`             | Mostrar toda la cadena              | `-showcerts`                                                                |
| `-tlsextdebug`           | Debug extensiones TLS               | `-tlsextdebug`                                                              |
| `-CAfile`                | Verificar con CA propia             | `-CAfile ca.pem`                                                            |
| `-CApath`                | Directorio de CAs                   | `-CApath /etc/ssl/certs`                                                    |
| `-cert` / `-key`         | Autenticación cliente mTLS          | `-cert client.crt -key client.key`                                          |
| `-starttls`              | STARTTLS (smtp/ftp/imap/pop3/xmpp)  | `-starttls smtp`                                                            |
| `-reconnect`             | Reutilizar sesión TLS               | `-reconnect`                                                                |
| `-debug`                 | Dump hexadecimal del handshake      | `-debug`                                                                    |
| `-msg`                   | Mostrar mensajes TLS raw            | `-msg`                                                                      |
| `-state`                 | Mostrar estados del handshake       | `-state`                                                                    |
| `-cipher`                | Forzar cipher suite TLS≤1.2         | `-cipher AES128-SHA`                                                        |
| `-ciphersuites`          | Forzar cipher suite TLS 1.3         | `-ciphersuites TLS_AES_128_GCM_SHA256`                                      |
| `-alpn`                  | ALPN protocol negotiation           | `-alpn h2,http/1.1`                                                         |
| `-nextprotoneg`          | NPN protocol negotiation            | `-nextprotoneg h2`                                                          |
| `-sess_out` / `-sess_in` | Guardar/cargar sesión TLS           | `-sess_out session.pem`                                                     |
| `-verify_return_error`   | Falla si certificado inválido       | `-verify_return_error`                                                      |
| `-verify`                | Profundidad de verificación         | `-verify 4`                                                                 |
| `-proxy`                 | Conectar vía HTTP proxy             | `-proxy proxy:8080`                                                         |
| `-4` / `-6`              | Forzar IPv4 / IPv6                  | `-4`                                                                        |

# SSL/TLS — s_server
| **s_server**           | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `s_server`             | Servidor TLS/SSL manual         | `openssl s_server -key key.pem -cert cert.pem -port 4433`            |
| `-accept`              | Puerto de escucha               | `-accept 4433`                                                       |
| `-cert` / `-key`       | Certificado y clave             | `-cert cert.pem -key key.pem`                                        |
| `-www`                 | Servir respuesta HTTP simple    | `-www`                                                               |
| `-WWW`                 | Servir archivos del directorio  | `-WWW`                                                               |
| `-HTTP`                | Responder peticiones HTTP raw   | `-HTTP`                                                              |
| `-Verify`              | Requerir cert cliente           | `-Verify 1`                                                          |
| `-CAfile`              | CA para verificar clientes      | `-CAfile ca.pem`                                                     |
| `-tls1_2`              | Forzar versión TLS              | `-tls1_2`                                                            |
| `-cipher`              | Forzar cipher suite             | `-cipher AES256-SHA`                                                 |
| `-no_dhe`              | Deshabilitar DHE                | `-no_dhe`                                                            |
| `-naccept`             | Aceptar N conexiones y salir    | `-naccept 1`                                                         |

# SSL/TLS — s_time
| **s_time**             | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `s_time`               | Benchmark de conexiones TLS     | `openssl s_time -connect target:443 -time 30`                        |
| `-connect`             | Host:puerto                     | `-connect target:443`                                                |
| `-time`                | Duración en segundos            | `-time 10`                                                           |
| `-new`                 | Solo sesiones nuevas            | `-new`                                                               |
| `-reuse`               | Solo reutilizar sesiones        | `-reuse`                                                             |
| `-cipher`              | Forzar cipher                   | `-cipher AES128-SHA`                                                 |

# SSL/TLS — sess_id
| **sess_id**            | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `sess_id`             | Gestionar sesiones TLS          | `openssl sess_id -in session.pem -text -noout`                       |
| `-text`               | Ver detalles de sesión          | `-text`                                                              |
| `-noout`              | No imprimir PEM                 | `-noout`                                                             |

# Certificados — x509
| **x509**               | **Descripción**                 | **Ejemplo / Uso típico**                                                   |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------------- |
| `x509`                 | Ver/manipular certificado       | `openssl x509 -in cert.crt -text -noout`                                   |
| `-text`                | Texto completo                  | `-text -noout`                                                             |
| `-dates`               | Solo fechas                     | `-dates`                                                                   |
| `-subject`             | Subject DN                      | `-subject -noout`                                                          |
| `-issuer`              | Issuer DN                       | `-issuer -noout`                                                           |
| `-fingerprint`         | Fingerprint SHA1/SHA256         | `-fingerprint -sha256 -noout`                                              |
| `-pubkey`              | Extraer clave pública           | `-pubkey -noout`                                                           |
| `-serial`              | Número de serie                 | `-serial -noout`                                                           |
| `-checkend`            | ¿Expira en N segundos?          | `-checkend 86400` (expira en 24h → exit 1)                                 |
| `-req`                 | Firmar CSR directamente         | `openssl x509 -req -in req.csr -signkey key.pem -out cert.crt -days 365`  |
| `-CA` / `-CAkey`       | Firmar con CA                   | `-CA ca.crt -CAkey ca.key -CAcreateserial`                                 |
| `-days`                | Días de validez                 | `-days 365`                                                                |
| `-inform` / `-outform` | Formato PEM/DER                 | `-inform DER -outform PEM`                                                 |
| `-extensions`          | Ver extensiones X.509v3         | `-extensions`                                                              |
| `-extfile`             | Archivo de extensiones          | `-extfile san.cnf -extensions v3_req`                                      |
| `-sha256`              | Algoritmo firma                 | `-sha256`                                                                  |

# Certificados — req (CSR)
| **req**                  | **Descripción**                 | **Ejemplo / Uso típico**                                                      |
| ------------------------ | ------------------------------- | ----------------------------------------------------------------------------- |
| `req`                    | Ver CSR                         | `openssl req -in request.csr -text -noout`                                    |
| `-new`                   | Crear CSR                       | `openssl req -new -key priv.key -out new.csr -subj "/CN=target"`              |
| `-newkey rsa:N`          | Clave RSA + CSR en un paso      | `openssl req -newkey rsa:2048 -nodes -keyout key.pem -out req.csr`            |
| `-newkey ec`             | Clave EC + CSR                  | `openssl req -newkey ec -pkeyopt ec_paramgen_curve:P-256 -nodes -keyout ec.key -out ec.csr` |
| `-x509`                  | Cert autofirmado directo        | `openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes` |
| `-key`                   | Clave privada a usar            | `-key priv.key`                                                               |
| `-subj`                  | Subject DN en línea             | `-subj "/C=ES/O=Org/CN=domain.com"`                                           |
| `-nodes`                 | Sin cifrar clave privada        | `-nodes`                                                                      |
| `-verify`                | Verificar CSR                   | `openssl req -verify -in req.csr`                                             |
| `-addext`                | Añadir extensión X.509v3        | `-addext "subjectAltName=DNS:domain.com,IP:1.2.3.4"`                          |
| `-config`                | Archivo de configuración        | `-config openssl.cnf`                                                         |

# Certificados — verify
| **verify**             | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `verify`               | Verificar cadena de certificados | `openssl verify -CAfile ca.crt cert.crt`                            |
| `-CAfile`              | CA raíz                         | `-CAfile ca.pem`                                                     |
| `-CApath`              | Directorio de CAs               | `-CApath /etc/ssl/certs`                                             |
| `-untrusted`           | Certificados intermedios        | `-untrusted chain.pem`                                               |
| `-partial_chain`       | Aceptar cadena parcial          | `-partial_chain`                                                     |
| `-crl_check`           | Verificar CRL                   | `-crl_check -CRLfile crl.pem`                                        |
| `-purpose`             | Comprobar uso extendido (EKU)   | `-purpose sslserver`                                                 |
| `-x509_strict`         | Modo estricto RFC 5280          | `-x509_strict`                                                       |

# Claves — RSA
| **RSA**                  | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------- |
| `genrsa`                 | Generar clave RSA (legacy)      | `openssl genrsa -out priv.key 4096`                                  |
| `genrsa -aes256`         | Clave RSA cifrada               | `openssl genrsa -aes256 -out priv.key 4096`                          |
| `rsa`                    | Convertir/ver clave RSA         | `openssl rsa -in priv.pem -out rsa.key`                              |
| `-text`                  | Ver parámetros RSA              | `openssl rsa -in priv.key -text -noout`                              |
| `-pubout`                | Extraer clave pública           | `openssl rsa -in priv.key -pubout -out pub.key`                      |
| `-check`                 | Verificar integridad clave      | `openssl rsa -in priv.key -check`                                    |
| `-inform` / `-outform`   | Formato PEM/DER                 | `-inform DER -outform PEM`                                           |
| `-passin` / `-passout`   | Contraseña entrada/salida       | `-passin pass:secret`                                                |

# Claves — modernas (pkey / genpkey)
| **pkey / genpkey**       | **Descripción**                 | **Ejemplo / Uso típico**                                                         |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------------------- |
| `genpkey`                | Generar clave privada moderna   | `openssl genpkey -algorithm ed25519 -out priv.key`                               |
| `-algorithm`             | Algoritmo: RSA/EC/Ed25519/X25519 | `-algorithm ed25519`                                                            |
| `-pkeyopt`               | Opciones del algoritmo          | `-pkeyopt rsa_keygen_bits:4096`                                                  |
| `-aes256`                | Cifrar clave generada           | `-aes256`                                                                        |
| `pkey`                   | Gestión clave moderna           | `openssl pkey -in priv.key -text -noout`                                         |
| `-pubout`                | Extraer clave pública           | `openssl pkey -in priv.key -pubout -out pub.key`                                 |
| `-check`                 | Verificar clave                 | `openssl pkey -in priv.key -check`                                               |
| `-pubin`                 | Input es clave pública          | `openssl pkey -pubin -in pub.key -text -noout`                                   |
| `pkeyparam`              | Ver parámetros de clave         | `openssl pkeyparam -in ecparam.pem -text`                                        |
| `pkeyutl -sign`          | Firmar datos                    | `openssl pkeyutl -sign -in data -inkey priv.key -out sig`                        |
| `pkeyutl -verify`        | Verificar firma                 | `openssl pkeyutl -verify -in data -sigfile sig -inkey pub.key -pubin`            |
| `pkeyutl -encrypt`       | Cifrar con clave pública        | `openssl pkeyutl -encrypt -in plain -pubin -inkey pub.key -out enc`              |
| `pkeyutl -decrypt`       | Descifrar con clave privada     | `openssl pkeyutl -decrypt -in enc -inkey priv.key -out plain`                    |
| `pkeyutl -derive`        | Derivar clave compartida (ECDH) | `openssl pkeyutl -derive -inkey priv.key -peerkey peer_pub.key -out shared`      |
| `rsautl`                 | Operaciones RSA (legacy)        | `openssl rsautl -sign -in hash -inkey priv.key -out sig`                         |

# Claves — Curva Elíptica (EC)
| **EC**                   | **Descripción**                 | **Ejemplo / Uso típico**                                                              |
| ------------------------ | ------------------------------- | ------------------------------------------------------------------------------------- |
| `ecparam`                | Parámetros de curva EC          | `openssl ecparam -name prime256v1 -genkey -out ec.key`                                |
| `-list_curves`           | Listar curvas disponibles       | `openssl ecparam -list_curves`                                                        |
| `-name`                  | Seleccionar curva               | `-name secp384r1` / `prime256v1` / `secp521r1`                                        |
| `-genkey`                | Generar clave EC inline         | `-genkey -noout -out ec.key`                                                          |
| `-param_enc`             | Codificación de parámetros      | `-param_enc named_curve`                                                              |
| `ec`                     | Gestionar clave EC              | `openssl ec -in ec.key -text -noout`                                                  |
| `-pubout`                | Extraer clave pública EC        | `openssl ec -in ec.key -pubout -out ec_pub.key`                                       |
| `-check`                 | Verificar clave EC              | `openssl ec -in ec.key -check`                                                        |
| `genpkey` EC             | Generar EC moderna              | `openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-256 -out ec.key`          |
| `genpkey` Ed25519        | Generar Ed25519                 | `openssl genpkey -algorithm ed25519 -out ed.key`                                      |
| `genpkey` X25519         | Generar X25519 (ECDH)           | `openssl genpkey -algorithm x25519 -out x25519.key`                                   |

# Claves — DSA / DH
| **DSA / DH**             | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------- |
| `dsaparam`               | Generar parámetros DSA          | `openssl dsaparam -out dsaparam.pem 2048`                            |
| `dsaparam -genkey`       | Parámetros + clave DSA          | `openssl dsaparam -genkey 2048 -out dsa.key`                         |
| `gendsa`                 | Generar clave DSA desde params  | `openssl gendsa -out dsa.key dsaparam.pem`                           |
| `dsa`                    | Gestionar clave DSA             | `openssl dsa -in dsa.key -text -noout`                               |
| `dsa -pubout`            | Extraer clave pública DSA       | `openssl dsa -in dsa.key -pubout -out dsa_pub.key`                   |
| `dhparam`                | Generar parámetros DH           | `openssl dhparam -out dh4096.pem 4096`                               |
| `dhparam -check`         | Verificar parámetros DH         | `openssl dhparam -check -in dh.pem`                                  |
| `dhparam -text`          | Ver parámetros DH               | `openssl dhparam -text -in dh.pem`                                   |

# Claves — Formatos PKCS
| **PKCS**                 | **Descripción**                 | **Ejemplo / Uso típico**                                                   |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------------- |
| `pkcs8`                  | Convertir a PKCS#8              | `openssl pkcs8 -topk8 -in priv.key -out pkcs8.key -nocrypt`               |
| `pkcs8 -topk8`           | RSA → PKCS#8 cifrado            | `openssl pkcs8 -topk8 -in priv.key -out pkcs8.key -v2 aes-256-cbc`        |
| `pkcs8` (decode)         | PKCS#8 → formato tradicional    | `openssl pkcs8 -in pkcs8.key -out priv.key`                                |
| `pkcs12`                 | Extraer de .pfx/.p12            | `openssl pkcs12 -in cert.pfx -nodes -out key+cert.pem`                     |
| `pkcs12 -export`         | Crear .pfx desde clave+cert     | `openssl pkcs12 -export -out new.pfx -inkey key.pem -in cert.pem`          |
| `-nocerts`               | Solo extraer clave              | `openssl pkcs12 -in cert.pfx -nodes -nocerts -out private.key`             |
| `-nokeys`                | Solo extraer certs              | `openssl pkcs12 -in cert.pfx -nokeys -out certs.pem`                       |
| `-cacerts`               | Solo CAs                        | `openssl pkcs12 -in cert.pfx -cacerts -nokeys -out ca-chain.pem`           |
| `-clcerts`               | Solo cert cliente               | `openssl pkcs12 -in cert.pfx -clcerts -nokeys -out client.pem`             |
| `-legacy`                | Compatibilidad OpenSSL 1.x pfx  | `openssl pkcs12 -legacy -in old.pfx -nodes -out out.pem`                   |

# PKCS#7 / CMS
| **PKCS7 / CMS**          | **Descripción**                 | **Ejemplo / Uso típico**                                                        |
| ------------------------ | ------------------------------- | ------------------------------------------------------------------------------- |
| `pkcs7`                  | Ver PKCS#7                      | `openssl pkcs7 -in bundle.p7b -text -noout`                                     |
| `pkcs7 -print_certs`     | Extraer certs de PKCS#7         | `openssl pkcs7 -in bundle.p7b -print_certs -out certs.pem`                      |
| `pkcs7 -inform DER`      | Leer DER binario                | `openssl pkcs7 -inform DER -in bundle.p7b -print_certs -out certs.pem`          |
| `crl2pkcs7`              | Certs → PKCS#7 bundle           | `openssl crl2pkcs7 -nocrl -certfile cert.pem -out bundle.p7b`                   |
| `cms -sign`              | Firmar mensaje CMS              | `openssl cms -sign -in msg.txt -signer cert.pem -inkey key.pem -out signed.msg` |
| `cms -verify`            | Verificar mensaje CMS           | `openssl cms -verify -in signed.msg -CAfile ca.pem -out msg.txt`                |
| `cms -encrypt`           | Cifrar con CMS                  | `openssl cms -encrypt -in msg.txt -aes256 -recip cert.pem -out enc.msg`         |
| `cms -decrypt`           | Descifrar CMS                   | `openssl cms -decrypt -in enc.msg -recip cert.pem -inkey key.pem`               |
| `smime -sign`            | Firmar S/MIME (legacy)          | `openssl smime -sign -in msg.txt -signer cert.pem -inkey key.pem -out signed.eml` |
| `smime -verify`          | Verificar S/MIME                | `openssl smime -verify -in signed.eml -CAfile ca.pem`                           |
| `smime -encrypt`         | Cifrar S/MIME                   | `openssl smime -encrypt -aes256 -in msg.txt -out enc.eml cert.pem`              |

# CRL (Lista de Revocación)
| **CRL**                  | **Descripción**                 | **Ejemplo / Uso típico**                                              |
| ------------------------ | ------------------------------- | --------------------------------------------------------------------- |
| `crl`                    | Ver CRL                         | `openssl crl -in crl.pem -text -noout`                                |
| `-inform DER`            | Leer CRL binario                | `openssl crl -in crl.der -inform DER -outform PEM -out crl.pem`       |
| `-CAfile`                | Verificar CRL con CA            | `openssl crl -in crl.pem -CAfile ca.crt -noout`                       |
| `-nextupdate`            | Próxima fecha de actualización  | `openssl crl -in crl.pem -nextupdate -noout`                          |

# CA (Autoridad Certificadora)
| **CA**                   | **Descripción**                 | **Ejemplo / Uso típico**                                              |
| ------------------------ | ------------------------------- | --------------------------------------------------------------------- |
| `ca`                     | Firmar CSR como CA              | `openssl ca -in req.csr -out cert.crt -config openssl.cnf`            |
| `-config`                | Archivo de configuración        | `-config /etc/ssl/openssl.cnf`                                        |
| `-days`                  | Días de validez                 | `-days 365`                                                           |
| `-revoke`                | Revocar certificado             | `openssl ca -revoke cert.crt -config openssl.cnf`                     |
| `-crl_reason`            | Motivo de revocación            | `-crl_reason keyCompromise`                                           |
| `-gencrl`                | Generar CRL                     | `openssl ca -gencrl -out crl.pem -config openssl.cnf`                 |
| `-batch`                 | No pedir confirmación           | `-batch`                                                              |
| `-extensions`            | Usar sección de extensiones     | `-extensions server_cert`                                             |
| `-subj`                  | Override de Subject             | `-subj "/CN=server"`                                                  |

# Cifrado/descifrado — enc
| **enc**                  | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------- |
| `enc`                    | Cifrado simétrico               | `openssl enc -aes-256-cbc -salt -in file -out file.enc`              |
| `-d`                     | Descifrar                       | `openssl enc -d -aes-256-cbc -in file.enc`                           |
| `-a` / `-base64`         | Encoding Base64                 | `-a` (codificar output en base64)                                    |
| `-pbkdf2`                | PBKDF2 KDF (recomendado)        | `-pbkdf2 -iter 100000`                                               |
| `-k`                     | Contraseña en línea             | `-k "mypassword"`                                                    |
| `-kfile`                 | Contraseña desde archivo        | `-kfile pass.txt`                                                    |
| `-K` / `-iv`             | Clave/IV en hexadecimal         | `-K AABB... -iv 0011...`                                             |
| `-nosalt`                | Sin salt (inseguro)             | `-nosalt`                                                            |
| `-list`                  | Listar cifrados disponibles     | `openssl enc -list`                                                  |
| `-in` / `-out`           | Archivos de entrada/salida      | `-in plain.txt -out enc.bin`                                         |
| `-pass`                  | Fuente de contraseña            | `-pass env:MY_PASS`                                                  |
| Solo Base64              | Codificar/decodificar Base64    | `openssl enc -base64 -in file -out file.b64`                         |

# Hashing — dgst
| **dgst**                | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ----------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `dgst`                  | Hash de archivo                 | `openssl dgst -sha256 file.bin`                                      |
| `-sha1` / `-sha256` / `-sha512` | Algoritmo hash          | `openssl dgst -sha512 file`                                          |
| `-sha3-256` / `-sha3-512` | SHA-3                         | `openssl dgst -sha3-256 file`                                        |
| `-md5`                  | MD5 (legacy, evitar)            | `openssl dgst -md5 file`                                             |
| `-blake2b512`           | BLAKE2b                         | `openssl dgst -blake2b512 file`                                      |
| `-sign`                 | Firmar hash con clave privada   | `openssl dgst -sha256 -sign priv.key -out sig file`                  |
| `-verify`               | Verificar firma                 | `openssl dgst -sha256 -verify pub.key -signature sig file`           |
| `-hmac`                 | HMAC                            | `openssl dgst -sha256 -hmac "secret" file`                           |
| `-binary`               | Output binario                  | `-binary`                                                            |
| `-hex`                  | Output hexadecimal (default)    | `-hex`                                                               |
| `-c`                    | Output con separadores (:)      | `-c`                                                                 |

# MAC (Message Authentication Code)
| **mac**                  | **Descripción**                 | **Ejemplo / Uso típico**                                                      |
| ------------------------ | ------------------------------- | ----------------------------------------------------------------------------- |
| `mac`                    | Calcular MAC genérico           | `openssl mac -digest SHA256 -macopt key:secret HMAC < file`                   |
| `HMAC`                   | HMAC con cualquier digest       | `openssl mac -digest SHA256 -macopt hexkey:AABBCC HMAC < file`                |
| `CMAC`                   | CMAC (AES-based)                | `openssl mac -cipher AES-128-CBC -macopt hexkey:AABB... CMAC < file`          |
| `GMAC`                   | GMAC (GCM-based)                | `openssl mac -cipher AES-128-GCM -macopt hexkey:... GMAC < file`              |

# KDF (Key Derivation Functions)
| **kdf**                  | **Descripción**                 | **Ejemplo / Uso típico**                                                       |
| ------------------------ | ------------------------------- | ------------------------------------------------------------------------------ |
| `kdf`                    | Derivar clave                   | `openssl kdf -keylen 32 -kdfopt digest:SHA256 -kdfopt pass:secret PBKDF2`     |
| `PBKDF2`                 | PBKDF2                          | `openssl kdf -keylen 32 -kdfopt digest:SHA256 -kdfopt pass:p -kdfopt salt:s -kdfopt iter:100000 PBKDF2` |
| `HKDF`                   | HKDF (TLS 1.3)                  | `openssl kdf -keylen 32 -kdfopt digest:SHA256 -kdfopt key:secret HKDF`        |
| `SCRYPT`                 | scrypt                          | `openssl kdf -keylen 32 -kdfopt pass:p -kdfopt salt:s SCRYPT`                 |
| `TLS1-PRF`               | TLS PRF                         | `openssl kdf -keylen 16 -kdfopt digest:SHA256 -kdfopt secret:s TLS1-PRF`      |

# Passwords
| **passwd**              | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ----------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `passwd`                | Generar hashes crypt            | `openssl passwd -6 -salt xyz123 password`                            |
| `-1`                    | MD5-crypt (Linux legacy)        | `openssl passwd -1 password`                                         |
| `-5`                    | SHA-256-crypt                   | `openssl passwd -5 password`                                         |
| `-6`                    | SHA-512-crypt (recomendado)     | `openssl passwd -6 -salt SALT123 Pass123`                            |
| `-apr1`                 | APR1 (Apache htpasswd)          | `openssl passwd -apr1 password`                                      |
| `-salt`                 | Salt manual                     | `-salt xyz123`                                                       |
| `-stdin`                | Leer password de stdin          | `echo -n "pass" \| openssl passwd -6 -stdin`                        |
| `-table`                | Mostrar password + hash         | `-table`                                                             |

# Random
| **rand**              | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| --------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `rand`                | Generar bytes aleatorios        | `openssl rand -hex 32`                                               |
| `-hex`                | Output hexadecimal              | `openssl rand -hex 16`                                               |
| `-base64`             | Output Base64                   | `openssl rand -base64 48`                                            |
| `-out`                | Guardar a archivo binario       | `openssl rand -out key.bin 32`                                       |

# Timestamping (RFC 3161)
| **ts**                   | **Descripción**                 | **Ejemplo / Uso típico**                                                         |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------------------- |
| `ts -query`              | Crear petición de timestamp     | `openssl ts -query -data file.txt -sha256 -out req.tsq`                          |
| `ts -reply`              | Responder petición (TSA)        | `openssl ts -reply -queryfile req.tsq -signer tsa.crt -inkey tsa.key -out resp.tsr` |
| `ts -verify`             | Verificar timestamp             | `openssl ts -verify -in resp.tsr -queryfile req.tsq -CAfile ca.pem`              |
| `-no_nonce`              | Sin nonce en petición           | `-no_nonce`                                                                      |
| `-cert`                  | Incluir cert TSA en respuesta   | `-cert`                                                                          |

# ASN.1
| **asn1parse**            | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------- |
| `asn1parse`              | Parsear estructura ASN.1        | `openssl asn1parse -in cert.pem`                                     |
| `-inform DER`            | Parsear DER binario             | `openssl asn1parse -inform DER -in cert.der`                         |
| `-strparse`              | Parsear offset específico       | `openssl asn1parse -strparse 4 -in cert.pem`                         |
| `-genstr`                | Generar objeto ASN.1            | `openssl asn1parse -genstr 'UTF8:Hello'`                             |
| `-length`                | Limitar bytes a parsear         | `-length 128`                                                        |
| `-offset`                | Empezar desde offset            | `-offset 32`                                                         |

# SPKAC / nseq
| **Otros formatos**       | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------- |
| `spkac`                  | SPKAC (Netscape signed pubkey)  | `openssl spkac -in spkac.txt -pubkey -noout`                         |
| `spkac -key`             | Generar SPKAC desde clave       | `openssl spkac -key priv.key -out spkac.txt`                         |
| `nseq`                   | Netscape cert sequence          | `openssl nseq -in nseq.pem -toseq`                                   |
| `storeutl`               | Acceder a URIs de store         | `openssl storeutl -noout -text cert.pem`                             |

# OCSP
| **ocsp**                 | **Descripción**                 | **Ejemplo / Uso típico**                                                         |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------------------- |
| `ocsp`                   | Comprobar revocación OCSP       | `openssl ocsp -issuer ca.crt -cert cert.crt -url http://ocsp.ca.com`             |
| `-issuer`                | Certificado del emisor          | `-issuer ca.crt`                                                                 |
| `-cert`                  | Certificado a comprobar         | `-cert server.crt`                                                               |
| `-url`                   | URL del responder OCSP          | `-url http://ocsp.ca.com`                                                        |
| `-resp_text`             | Ver respuesta completa          | `-resp_text`                                                                     |
| `-noverify`              | No verificar respuesta          | `-noverify`                                                                      |
| `-respout`               | Guardar respuesta               | `-respout resp.der`                                                              |

# Diagnóstico y utilidades
| **Utilidades**         | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `version`              | Versión OpenSSL                 | `openssl version -a`                                                 |
| `speed`                | Benchmark criptográfico         | `openssl speed aes-256-cbc`                                          |
| `speed -evp`           | Benchmark EVP                   | `openssl speed -evp aes-256-gcm`                                     |
| `ciphers`              | Listar cipher suites TLS        | `openssl ciphers -v 'ALL'`                                           |
| `ciphers -tls1_2`      | Solo suites TLS 1.2             | `openssl ciphers -tls1_2 -v`                                         |
| `ciphers -V`           | Con ID hexadecimal              | `openssl ciphers -V 'HIGH'`                                          |
| `prime`                | Verificar número primo          | `openssl prime 17`                                                   |
| `prime -bits`          | Generar primo aleatorio         | `openssl prime -bits 256 -generate`                                  |
| `prime -hex`           | Input/output hex                | `openssl prime -hex FF`                                              |
| `errstr`               | Texto de código de error        | `openssl errstr 0x02001002`                                          |
| `rehash`               | Rehash directorio de CAs        | `openssl rehash /etc/ssl/certs`                                      |
| `info`                 | Info de compilación             | `openssl info -configdir`                                            |

# list — Introspección
| **list**               | **Descripción**                 | **Ejemplo / Uso típico**                                             |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------- |
| `list -commands`       | Todos los comandos              | `openssl list -commands`                                             |
| `list -digest-algorithms` | Algoritmos de hash           | `openssl list -digest-algorithms`                                    |
| `list -cipher-algorithms` | Cifrados simétricos          | `openssl list -cipher-algorithms`                                    |
| `list -public-key-algorithms` | Algoritmos de clave pública | `openssl list -public-key-algorithms`                           |
| `list -key-managers`   | Key managers disponibles        | `openssl list -key-managers`                                         |
| `list -providers`      | Providers cargados              | `openssl list -providers`                                            |
| `list -mac-algorithms` | Algoritmos MAC                  | `openssl list -mac-algorithms`                                       |
| `list -kdf-algorithms` | KDFs disponibles                | `openssl list -kdf-algorithms`                                       |

# Providers (OpenSSL 3.x)
| **Provider**   | **Descripción**                                      | **Activar**                          |
| -------------- | ---------------------------------------------------- | ------------------------------------ |
| `default`      | Algoritmos modernos (activo por defecto)             | `-provider default`                  |
| `legacy`       | MD5, DES, RC4, IDEA, Blowfish (desactivado)          | `-provider legacy -provider default` |
| `fips`         | Módulo FIPS 140-2/3                                  | `-provider fips`                     |
| `base`         | Solo codecs, sin crypto                              | `-provider base`                     |

# Formatos (-inform / -outform)
| **Formato** | **Descripción**                          | **Uso típico**                            |
| ----------- | ---------------------------------------- | ----------------------------------------- |
| `PEM`       | Base64 con cabecera `-----BEGIN...-----` | Default en la mayoría de comandos         |
| `DER`       | Binario ASN.1                            | Interop con sistemas Java/.NET            |
| `PKCS12`    | Contenedor .pfx/.p12                     | Windows, Java KeyStore                    |
| `SMIME`     | Formato S/MIME                           | Email firmado/cifrado                     |
| `ENGINE`    | Motor criptográfico externo (HSM, TPM)   | `-engine pkcs11`                          |

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

# Cert autofirmado rápido (dev) con SAN
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes \
  -subj "/CN=localhost" -addext "subjectAltName=DNS:localhost,IP:127.0.0.1"

# Verificar que clave y cert hacen par (misma modulus)
openssl rsa -modulus -in key.pem -noout | md5sum
openssl x509 -modulus -in cert.pem -noout | md5sum

# Extraer cert de conexión TLS
openssl s_client -connect target:443 -servername domain.com < /dev/null 2>/dev/null \
  | openssl x509 -text -noout

# Ver todos los certs de la cadena
openssl s_client -connect target:443 -showcerts < /dev/null 2>/dev/null \
  | grep -E "^(subject|issuer)"

# Convertir DER → PEM
openssl x509 -in cert.der -inform DER -out cert.pem

# Generar clave EC P-256 + CSR
openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-256 -out ec.key
openssl req -new -key ec.key -out ec.csr -subj "/CN=target"

# HMAC-SHA256
openssl dgst -sha256 -hmac "secret_key" file.bin

# Servidor TLS rápido para testing
openssl s_server -key key.pem -cert cert.pem -accept 4433 -www

# Verificar OCSP usando URL del propio cert
openssl ocsp -issuer chain.pem -cert cert.pem \
  -url "$(openssl x509 -in cert.pem -noout -ocsp_uri)" -resp_text

# Extraer certs de bundle PKCS#7
openssl pkcs7 -in bundle.p7b -print_certs -out certs.pem

# Parsear DER binario (ASN.1 debug)
openssl asn1parse -inform DER -in cert.der

# Activar algoritmos legacy (MD5, DES, RC4...)
openssl enc -provider legacy -provider default -des-cbc -in file -out file.enc

# Benchmark AES-GCM
openssl speed -evp aes-256-gcm

# PBKDF2 manual para derivar clave
openssl kdf -keylen 32 -kdfopt digest:SHA256 -kdfopt pass:mypassword \
  -kdfopt salt:mysalt -kdfopt iter:100000 PBKDF2 | xxd
```
