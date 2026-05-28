
```
wget [opciones] URL
```

| Parámetro             | Función                                      | Ejemplo de sintaxis                                   |
| --------------------- | -------------------------------------------- | ----------------------------------------------------- |
| `-R <LISTA>`          | Reject: "Rechaza" (ignora) 1+ archivos/dirs. | `wget -r -np -R "index.html*" http://target/backups/` |
| `-r`                  | Activa recursión                             |                                                       |
| `-np`                 | No-parent (no sube a dirs superiores)        |                                                       |
| `-l=INT`              | Nivel de recursión (0=infinito)              |                                                       |
| `-m --no-clobber`     | Mirror completo sin sobreescribir            | `wget -m --no-clobber http://target/`                 |
| `--random-wait -w` 1  | Delay aleatorio (evasión)                    | `wget -r --random-wait -w 1 http://target/`           |
| `-U`=(User-Agent)     | Spoof User-Agent                             | `wget -U "Mozilla/5.0" http://target/robots.txt`      |
| `-e` robots=off       | Ignora robots.txt                            | `wget -e robots=off -r http://target/`                |
| `--limit-rate`=(n)    | Limita velocidad (evasión IDS/WAF)           | `wget --limit-rate=100k bigfile.iso`                  |
| `-b -o` log           | Modo background + logfile                    | `wget -b -o download.log http://target/bigfile.zip`   |
| --tries=1 --timeout=5 | Rápido y silencioso (masivo)                 | `wget --tries=1 --timeout=5 -i massive.txt`           |
| `-q`                  | Silencioso total                             | `wget -q http://target/.env`                          |
| `--spider`            | Solo verifica existencia (no descarga)       | `wget --spider http://target/admin.php`               |
