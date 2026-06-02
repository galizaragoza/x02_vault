#reference #Ciberseguridad #OSINT

**Social Analyzer** = herramienta OSINT para encontrar el perfil de una persona/username en **cientos de redes sociales** y sitios. Usa coincidencia de nombre, detección por OCR/imagen y análisis de presencia. API + CLI + web.

# Instalación (one-liner)

```shell
sudo apt-get update && sudo DEBIAN_FRONTEND=noninteractive apt-get install -y software-properties-common && sudo add-apt-repository ppa:mozillateam/ppa -y && sudo apt-get install -y firefox-esr tesseract-ocr git nodejs npm && git clone https://github.com/qeeqbox/social-analyzer.git && cd social-analyzer && npm update && npm install && npm start
```

> Web en `http://0.0.0.0:9005`. También hay versión Python (`pip install social-analyzer`).

# Uso CLI (Python)

```bash
# Buscar un username en todos los sitios, solo coincidencias detectadas
social-analyzer --username "objetivo" --metadata --top 100 --output pretty

# Filtrar por modo de detección
social-analyzer --username "objetivo" --websites "instagram twitter github"
```

| Flag | Función |
|------|---------|
| `--username` | Username/nombre a buscar. |
| `--websites` | Limitar a sitios concretos. |
| `--metadata` | Extraer metadatos del perfil. |
| `--top N` | Top N sitios por popularidad. |
| `--output` | Formato de salida. |

# En OSINT de personas

Parte del flujo de enumeración de usernames → cruzar con email/imágenes. Complementa a Sherlock, Maigret. Ver [[OSINT_cheatsheet]].

# Recursos
### [[OSINT_cheatsheet]] · [[SEToolkit]]
### [Social Analyzer — repo](https://github.com/qeeqbox/social-analyzer)
