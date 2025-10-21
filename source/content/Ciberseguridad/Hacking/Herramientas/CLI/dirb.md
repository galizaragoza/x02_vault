> Dirb es una herramienta para enumerar directorios y archivos en servidores web mediante ataques de diccionario.
### **Funciones principales**
1. **Escaneo de directorios/archivos**: Identifica recursos no enlazados mediante listas de palabras.
2. **Análisis de respuestas HTTP**: Examina códigos de estado (200, 403, etc.) para detectar contenido.
3. **Personalización**: Permite configurar cabeceras, cookies, proxies y extensiones.
4. **Enumeración recursiva**: Explora subdirectorios opcionalmente.
5. **Automatización**: Agiliza auditorías web en entornos autorizados.
### **Usos comunes en pentesting y ciberseguridad**
- **Enumeración web**: Descubre paneles de administración, backups o archivos sensibles.
- **Mapeo de aplicaciones**: Identifica puntos de entrada para pruebas de seguridad.
- **Complemento de herramientas**: Combina con Burp Suite o Nikto para auditorías completas.
- **Bug bounties**: Busca endpoints ocultos en programas de recompensas.
- **Pruebas de configuración**: Detecta directorios mal protegidos o expuestos..
```bash
dirb <URL_base> [<archivo_lista_palabras>] [opciones]
```

|      |                                                                                      |                                                                                    |
| ---- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| `-a` | Especifica User-Agent personalizado.                                                 | dirb http://target.com -a "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Firefox/90.0" |
| `-c` | Establece una cookie para la solicitud                                               | dirb http://target.com -a "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Firefox/90.0" |
| `-H` | Modifica los headers                                                                 | dirb http://target.com -H "Authorization: Bearer token123"                         |
| `-i` | Modo case insensitive                                                                | dirb http://target.com -i                                                          |
| `-N` | Ignora ciertas respuestas                                                            | dirb http://target.com -N 404                                                      |
| `-o` | Guarda los resultados en un archivo                                                  | dirb http://target.com -o scan_results.txt                                         |
| `-p` | Utiliza un proxy                                                                     | dirb http://target.com -p 127.0.0.1:8080                                           |
| `-r` | Ignora los subdirectorios                                                            | dirb http://target.com -r                                                          |
| `-R` | Activa la recursión interactiva (pide confirmación antes de escanear subdirectorios) | dirb http://target.com -R                                                          |
| `-S` | Activa el modo silencioso                                                            | dirb http://target.com -S                                                          |
| `-X` | Especifica las extensiones a probar                                                  | dirb http://target.com -X .php,.html,.txt                                          |
| `-w` | Elimina las extensiones default al iterar por el diccionario                         | dirb http://target.com -w                                                          |
|      |                                                                                      |                                                                                    |
