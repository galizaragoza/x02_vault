Fingerprinting de páginas web, identifica CMS, frameworks, servidores, versiones, módulos, tecnologías... Se utiliza para ir perfilando el objetivo y explorar posibles vectores de ataque en versiones vulnerables.

```bash
whatweb [opciones] URL|IP|archivo
```

| Parámetro           | Función                                              |
| ------------------- | ---------------------------------------------------- |
| `-u URL`            | URL objetivo                                         |
| `-a N`              | Nivel de agresión (1=stealth, 3=aggressive, 4=heavy) |
| `-p plugins`        | Lista de plugins (e.g. -p jquery,wordpress)          |
| `--log-json=file`   | Salida JSON                                          |
| `--log-xml=file`    | Salida XML                                           |
| `-v`                | Verbose                                              |
| `-q`                | Silencioso (solo resultados)                         |
| `--follow-redirect` | Siempre seguir redirecciones                         |
| `--max-redirects=N` | Límite de redirecciones                              |
| `--colour=never`    | Sin colores                                          |
| `-U="string"`       | User-Agent personalizado                             |
| `--header="X:name"` | Header HTTP personalizado                            |
| `-t N`              | Threads (default 25)                                 |